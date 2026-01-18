---
tags:
  - topic/programming/gamedev
author: England Liz
source: https://lizengland.com/blog/2014/04/the-door-problem/
read: true
---
“So what does a game designer do? Are you an artist? Do you design characters and write the story? Or no, wait, you’re a programmer?”

Game design is one of those nebulous terms to people outside the game industry that’s about as clear as the “astrophysicist” job title is to me. It’s also my job, so I find myself explaining what game design means to a lot of people from different backgrounds, some of whom don’t know anything about games.
## The Door Problem

I like to describe my job in terms of “The Door Problem”.

Premise: You are making a game.

- Are there doors in your game?
- Can the player open them?
- Can the player open every door in the game?
- Or are some doors for decoration?
- How does the player know the difference?
- Are doors you can open green and ones you can’t red? Is there trash piled up in front of doors you can’t use? Did you just remove the doorknobs and call it a day?
- Can doors be locked and unlocked?
- What tells a player a door is locked and will open, as opposed to a door that they will never open?
- Does a player know how to unlock a door? Do they need a key? To hack a console? To solve a puzzle? To wait until a story moment passes?
- Are there doors that can open but the player can never enter them?
- Where do enemies come from? Do they run in from doors? Do those doors lock afterwards?
- How does the player open a door? Do they just walk up to it and it slides open? Does it swing open? Does the player have to press a button to open it?
- Do doors lock behind the player?
- What happens if there are two players? Does it only lock after both players pass through the door?
- What if the level is REALLY BIG and can’t all exist at the same time? If one player stays behind, the floor might disappear from under them. What do you do?
- Do you stop one player from progressing any further until both are together in the same room?
- Do you teleport the player that stayed behind?
- What size is a door?
- Does it have to be big enough for a player to get through?
- What about co-op players? What if player 1 is standing in the doorway – does that block player 2?
- What about allies following you? How many of them need to get through the door without getting stuck?
- What about enemies? Do mini-bosses that are larger than a person also need to fit through the door?

It’s a pretty classic design problem. SOMEONE has to solve The Door Problem, and that someone is a designer.
## The Other Door Problems

To help people understand the role breakdowns at a big company, I sometimes go into how other people deal with doors.

- Creative Director: “Yes, we definitely need doors in this game.”
- **Project Manager**: “I’ll put time on the schedule for people to make doors.”
- Designer: “I wrote a doc explaining what we need doors to do.”
- Concept Artist: “I made some gorgeous paintings of doors.”
- Art Director: “This third painting is exactly the style of doors we need.”
- Environment Artist: “I took this painting of a door and made it into an object in the game.”
- Animator: “I made the door open and close.”
- **Sound Designer**: “I made the sounds the door creates when it opens and closes.”
- **Audio Engineer**: “The sound of the door opening and closing will change based on where the player is and what direction they are facing.”
- **Composer**: “I created a theme song for the door.”
- FX Artist: “I added some cool sparks to the door when it opens.”
- **Writer**: “When the door opens, the player will say, ‘Hey look! The door opened!’ “
- Lighter: “There is a bright red light over the door when it’s locked, and a green one when it’s opened.”
- Legal: “The environment artist put a Starbucks logo on the door. You need to remove that if you don’t want to be sued.”
- **Character Artist**: “I don’t really care about this door until it can start wearing hats.”
- Gameplay Programmer: “This door asset now opens and closes based on proximity to the player. It can also be locked and unlocked through script.”
- **AI Programmer:** “Enemies and allies now know if a door is there and whether they can go through it.”
- **Network Programmer:** “Do all the players need to see the door open at the same time?”
- **Release Engineer**: “You need to get your doors in by 3pm if you want them on the disk.”
- **Core Engine Programmer:** “I have optimized the code to allow up to 1024 doors in the game.”
- **Tools Programmer:** “I made it even easier for you to place doors.”
- Level Designer: “I put the door in my level and locked it. After an event, I unlocked it.”
- **UI Designer**: “There’s now an objective marker on the door, and it has its own icon on the map.”
- **Combat Designer**: “Enemies will spawn behind doors, and lay cover fire as their allies enter the room. Unless the player is looking inside the door in which case they will spawn behind a different door.”
- **Systems Designer**: “A level 4 player earns 148xp for opening this door at the cost of 3 gold.”
- **Monetization Designer**: “We could charge the player $.99 to open the door now, or wait 24 hours for it to open automatically.”
- QA Tester: “I walked to the door. I ran to the door. I jumped at the door. I stood in the doorway until it closed. I saved and reloaded and walked to the door. I died and reloaded then walked to the door. I threw grenades at the door.”
- **UX / Usability Researcher**: “I found some people on Craigslist to go through the door so we could see what problems crop up.”
-  **Localization**: “Door. Puerta. Porta. Porte. Tür. Dør. Deur. Drzwi. Drws. 문”
- **Producer**: “Do we need to give everyone those doors or can we save them for a pre-order bonus?”
- **Publisher**: “Those doors are really going to help this game stand out during the fall line-up.”
- **CEO:** “I want you all to know how much I appreciate the time and effort put into making those doors.”
- **PR**: “To all our fans, you’re going to go crazy over our next reveal `#gamedev #doors #nextgen #retweet”`
- **Community Manager**: “I let the fans know that their concerns about doors will be addressed in the upcoming patch.”
- **Customer Support**: “A player contacted us, confused about doors. I gave them detailed instructions on how to use them.”
- Player: “I totally didn’t even notice a door there.”

One of the reasons I like this example is because it’s so mundane. There’s an impression that game design is flashy and cool and about crazy ideas and fun all the time. But when I start off with, “Let me tell you about doors…” it cuts straight to the everyday practical considerations.