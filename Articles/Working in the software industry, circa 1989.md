---
tags:
  - topic/programming/philosophy
source: https://dev.jimgrey.net/2022/07/05/working-in-the-software-industry-circa-1989/
author: Jim Grey
read: false
---
Indulge me today as I tell a story. It’s a long one, so strap in. 

My 33rd anniversary in the software industry was the Sunday that just passed, July 3. I remember the date because my second day on the job was a paid holiday!

I want to show you just how far our industry has come and how much we’ve learned.

[![](https://softwaresaltmines.files.wordpress.com/2022/07/acd.png?w=435)](https://softwaresaltmines.files.wordpress.com/2022/07/acd.png)

The logo of the first software company I worked for — it’s a low-res copy because the company has been gone since 1996 and this is all I could find on the Internet.

The first software company I worked for was Applied Computing Devices in Terre Haute, Indiana. That place was filled with wicked smart people, and it was a joy to work with them. We made software that managed telephone networks. Our customers were AT&T; US Sprint (now called just Sprint); [GTE](https://en.wikipedia.org/wiki/GTE) (now Verizon); and several [Regional Bell Operating Companies](https://en.wikipedia.org/wiki/Regional_Bell_Operating_Company) (landline phone companies) including Ameritech (which served Indiana), BellSouth, and Pacific Bell. These companies provided primarily landline (“wireline” in the biz) telephone service then but some were beginning to branch into mobile service. This was complicated stuff to manage, and our software products were correspondingly complex. I needed my 400-level college mathematics classes to understand some elements of our software.

I wasn’t a software engineer – I was a technical writer. I wrote huge paper manuals we shipped to customers that explained to them how to install, configure, and use our software. The software industry was very small then, and as I graduated with my Math/CS degree the economy was in a downturn. I wasn’t getting many interviews for coding jobs and the interviews I did get didn’t lead to offers. 

I lived on campus that summer while I looked for work, but the summer would soon end and I’d have to go home and live with Dad. I had zero interest in doing that, so I decided to pivot to just getting _any_ job at a software company – QA, support, IT, janitor, _anything_. I’d figure out my way from there. 

A professor at my school knew people at ACD and introduced me. They needed a technical writer. I said I’d happily do the job, and they hired me at $23,000 a year (about $54,000 today). To my astonishment I discovered that I _loved_ to write, even more than I enjoyed writing code. I became very good at explaining that deeply technical software to users.

What I really want to tell you about is what working there was like, aside from being in awe of my super smart co-workers. The software industry was _very different_ then!

Lots of things we all take for granted didn’t exist. The Internet existed but not the Web. Software was delivered to customers on tapes or floppy disks. The CD burner was still a few years in the future. Java didn’t exist, JavaScript didn’t exist, .NET didn’t exist. Languages commonly in use then were C/C++, [FORTRAN](https://en.wikipedia.org/wiki/Fortran), [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)), [Ada](https://en.wikipedia.org/wiki/Ada_(programming_language)), [Perl](https://en.wikipedia.org/wiki/Perl), [Tcl](https://en.wikipedia.org/wiki/Tcl), and [Lisp](https://en.wikipedia.org/wiki/Common_Lisp). Oh, and of course [COBOL](https://en.wikipedia.org/wiki/COBOL). I had a summer job in college as a Pascal programmer! Object-oriented programming existed, but was niche. The leading OO language then was [Smalltalk](https://en.wikipedia.org/wiki/Smalltalk). One of my college roommates became very big in the Smalltalk community, and even [wrote books about Smalltalk](https://www.amazon.com/Design-Patterns-Smalltalk-Companion/dp/0201184621)! 

Finally, software subscriptions were not a thing yet. Companies paid for software entirely up front and got to use the release they bought forever. They had to pay again for major upgrades. Some smart companies spread their payments out over a period of years, but the license was still perpetual.

When you mailed customers the new version of your product most of them wouldn’t install it right away and some would never install it. “The version we’re on works just fine for us.” It was a nightmare in support. We finally decided to support only the last four versions, to our customers’ extreme frustration but the support team’s undying gratitude.

The best SDLC available was [waterfall](https://en.wikipedia.org/wiki/Waterfall_model) with all of its problems that were not lost on us even then. The whole industry did very long release projects, such as two months of requirements gathering and design specification followed by nine months of coding and then three months of testing. Even if we did see then (and we mostly didn’t) that small, frequent releases were better in a whole bunch of ways, we couldn’t really do it. It was expensive to ship on physical media, and disruptive to our customers to have to do frequent installs.

We thought back then that software development projects should be managed like manufacturing or construction projects. That led us to create enormous [Gantt charts](https://en.wikipedia.org/wiki/Gantt_chart) (printed [dot matrix](https://en.wikipedia.org/wiki/Dot_matrix)) that we’d tack onto a huge wall, and use them to track plans and work completed. It never failed that during the first week of coding we’d discover something that we didn’t think of in the design phase, and we’d have to replan the entire project and print all new Gantt charts. We did this over and over every project. 

When the code finally reached QA, the testers would find hundreds or thousands of bugs. They had hardly seen the software before it reached them, and were only barely involved during the construction phase. Because of sheer bug volume, the test phase always needed to go way longer than planned. But by then we’d promised a delivery date to customers. So we could hit the promised date, every release went out with a whole bunch of known bugs that we’d fix in what we called a “fast follower” release several weeks later. Smart customers learned not to install the release until the “fast follower” was available.

Because of all this, projects always turned into awful death marches with lots of late nights and weekends in the weeks leading up to the release date. Burnout was high. But few people quit over it because we thought it was just how it had to be. There weren’t many other software companies to defect to anyway, and the ones that existed had death marches too.

ACD’s tech stack (which was a term we didn’t have then) was C++ on two flavors of UNIX: Digital Equipment Corporation’s [Ultrix](https://en.wikipedia.org/wiki/Ultrix) and IBM’s [AIX](https://en.wikipedia.org/wiki/IBM_AIX). Our software ran on DEC and IBM RISC-based minicomputers, machines that were about the size of a bar fridge. We had a whole bunch of them in a big, cold server room. Because we were on the outskirts of Terre Haute our power was supplied by a rural power cooperative and wasn’t terribly reliable. I don’t know why we didn’t have uninterruptible power supplies or a generator, but we didn’t. About once a month we had a short power outage. The minicomputers were chained in sequence and had to boot in order. It took about 10 minutes for one machine to boot. It took more than three hours for all of the computers to finish booting. If the power flickered after about 2 pm, we all just went home for the day.

There was no working from home. The minicomputers were available only on the in-office network. I think it was not technically impossible to connect them to the Internet, but at home we all had only dialup access. Not only would speed have been a concern, but the family wouldn’t enjoy having the phone tied up for hours while you worked.

Everyone had a terminal on their desk on which they did everything. At first all engineers used the venerable [VT100 terminal](https://en.wikipedia.org/wiki/VT100), but later they bought them all these huge $2,000 graphics terminals so they could build the UI in [X Windows](https://en.wikipedia.org/wiki/X_Window_System). UX design wasn’t a thing yet. For that matter, neither was the distinction between front end and back end engineering. Our software engineers designed our user interface, and most of them weren’t good at it.

As a technical writer, I had a Macintosh II computer on my desk running System 6. (System is what MacOS used to be called.) It had 8 MB of RAM. That was a _screaming_ machine in its day. I wrote manuals in a program called [Interleaf](https://en.wikipedia.org/wiki/Interleaf). I used a terminal emulator on the Mac to connect to the minicomputers.

There was a holy war over text editors. IDEs weren’t a thing yet, so we all coded in a text editor. I was firmly in the Emacs camp, but most of my co-workers loved vi.

Our terminals were on a [token-ring network](https://en.wikipedia.org/wiki/Token_Ring) linked back to those minicomputers. Token-ring networks don’t function unless the chain of nodes on it is complete, as information flowed through every node on its way around the network. I did not know this the day I decided to rearrange my [cubicle](https://en.wikipedia.org/wiki/Cubicle). When I unplugged my terminal to move it, I took half of the network down. I was not anyone’s favorite person that day.

AIX and Ultrix and their underlying hardware were different enough that our code was a mess of IF AIX and IF ULTRIX statements. Sometimes we had to write separate AIX and Ultrix versions of entire subroutines and functions. We had to compile the code twice, once on AIX and once on Ultrix! Java was a _total game changer_ when it came out in 1995. You mean we can write _one_ codebase with no OS- or hardware-dependent IFs and no separate routines, and run it on any machine that can run the JVM? _Voodoo!_

The software industry was a far less diverse place then. The strong majority of engineers were men, largely white, most of them younger than 35. Two of our engineering managers were women, I recall. We did have a few engineers who immigrated from elsewhere. There were no people of color. It was not safe to be out at work (or anywhere, really) then. I knew that one of the technical writers on my team was gay, but that’s only because we’d become friends and he decided to take the risk and come out to me.

The software engineering team had two titles: Software Engineer and Senior Software Engineer. This was typical in the industry. You had to have at least 10 years of experience, but more likely 15 years, as a Software Engineer before being considered for promotion to Senior. The bar was higher for Senior then, too. I’d say a Senior engineer back then was skilled and experienced more like a Staff or Principal engineer today.

I was very happy at ACD. I loved working there. Because there were only so many telephone companies then, however, we had maybe a dozen customers. Our relationship with US Sprint was always iffy, and one day we pissed them off one time too many and they canceled their contract and dared us to sue them over it. The loss of that revenue crippled us, and was the beginning of the end for ACD. The company started to circle the drain. I did not want to be unemployed in Terre Haute, so I took a job in Indianapolis and moved. Then as now, most Indiana software development happened in Indianapolis.

Let me tell you one last story from ACD, and it involves US Sprint. They were angry with us over one too many buggy releases. They sent us a list of all of the bugs they wanted fixed, and they wanted them fixed by Monday or they would buy a competitor’s product and move on from us. The Engineering team worked days and nights trying to fix those bugs. They worked into the weekend, but by Sunday morning they still had not licked a couple of particularly thorny bugs.

Remember that we had to ship code on physical media. We used tape cartridges, which were smaller than a VHS tape but way bigger than an audio cassette tape.

The Federal Express deadline was approaching on Sunday and the engineers still weren’t done. (Federal Express wouldn’t be renamed to FedEx for many years yet.) Then someone had a brilliant idea: we would ship US Sprint a blank tape with our usual letter listing the changes in the release. And so we did.

Monday we got a call from US Sprint: no matter what they tried, they were unable to load the software from the tape we sent them. Support was prepared: “Oh! We are so sorry. We can’t imagine what must have gone wrong! We will send you another tape today.”

By that time the engineers had fixed those last two bugs. We wrote the actual maintenance release to a new tape and sent it to US Sprint. The company lived to fight another day!