# Genaral Information
Here are some little thing that can be easily fixed and the won't break any mod compability or game mechanics.

## Annoying buzzsaws...
Please, JUST PLEASE, add this to `KFGameType` -> `State MatchInProgress` -> `function CloseShops()`:
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
90% of players doesn't pickup their buzzsaws (even if they are lucky not to shoot saws at places where they cant be picked up again), they just refil and continue the spam. And the annoying noise will become more and more strong and frequant. You can't deal with it in any way. So i'd say removing them every wave is pretty fair.

## Inadequate match end
If a lone player joins to empty server as a spectator / usual player and then leave, game thinks that team is wiped and triggers voting -> map switch. Flame made a [CancelPLME](http://killingfloor.ru/xforum/threads/boremsja-s-avarijnym-zaversheniem-karty-do-nachala-igry.4300/) which spawns a `GameRule` and modifies `CheckEndGame` function to check lobby state. You can add the same check to your `KFGameType`'s `CheckEndGame`.
```unrealscript
function bool CheckEndGame(PlayerReplicationInfo Winner, string Reason)
...
if(Level.Game.IsInState('PendingMatch'))
  return false;
...
```

