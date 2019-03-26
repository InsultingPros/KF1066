# KF1066
Incoming patch (I hope xD)

# DOSH RELATED INFORMATION
## General information
Since KF limits only maximum amount of dosh that can throwed

**KFPawn.uc** -> **exec function TossCash(int Amount)**
```unrealscript
if( Amount<=0 )
        Amount = 50;
    Controller.PlayerReplicationInfo.Score = int(Controller.PlayerReplicationInfo.Score); // To fix issue with throwing 0 pounds.
    if( Controller.PlayerReplicationInfo.Score<=0 || Amount<=0 )
        return;
    Amount = Min(Amount,int(Controller.PlayerReplicationInfo.Score));
```
Controller.PlayerReplicationInfo.Score(limited to )
throw If you throw huge amount of dosh (per 1
DOSH collision bypass: не работает с BSP геометрией и так же со встроенной коллижн моделью меша
