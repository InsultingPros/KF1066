# KF1066
Incoming patch (I hope xD)

# DOSH RELATED INFORMATION
## General information
If we look inside main function that is responsible for dosh throwing we will see:

**KFPawn.uc** -> **exec function TossCash(int Amount)**
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
### Bypassing collisions
**ECEPTIONS!** - BSP Geometry, built in Collision of Mesh.

If you use macro, which is easily made in a few clicks and which throws several hundreds of CashPickup's in a short time at *Blocking Volume*, *Meshes*, everything else that has *Pawn* blocking feature **->** engine will fail to calcualte all these amounts of picups and collisions, and you will be able to pass through. This means you can easily:
- go out of map. Crash, Forgotten, Hospital Horrors, lots of them. Here is one screenshot.
!(https://i.imgur.com/t4CUm2C.jpg)
- get to places where zeds cant reach you (WestLondon - on top of busses near church, under Crash's elevator, Manor's hills on top tunnel, etc), welded / closed doors, under a map (same Manor, Ice Cave, etc); 
