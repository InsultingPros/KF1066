# General information
Let's look inside function that is responsible for dosh throwing

**\KFMod\KFPawn.uc**

`line 2964:`
```unrealscript
exec function TossCash(int Amount)
...
if( Amount<=0 )
  Amount = 50;
Controller.PlayerReplicationInfo.Score = int(Controller.PlayerReplicationInfo.Score); // To fix issue with throwing 0 pounds.
if( Controller.PlayerReplicationInfo.Score<=0 || Amount<=0 )
  return;
Amount = Min(Amount,int(Controller.PlayerReplicationInfo.Score));
...
```
From where:
```unrealscript
Amount = Min(Amount,int(Controller.PlayerReplicationInfo.Score));
```
So it's only limited from top. And it lets you to throw a single CashPickup with value of 1 dosh (or 2, 3, etc). And there is no limitation in toss duration. It means you are able to throw several hundreds and thousands of CashPickup's in a few milliseconds during all games you join (since you always get in average 3-8k dosh). And this is the root of evil.

## Lags
Obviously even several hundreds of CashPickup's lag clients PC's, and if spam continues more and more server will start to overload too. Even to the limits when the gameplay freeze for several seconds. More server hardware power - a bit shorter freeze will be, and pickup limit that triggers it will also raise. But still nothing can prevent it.

## Bypassing collisions
**ECEPTIONS!** - BSP Geometry, built in Collision of Mesh.

If you use macro, which is easily made in a few clicks and which throws several hundreds of CashPickup's in a short time at *Blocking Volume*, *Meshes*, everything else that has *Pawn* blocking feature **->** engine will fail to calcualte all these amounts of picups and collisions, and you will be able to pass through. This means you can easily:
- go out of map. Crash, Forgotten, Hospital Horrors, lots of them. Here is one example.
![IceCave](https://i.imgur.com/t4CUm2C.jpg)
- get to places where zeds cant reach you (WestLondon - on top of busses near church, under Crash's elevator, Manor's hills on top tunnel, etc), welded / closed doors, under a map (same Manor, Ice Cave, etc);
- go through closed, welded doors. Need to run or troll a teammate? No problem!
This has lot's of usecases, limited only to maps layout.

## Killing Zeds
You can do it with while bumping a zed, starting CashPickup spam and finally moving forward to it. Or bumb it, look down, spam CashPickups, and jump down to crush zed with your own collision.
- you see a FP rushing at your direction? No problems, hug it and press macro. Now he's gone.
- you see a lonely pat? Make his life super short with your macro!
But even this is not the main problem.

## Crashing Servers
If you have fast enough macro or there are several fags who spam dosh with savage speed, server will die. And lucky you if it has autorestart script, otherwise its just gone.

# Possible Fixes for these exploits
#### Hard way
Dig into engine and fix collsion calculating issues. I can get that this is a very time consuming and lot's of testing required solution (and still won't fix crashes / lags). So.
#### Lazy way.
**Limit** the CashPickups that a single **KFPawn** can throw. This can be:
- don't let pawns to throw less than 50 dosh CashPickup. So you won't be able to do shit with that few money / 50 -CashPickup's. BUT there are lot's of servers with custom respawn dosh amount, or lot's of zeds to kill, raised bounty for them, so they still will be able to abuse.
- So better limit the CashPickup amount that a single pawn can throw. And you will be able to draw flowers with dosh while not being able to do other harmfull stuff.
![Imgur](https://i.imgur.com/ITaG6xL.jpg)
Marco's [ServerPerks](https://forums.tripwireinteractive.com/forum/killing-floor/killing-floor-modifications/coding-aa/36898-mut-per-server-stats) has a very smart solution. It's [Source files](http://www.klankaos.com/downloads/ServerPerksSrc.rar).

**\Source\ServerPerks\Classes\SRHumanPawn.uc**

`line 7:`
```unrealscript
var transient float CashTossTimer,LongTossCashTimer;
var transient byte LongTossCashCount;
```
`line 194:`
```unrealscript
exec function TossCash( int Amount )
{
  // To fix cash tossing exploit.
  if( CashTossTimer<Level.TimeSeconds && (LongTossCashTimer<Level.TimeSeconds || LongTossCashCount<20) )
  {
    Super.TossCash(Max(Amount,50));
    CashTossTimer = Level.TimeSeconds+0.1f;
    if( LongTossCashTimer<Level.TimeSeconds )
    {
      LongTossCashTimer = Level.TimeSeconds+5.f;
      LongTossCashCount = 0;
    }
    else ++LongTossCashCount;
  }
}
```

So you can just do similar timing check in your **KFPawn**.
`line 194:`
```unrealscript
var transient float CashTossTimer,LongTossCashTimer;
var transient byte LongTossCashCount;

exec function TossCash( int Amount )
{
  // To fix cash tossing exploit.
  if( CashTossTimer<Level.TimeSeconds && (LongTossCashTimer<Level.TimeSeconds || LongTossCashCount<20) )
  // 20 is way too punishing, 60-100 is kinda ok. But if you want to keep it as low ->
  {
    // CashPickup spawning and etc, your main code here
    CashTossTimer = Level.TimeSeconds+0.1f;
    if( LongTossCashTimer<Level.TimeSeconds )
    {
      // -> then at least decrease this too
      LongTossCashTimer = Level.TimeSeconds+5.f; 
      LongTossCashCount = 0;
    }
    else ++LongTossCashCount;
  }
}
```

