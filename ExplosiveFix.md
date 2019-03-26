# Genaral Information
Almost all KF weapons can be used to teamkill players. Happen's because your **KFPawn**'s `line 2250: TakeDamage(...)` doesn't have some checks if InstigatedBy is none. It means when some one shoots harpoon, orca granade, m99, xbow, autorifles, etc etc and go to spectators quickly, projectile hits Pawns and *DEALS* damage like it was made by an enemy, and kill it. It's being used to troll players a lot. Just join, buy harpoon (coz its the easiest to pull), shoot, go spectate. Done. But if you have macro for shoot-spectate case you can do it with 80% of KF weaponary (mainly not hitscan ones).

## Possible Fixes
1. Mutant's [Explosive Fix Mut](https://forums.tripwireinteractive.com/forum/killing-floor/killing-floor-modifications/general-modding-discussion-aa/106460-explosives-fix-mutator). I even made a [hiteListed version](https://forums.tripwireinteractive.com/forum/killing-floor/killing-floor-modifications/general-modding-discussion-aa/106460-explosives-fix-mutator?p=2329339#post2329339). Basically spawns a GameRule which does:
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
2. Since you have to edit **KFPawn** for dosh exploits, please add fixes from Poosh's [ScrnHumanPawn](https://github.com/poosh/KF-ScrnBalance/blob/fac4421d42022fafb6247ac6b78d5acbbfe79029/Classes/ScrnHumanPawn.uc#L1951). It has all the needed comments so I won't post them here.
