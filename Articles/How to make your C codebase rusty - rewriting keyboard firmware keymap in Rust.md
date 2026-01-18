---
tags:
  - topic/programming/rust
  - topic/programming/cpp
  - topic/programming/embedded
  - topic/keyboards
source: https://about.houqp.me/posts/rusty-c/
author: Hou, Qingping
read: false
---
Recently, my colleague introduced me to QMK [1](https://about.houqp.me/posts/rusty-c/#fn:1), an open-source keyboard firmware. Like many non-trivia C projects, macro is heavily used everywhere, especially in keymap files [2](https://about.houqp.me/posts/rusty-c/#fn:2). It didn’t bother me much since I have been programming C for many years. But I kept thinking to myself, “what would it look like if I can start from scratch and redesign everything using Rust’s more powerful macro system?”

After couple nights of hacking, I was able to come up with something I am quite happy with. For a quick preview, here is how the same keymap definition looks like in C and Rust respectively:

```C
// Original QMK C keymap
const uint16_t PROGMEM keymaps[][MATRIX_ROWS][MATRIX_COLS] = {
    [0] = LAYOUT(
        KC_GRV,         KC_1,    KC_2,    KC_3,    KC_4,    KC_5,    KC_6,    KC_7,    KC_8,    KC_9,    KC_0,    KC_MINS, KC_EQL,  KC_BSPC, KC_DEL,  \	
        LT(3, KC_TAB),  KC_Q,    KC_W,    KC_E,    KC_R,    KC_T,    KC_Y,    KC_U,    KC_I,    KC_O,    KC_P,    KC_LBRC, KC_RBRC, KC_BSLS, KC_HOME, \	
        MO(2),          KC_A,    KC_S,    KC_D,    KC_F,    KC_G,    KC_H,    KC_J,    KC_K,    KC_L,    KC_SCLN, KC_QUOT,          KC_ENT,  KC_PGUP, \	
        KC_LSFT,        KC_Z,    KC_X,    KC_C,    KC_V,    KC_B,    KC_N,    KC_M,    KC_COMM, KC_DOT,  KC_SLSH, KC_RSFT,          KC_UP,   KC_PGDN, \	
        KC_LCTL,        KC_LALT, KC_LGUI,                            KC_SPC,                             MO(1),   KC_RGUI, KC_LEFT, KC_DOWN, KC_RGHT  \	
    ),
    [1] = LAYOUT(
        KC_ESC,  KC_F1,   KC_F2,   KC_F3,   KC_F4,   KC_F5,   KC_F6,   KC_F7,   KC_F8,   KC_F9,   KC_F10,  KC_F11,  KC_F12,  _______, KC_MUTE, \	
        L_T_BR,  L_PSD,   L_BRI,   L_PSI,   L_EDG_I, _______, _______, _______, U_T_AGCR,_______, KC_PSCR, KC_SLCK, KC_PAUS, _______, KC_END,  \	
        L_T_PTD, L_PTP,   L_BRD,   L_PTN,   _______, _______, KC_LEFT, KC_DOWN, KC_UP,   KC_RGHT, _______, _______,          _______, KC_VOLU, \	
        _______, L_T_MD,  L_T_ONF, _______, L_EDG_M, MD_BOOT, TG_NKRO, _______, _______, _______, _______, _______,          KC_PGUP, KC_VOLD, \	
        _______, _______, _______,                            DBG_FAC,                            _______, _______, KC_HOME, KC_PGDN, KC_END   \	
    ),
    ...
};
```

```rust
// New keymap in Rust
keymaps!(
    rows => MATRIX_ROWS,
    cols => MATRIX_COLS,
    layer_cnt => 4,
    layer!( // layer 0
        r!(     '`'     | 1  | 2  | 3 | 4 | 5 |  6  | 7 | 8 | 9 |   0   |  -  | =  |BSPC |DEL  ),
        r!( [TAB &LT{3}]| Q  | W  | E | R | T |  Y  | U | I | O |   P   | '[' |']' |BSLS |HOME ),
        r!(    [MO{2}]  | A  | S  | D | F | G |  H  | J | K | L |   ;   |QUOTE|xxxx| '⏎' |PGUP ),
        r!(     LSFT    |xxxx| Z  | X | C | V |  B  | N | M | , |   .   |  /  |RSFT| '↑' |PGDN ),
        r!(     LCTL    |LALT|LGUI|xxx|xxx|xxx|SPACE|xxx|xxx|xxx|[MO{1}]|RGUI |'←' | '↓' | '→' ),
    ),
    layer!( // layer 1
        r!( ESC  |F1 | F2 | F3  | F4  | F5  |  F6   |  F7   |   F8   | F9 |F10 |F11 |F12 |[  ]|'🔇' ),
        r!( T_BR |PSD|BRI | PSI |EDG_I|[   ]| [   ] | [   ] |U_T_AGCR|[  ]|PSCR|SLCK|PAUS|[  ]|END  ),
        r!( T_PTD|PTP|BRD | PTN |[   ]|[   ]|  '←'  |  '↓'  |  '↑'   |'→' |[  ]|[  ]|xxxx|[  ]|'🔊' ),
        r!( [  ] |xxx|T_MD|T_ONF|[   ]|EDG_M|MD_BOOT|TG_NKRO| [    ] |[  ]|[  ]|[  ]|[  ]|PGUP|'🔉' ),
        r!( [  ] |[ ]|[  ]|xxxxx|xxxxx|xxxxx|DBG_FAC|xxxxxxx|xxxxxxxx|xxxx|[  ]|[  ]|HOME|PGDN|END  ),
    ),
    ...
);
```

IMHO, the Rust version is much more succinct and readable. Thanks to Rust’s hygienic macro system, it is free from surprising scoping bugs you might get in C. But most importantly, I can reference keys using Emojis!

In this blog post, I am going to summarize the techniques I used for this Rust rewrite. To keep it simple, I will demonstrate the core concepts with stripped down sample code. The actual working QMK implementation is linked at the end of the post.

## Link C and Rust objects

First thing first, let’s figure out how to produce a single binary from C and Rust source code.

Roughly speaking, in QMK, each C source file is first compiled to an object file using GCC. At the end of the build process, GCC is invoked again to link all the object files into a single binary. This would be our final firmware that can be flashed onto the keyboard.

So the idea here is very straightforward, if we can produce compatible object files from Rust and C codebase, then we can just link them together to assemble the final firmware.

To simulate QMK’s build process, I have created a dummy C source (`keymap.c`) below:

```C
#include <stdint.h>

