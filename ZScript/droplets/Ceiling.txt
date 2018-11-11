class CeilingDroplet : DecoDroplet
{
	Default
	{
		Height 1;
		-MISSILE
		-FORCEXYBILLBOARD
		+FORCEYBILLBOARD
		+NOINTERACTION
	}
	
	override void PostBeginPlay()
	{
		Super.PostBeginPlay();
		SetOrigin((pos.xy, ceilingz), false);
	}
	
	States
	{
	Spawn:
		TNT1 A 1 NoDelay { alpha = GetCVar("blood_alpha"); }
		DROP A 6 {scale = (0.53,0.1) * final_scale;}
		DROP A 5 {scale = (0.4,0.15) * final_scale;}
		DROP A 4 {scale = (0.3,0.22) * final_scale;}
		DROP A 3 {scale = (0.22,0.33) * final_scale;}
		DROP A 2 {scale = (0.17,0.5) * final_scale;}
		TNT1 A 0 { bNOINTERACTION = false; scale = (0.125, 0.75) * final_scale; gravity = GetCVar("blood_grav"); }
		DROP A 8;
		TNT1 A 0 { bMISSILE = true; }
		goto Fall;
	Fall:
		TNT1 A 0 A_JumpIf(pos.z <= floorz, "Death");
		DROP A 1;
		TNT1 A 0 A_JumpIf(waterlevel > 0, "Underwater");
		Loop;
	Death:
		TNT1 A 0 { bWINDTHRUST = false; bMISSILE = false; }
		TNT1 A 0 {if (blood_splash) A_SplitDroplet('DecoDroplet', scale.x * 2.666664);}
		TNT1 A 0 A_PlaySound("blood/drip",volume: final_scale * last_vel * 0.0625);
		TNT1 A 0 A_JumpIf(pos.z > floorz + 8.0, "Wall");
		TNT1 A 0 A_JumpIf(LiquidCheck(),"Fog");
		TNT1 A 0 A_SetAngle(frandom(0.0,360.0));
		PLUD "#" 1
		{
			frame = random(0,2);
			A_SetScale(final_scale * blood_size,0.1);
		}
	Splat:
		"####" "#" 1 A_SetScale(final_scale * 3.13 * blood_size,0.1);
		"####" "#" 1 A_SetScale(final_scale * 4.2 * blood_size,0.1);
		"####" "#" 1 A_SetScale(final_scale * 4.73 * blood_size,0.1);
		"####" "#" 1 A_SetScale(final_scale * 5.00 * blood_size,0.1);
		goto Cleanup;
	}
}

class CeilDripper : Droplets
{
	double final_scale;

	override void Tick()
	{
		Super.Tick();
		SetOrigin((pos.x, pos.y, ceilingz - rndzoffset), false);
	}
	
	action void A_Drip()
	{
		double factor = invoker.final_scale;
		if (factor > 0.5 && frandom(0,100) < factor * 25)
		{
			let droplet = CeilingDroplet(Actor.Spawn('CeilingDroplet',invoker.pos + (frandom(-1.5,1.5),frandom(-1.5,1.5),0)));
			droplet.final_scale = factor;
			droplet.translation = invoker.translation;
			invoker.final_scale -= frandom(0.00, 0.04);
		}
	}
	
	Default
	{
		Radius 1;
		Height 1;
		+THRUACTORS
		+NOTONAUTOMAP
		+SPAWNCEILING
		+NOGRAVITY
		//DistanceCheck GetCVar(cl_drop1_dist);
	}
	States
	{
	Spawn:
		TNT1 A 0 NoDelay A_QueueCorpse;
		PLUD "#" 0 { frame = random(0,2); }
		"####" "#" 0 A_SetAngle(frandom(0.0,360.0));
		"####" "#" 1 A_SetScale(final_scale * 1.00 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 3.13 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 4.2 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 4.73 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 5.00 * GetCVar("blood_size"),0.1);
		goto Drippy;
	Drippy:
		"####" "#" random(6,8) A_Drip;
		goto Drippy;
	}
}