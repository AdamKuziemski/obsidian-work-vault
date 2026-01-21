<%*
const pull = await tp.system.prompt("Enter a PR");
if (pull) {
  tR += `[#${pull}](https://github.com/company/repo/pull/${pull})`
}
_%>