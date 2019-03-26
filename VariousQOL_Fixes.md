# Genaral Information
Here are some little thing that can be easily fixed and the won't break any mod compability or game mechanics.

## Inadequate match end
If a lone player joins to empty server as a spectator / usual player and then leave, game thinks that team is wiped and triggers voting -> map switch. Flame made a [CancelPLME](http://killingfloor.ru/xforum/threads/boremsja-s-avarijnym-zaversheniem-karty-do-nachala-igry.4300/) which spawns a `GameRule` and modifies `CheckEndGame` function to check lobby state. You can add the same check to your `KFGameType`'s `CheckEndGame`.
```unrealscript
function bool CheckEndGame(PlayerReplicationInfo Winner, string Reason)
...
if(Level.Game.IsInState('PendingMatch'))
  return false;
...
```
