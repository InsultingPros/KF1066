# Genaral Information
## Part A
Almost all KF weapons can be used to teamkill players. Happen's because your **KFPawn**'s `line 2250: TakeDamage(...)` doesn't have some checks if InstigatedBy is none. It means when some one shoots harpoon, orca granade, m99, xbow, autorifles, etc etc and go to spectators quickly, projectile hits Pawns and *DEALS* damage like it was made by an enemy, and kill it. It's being used to troll players a lot. Just join, buy harpoon (coz its the easiest to pull), shoot, go spectate. Done. But if you have macro for shoot-spectate case you can do it with 80% of KF weaponary (not hitscan ones + some others).
And there is another issue related with this. You can toss a nade at your teammate's **PipeBombs** and go spec, this will detonate them and kill the owner. About this in part B.
## Part B
Pipes have another issue. Much more deadly than the first one, at least to zeds. You can keep triggering **PipeBombProjectile** with high firerate weapons (like medguns, fal, etc), flamethrower, shotguns (this one needs much closer distance, and is mainly being used for pat finishing) and will deal tremendous amount of damage - from 8k to 17k, depends how succesfull was your aim. Happens because your weapons execute **PipeBombProjectile** 's `line 485: TakeDamage(...)` and before projectile gets destroyed some time passes, and there is no bool to prevent TakeDamage trigger again and again. So you just keep shooting for 0.2-0.3 seconds and damage increases to a nuke like, because in fact several explosion happen.
Aaaand besides that when multiple pipe bombs explode or you detonate one with methods described above - you will get huge log spam about out of bound sound array.

## Possible Fixes
1. Mutant's [Explosive Fix Mut](https://forums.tripwireinteractive.com/forum/killing-floor/killing-floor-modifications/general-modding-discussion-aa/106460-explosives-fix-mutator). I even made a [WhiteListed version](https://forums.tripwireinteractive.com/forum/killing-floor/killing-floor-modifications/general-modding-discussion-aa/106460-explosives-fix-mutator?p=2329339#post2329339). Basically spawns a GameRule which does:
```unrealscript
function int NetDamage( int OriginalDamage, int Damage, pawn injured, pawn instigatedBy, vector HitLocation, out vector Momentum, class<DamageType> DamageType )
{
	local TeamGame TG;

	TG = TeamGame(Level.Game);

	if ( KFPawn(injured) != None && TG != None && Damage > 0 )
	{
		if ( (KFPawn(instigatedBy) != None || FakePlayerPawn(instigatedBy) != None ) && (instigatedBy.PlayerReplicationInfo == None || instigatedBy.PlayerReplicationInfo.bOnlySpectator) )
		{
			Momentum = vect(0,0,0);
			if ( NoFF(injured, TG.FriendlyFireScale) )
				return 0;
			else if ( OriginalDamage == Damage )
				return Damage*TG.FriendlyFireScale;
		}
		else if ( instigatedBy == None && !DamageType.default.bCausedByWorld )
		{
			Momentum = vect(0,0,0);
			if ( NoFF(injured, TG.FriendlyFireScale) )
				return 0;
			else if ( OriginalDamage == Damage )
				return Damage*TG.FriendlyFireScale;
		}
	}

	return Super.NetDamage( OriginalDamage, Damage, injured, instigatedBy, HitLocation, Momentum,DamageType );
}

function bool NoFF( Pawn injured, float FF )
{
	return (FF == 0.0 || (Vehicle(injured) != None && Vehicle(injured).bNoFriendlyFire));
}
```
But this is a half solution, since it blocks fire on levels and breaks TestMaps, some story maps (like RE-Mansion where there is a husk projectile emitter at some part, and it deals 0....).

2. Since you have to edit **KFPawn** for dosh exploits, please add fixes from Poosh's [ScrnHumanPawn](https://github.com/poosh/KF-ScrnBalance/blob/fac4421d42022fafb6247ac6b78d5acbbfe79029/Classes/ScrnHumanPawn.uc#L1951). It has all the needed comments and code are there.

3. For **PipeBombs** lets look again at [ScrnPipeBombProjectile](https://github.com/poosh/KF-ScrnBalance/blob/master/Classes/ScrnPipeBombProjectile.uc). 

- `default.ExplodeSounds[0] = sound(DynamicLoadObject(default.ExplodeSoundRefs[0], class'Sound', true));` is moved from `PreloadAssets()` to `defaultproperties` block. And removed `default.ExplodeSounds[0] = none;` from `UnloadAssets`. Fixes log spam.
- Added a `bTriggered` bool to prevent `TakeDamage(...)` to trigger more than once. Fixes super damage.
- Added a check to return all damage that is NOT from zeds. Fixes pipe detonation with spectator projectiles / nades.

P.S. Poosh is not against to use his code, and besides that I asked him to make those fixes xD
