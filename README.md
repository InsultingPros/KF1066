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
If you use macro, which is easily made in a few clicks and which throws several hundreds of CashPickup's in a short time to Blocking Volume
DOSH collision bypass: не работает с BSP геометрией и так же со встроенной коллижн моделью меша