uint8_t led_animation_id = 0;

const uint16_t keymaps[][2][3] = {
    [0] = {
        {1, 2, 3},
        {4, 5, 6},
    },
};

void matrix_init_user(void) {
    led_animation_id = 1;
}

void matrix_scan_user(void) {
}

int main() {
   matrix_init_user();
   matrix_scan_user();
   return keymaps[0][0][0];
}
```

We can compile it into object file using GCC:

```bash
$ gcc -c -o keymap.o keymap.c
$ file keymap.o
keymap.o: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0
```

Next comes a minimal Rust boilerplate source we will use to replace the above C implementation:

```rust
#![no_std]
#![no_builtins]
#![crate_type = "staticlib"]
#![allow(dead_code)]

#[panic_handler]
fn my_panic(_info: &core::panic::PanicInfo) -> ! {
    loop {}
}
```

Similar to the C source, we can use `rustc` to compile the Rust source into object file:

```bash
$ rustc --emit=obj --codegen panic=abort -o rust_keymap.o keymap.rs
$ file rust_keymap.o
rust_keymap.o: ELF 64-bit LSB relocatable, x86-64, version 1 (SYSV), not stripped
```

Lastly, we make sure two object files can be linked into a single binary using GCC:

```bash
$ gcc -o firmware keymap.o rust_keymap.o
$ ./firmware
$ echo $?
1
```

So far so good, we have now setup a build “infrastructure” that can produce a binary from a codebase mixed with C and Rust. These build steps can also be easily integrated into QMK’s build script. We are all set to rewrite the C source code piece by piece in Rust.

## Implement function callable by C

Let’s start with rewriting the no op `matrix_scan_user()` function in Rust:

```rust
#[no_mangle]
pub extern "C" fn matrix_scan_user() {
}
```

Here, we introduced two new concepts: `#[no_mangle]` attribute and `extern "C"` [3](https://about.houqp.me/posts/rusty-c/#fn:3) keyword.

The `#[no_mangle]` attribute annotation signals the Rust compiler to not change the symbol name for internal optimizations. The `extern "C"` keyword tells the Rust compiler to compile this function as FFI (foreign function interface) following C ABI (calling convention). Both are required to make this function callable from C code.

We can verify the object file created from the new Rust code indeed contains the function symbol we want to export using `nm` command:

