# KF1066
Incoming patch (I hope xD)

# DOSH RELATED INFORMATION
## General information
Let's look inside function that is responsible for dosh throwing

**KFPawn.uc**

`line: 2964: exec function TossCash(int Amount)`
```unrealscript
if( Amount<=0 )
  Amount = 50;
Controller.PlayerReplicationInfo.Score = int(Controller.PlayerReplicationInfo.Score); // To fix issue with throwing 0 pounds.
if( Controller.PlayerReplicationInfo.Score<=0 || Amount<=0 )
  return;
Amount = Min(Amount,int(Controller.PlayerReplicationInfo.Score));
```
From where:
```unrealscript
Amount = Min(Amount,int(Controller.PlayerReplicationInfo.Score));
```
So it's only limited from top. And it lets you to throw a single CashPickup with value of 1 dosh (or 2, 3, etc). And there is no limitation in toss duration. It means you throw several hundreds and thousands of CashPickup's in a very very small time (less than 0.05-0.01s). And this is the root of evil.
### Lags
Obviously several hundreds of CashPickup's lag clients PC's, and if spam continues more and more server will start to overload too. Even to the limits when the gameplay freeze for several seconds. More server hardware power - a bit shorter freeze will be, and pickup limit that triggers it will also raise. But still nothing can prevent it or overplay.

### Bypassing collisions
**ECEPTIONS!** - BSP Geometry, built in Collision of Mesh.

If you use macro, which is easily made in a few clicks and which throws several hundreds of CashPickup's in a short time at *Blocking Volume*, *Meshes*, everything else that has *Pawn* blocking feature **->** engine will fail to calcualte all these amounts of picups and collisions, and you will be able to pass through. This means you can easily:
- go out of map. Crash, Forgotten, Hospital Horrors, lots of them. Here is one example.
![IceCave](https://i.imgur.com/t4CUm2C.jpg)
- get to places where zeds cant reach you (WestLondon - on top of busses near church, under Crash's elevator, Manor's hills on top tunnel, etc), welded / closed doors, under a map (same Manor, Ice Cave, etc);
- go through closed, welded doors. Need to run or troll a teammate? No problem!
This has lot's of usecases, limited only to maps layout.

### Killing Zeds
You can do it with while bumping a zed, starting CashPickup spam and finally moving forward to it. Or bumb it, look down, spam CashPickups, and jump down to crush zed with your own collision.
- you see a FP rushing at your direction? No problems, hug it and press macro. Now he's gone.
- you see a lonely pat? Make his life super short with your macro!
But even this is not the main problem.

### Crashing Servers
If you have fast enough macro or there are several fags who spam dosh with savage speed, server will die. And lucky you if it has autorestart script, otherwise its just gone.

### Possible Fixe for these exploits
#### Hard way
Dig into engine and fix collsion calculating issues. I can get that this is a very time consuming and lot's of testing required solution (and still won't fix crashes / lags). So.
#### Lazy way.
**Limit** the CashPickups that a single **KFPawn** can throw. This can be:
- don't let pawns to throw less than 50 dosh CashPickup. So you won't be able to do shit with that few money / 50 -CashPickup's. BUT there are lot's of servers with custom respawn dosh amount, or lot's of zeds to kill, raised bounty for them, so they still will be able to abuse.
- So better leave cash amount as is, but limit the CashPickup amount that a single pawn can throw. And you will be able to draw flowers with dosh while not being able to do other harmfull stuff.
![Imgur](https://i.imgur.com/ITaG6xL.jpg)
Marco's [ServerPerks](https://forums.tripwireinteractive.com/forum/killing-floor/killing-floor-modifications/coding-aa/36898-mut-per-server-stats) has a very smart solution.
