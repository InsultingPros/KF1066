Here are some little thing that can be easily fixed and the won't break any mod compability or game mechanics.

# Buzzsaw Projectiles
### Genaral Information
Very often saws go out of map / areas where players can reach it, or players itself doesn't pick up them and refill in trader. This leads to tremandous sound spam. You can't destroy them in any way, and have to deal with sound spam for whole game.

### Possible Fixes
For level cleanup add this to `KFGameType` -> `State MatchInProgress` -> `line 2308: function CloseShops()`:
```unrealscript
local CrossbuzzsawBlade CrossbuzzsawBlade;

foreach DynamicActors(class'CrossbuzzsawBlade', CrossbuzzsawBlade)
{
  if(CrossbuzzsawBlade == none)
    continue;
  if(CrossbuzzsawBlade.ImpactActor != none)
    CrossbuzzsawBlade.Destroy();
}
```

# Inadequate Match End
### Genaral Information
If a lone player joins to empty server as a spectator / usual player and then leave, game thinks that team is wiped and triggers voting -> map switch. Add an additional check to your `KFGameType` -> `line 4736: CheckEndGame(...)` so lobby state will be excluded.
```unrealscript
function bool CheckEndGame(PlayerReplicationInfo Winner, string Reason)
...
if(Level.Game.IsInState('PendingMatch'))
  return false;
...
```