```bash
$ nm rust_keymap.o
0000000000000000 T matrix_scan_user
0000000000000000 T rust_begin_unwind
```

Now we can go ahead and replace `matrix_scan_user()` implementation with a declaration in `keymap.c`:

```diff
-void matrix_scan_user(void) {
-}
+void matrix_scan_user(void);
```

Finally, let’s go through the same build process to make sure two object files can be linked into a single binary without issue:

```bash
$ gcc -c -o keymap.o keymap.c
$ rustc --emit=obj --codegen panic=abort -o rust_keymap.o  keymap.rs
$ gcc -o firmware keymap.o rust_keymap.o
$ file firmware
firmware: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0, BuildID[sha1]=52e2f832b83786b3f9668b572fb8d10a4ca20739, not stripped
```

## Read/write C global variables

Accessing C global variables from Rust functions is a common pattern I have encountered while rewriting my QMK keymap in Rust. The `matrix_init_user()` function in `keymap.c` writes to a global integer, which is exactly what we needed to demonstrate this use-case.

This time, let’s start with replacing `matrix_init_user()`’s implementation with a declaration in `keymap.c`:

```diff
-void matrix_init_user(void) {
-    led_animation_id = 1;
-}
+void matrix_init_user(void);
```

As you can tell, this function simply updates the global variable `led_animation_id` to `1`. To rewrite it in Rust, we need to let the compiler know that `led_animation_id` is defined externally in the C codebase using `extern "C"` block:

```rust
extern "C" {
    #[no_mangle]
    static mut led_animation_id: u8;
}

#[no_mangle]
pub extern "C" fn matrix_init_user() {
    unsafe {
        led_animation_id = 1;
    }
}
```

Notice `extern "C"` is used in two different mirroring contexts. One is in the form of external block, which tells Rust compiler that this symbol needs to be imported externally. The other one is in the form of keyword (part of function signature), which tells Rust compiler that this symbol needs to be exported externally.

In `matrix_init_user()`, we need to access `led_animation_id` within an unsafe block for two reasons:

