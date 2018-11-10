class FloorSplat : Droplets
{
	double final_scale;
	
	action void A_SplatImage()
	{
		invoker.frame = random(0,6);
	}

	Default
	{
		Radius 1;
		Height 1;
		+NOTONAUTOMAP
		+NOTELEPORT
		+THRUACTORS
		+MOVEWITHSECTOR
		+DONTSPLASH
		RenderStyle "Translucent";
	}
	States
	{
	Spawn:
		TNT1 A 12 NoDelay A_JumpIf(LiquidCheck(),"Null");
		PLUD A 0
		{
			alpha = GetCVar("blood_alpha");
			A_SetAngle(frandom(0.0,360.0));
			A_SplatImage();
		}
		"####" "#" 1 A_SetScale(final_scale * 0.626 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 0.84 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 0.946 * GetCVar("blood_size"),0.1);
		"####" "#" 1 A_SetScale(final_scale * 1.00 * GetCVar("blood_size"),0.1);
	Done:
		"####" "#" 0
		{
			if (random(0,2))
				A_QueueCorpse();
		}
		"####" "#" -1;
		Stop;
	}
}