1. Being an extern static variable means it is managed by another compiler that does not enforce Rust’s memory safety guarantees.
2. Being a static mut variable means it is a unsynchronized shared mutable state. As a result, it is impossible for Rust’s type system to guarantee it will be free from data race. [4](https://about.houqp.me/posts/rusty-c/#fn:Shadow0133)

## Declare global variables accessible from C

In QMK, the keyboard keymaps is defined as a 3 dimensional C array. Two dimensions for all the keys on a keyboard and one extra for layers. To finish off our Rust keymap port, we need to move the declaration of this multidimensional array into Rust land and make it accessible from C.

Just like the previous section, we first update `keymap.c` to let the C compiler know that `keymaps` array is declared externally:

```diff
-const uint16_t keymaps[][2][3] = {
-    [0] = {
-        {1, 2, 3},
-        {4, 5, 6},
-    },
-};
+extern uint16_t keymaps[][2][3];
```

Declaring a C accessible variable in Rust is very straight forward using `#[no_mangle]` attribute:

```rust
#[no_mangle]
static keymaps: [[[u16; 3]; 2]; 1] = [
    [
        [1, 2, 3],
        [4, 5, 6],
    ],
];
```

## Cross compiling with Rust

My massdrop alt keyboard [5](https://about.houqp.me/posts/rusty-c/#fn:4) is powered by cortex-m4 processor, which means we need to cross compile the Rust code.

Luckily, cross compiling in Rust is surprisingly easy and can be done in just two steps. Firstly, a specific target needs to be added to rust toolchain using `rustup`:

```bash
$ rustup target add thumbv7em-none-eabihf
```

Once a target has been installed, it can be enabled using `--target` flag when compiling the object file:

```bash
$ cargo rustc --lib --release \
    --target=thumbv7em-none-eabihf \
    -- \
    --emit=obj --codegen panic=abort
```

## Access C headers

Technically, At this point we have everything we need to fully rewrite a QMK keymap in Rust. However, QMK itself has a rather complex codebase that comes with a lot of macro and enum definitions. For example, each key code in QMK is defined as an enum field. For extra fun, the fields for some of the enums will change at GCC preprocessing time based on predefined macros passed in from CFLAGS.

Needless to say, it’s not practical to manually keep all these C constants in sync in Rust. Luckily, we can automate this process using the awesome [bindgen](https://github.com/rust-lang/rust-bindgen) Rust crate.

To ease integration with external build tools, Cargo provides build script [6](https://about.houqp.me/posts/rusty-c/#fn:5) support. By default, it executes `build.rs` in the project root directory before anything else gets compiled.

By leveraging this feature, we can pass QMK CFLAGS to Cargo build command through `BINDGEN_CFLAGS` environment variable. Then within `build.rs`, we parse the QMK CFLAGS and pass all `-D` arguments to `BINDGEN_EXTRA_CLANG_ARGS` environment variable. This environment variable will be read by `bindgen` and all arguments within will be passed to clang command line when generating the Rust binding (`binding.rs`) for the selected C header files:

```rust
let cflags = std::env::var("BINDGEN_CFLAGS").expect("Missing CFLAGS environment variable");
let extra_clang_args = cflags
    .split(" ")
    .filter(|s| s.starts_with("-D"))
    .collect::<Vec<&str>>()
    .join(" ");
// bindgen will pass -D from BINDGEN_EXTRA_CLANG_ARGS to clang
std::env::set_var("BINDGEN_EXTRA_CLANG_ARGS", extra_clang_args);

let bindings = bindgen::builder()
    .header("../quantum/quantum_keycodes.h")
    .header("../tmk_core/common/keycode.h")
    .rustfmt_bindings(true)
    .generate()
    .expect("Unable to generate bindings");

let out_path = PathBuf::from(env::var("OUT_DIR").unwrap());
bindings
    .write_to_file(out_path.join("bindings.rs"))
    .expect("Couldn't write bindings!");
```

See my [Github repo](https://github.com/houqp/qmk_firmware/blob/dfe20d7/rust/build.rs) for what a fully working `build.rs` file looks like.

Finally, in our Rust codebase, we can import all the auto-generated C constants using the include macro:

```rust
include!(concat!(env!("OUT_DIR"), "/bindings.rs"));
```

## Putting it all together

To see how everything fits together, checkout my QMK branch at [https://github.com/houqp/qmk_firmware/tree/massdrop_houqp_rust](https://github.com/houqp/qmk_firmware/tree/massdrop_houqp_rust). All rust code, including the keymap, live under `rust` directory.

After getting all the required Rust and QMK C toolchains installed, you will be able to compile my QMK branch with:

```bash
$ make massdrop/alt:houqp
```

Lastly, in case that’s not obvious to you, this entire blog post is also produced using my Rusty firmware ;)

## Reference

- [https://danielkeep.github.io/tlborm/book/mbe-min-non-identifier-identifiers.html](https://danielkeep.github.io/tlborm/book/mbe-min-non-identifier-identifiers.html)
- [https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html#calling-rust-functions-from-other-languages](https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html#calling-rust-functions-from-other-languages)
- [https://rust-embedded.github.io/book/interoperability/rust-with-c.html](https://rust-embedded.github.io/book/interoperability/rust-with-c.html)
- [https://blog.cloudflare.com/writing-complex-macros-in-rust-reverse-polish-notation/](https://blog.cloudflare.com/writing-complex-macros-in-rust-reverse-polish-notation/)
- [https://github.com/rust-embedded/cross](https://github.com/rust-embedded/cross)

---

1. [https://docs.qmk.fm](https://docs.qmk.fm) [return](https://about.houqp.me/posts/rusty-c/#fnref:1)
2. In QMK, you implement all custom keyboard logic in keymap file. This file contains layered key mappings, which are defined in a global array called keymaps, and other custom keyboard event handling code. [return](https://about.houqp.me/posts/rusty-c/#fnref:2)
3. [https://doc.rust-lang.org/std/keyword.extern.html](https://doc.rust-lang.org/std/keyword.extern.html) [return](https://about.houqp.me/posts/rusty-c/#fnref:3)
4. Thanks to [Shadow0133](https://www.reddit.com/user/Shadow0133/) for the [correction on reason to use unsafe](https://www.reddit.com/r/rust/comments/ds3qr7/how_to_make_your_c_codebase_rusty_rewriting/f6nbegs/). [return](https://about.houqp.me/posts/rusty-c/#fnref:Shadow0133)
5. [https://drop.com/buy/massdrop-alt-mechanical-keyboard](https://drop.com/buy/massdrop-alt-mechanical-keyboard) [return](https://about.houqp.me/posts/rusty-c/#fnref:4)
6. [https://doc.rust-lang.org/cargo/reference/build-scripts.html](https://doc.rust-lang.org/cargo/reference/build-scripts.html) [return](https://about.houqp.me/posts/rusty-c/#fnref:5)