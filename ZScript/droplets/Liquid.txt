class BloodFog : Droplets
{
	double user_size, user_rot, final_scale, target_alpha;
	
	virtual void ReduceFog()
	{
		user_size += 0.0025 * final_scale;
		if (alpha < target_alpha)
			alpha += (target_alpha + 0.1 - alpha)*0.1;
		else
		{
			target_alpha = -1;
			alpha = max(0.0, alpha + (-0.1 - alpha)*0.01);
		}
	}
	
	action void A_FogWander()
	{
		invoker.scale = (invoker.user_size, 1.0);
		invoker.A_Wander(CHF_NODIRECTIONTURN|CHF_DONTTURN);
		invoker.angle += invoker.user_rot;
		invoker.ReduceFog();
		if (invoker.alpha == 0.0)
			invoker.Destroy();
	}
	
	Default
	{
		Radius 4;
		Height 5;
		+NOTELEPORT
		+CANTLEAVEFLOORPIC
		+NOBLOCKMAP
		+MOVEWITHSECTOR
		+SLIDESONWALLS
		+NOTONAUTOMAP
		Gravity 1;
		Speed 0.12;
		MaxStepHeight 1;
		RenderStyle "Translucent";
		Alpha 0.0;
	}
	States
	{
	Spawn:
		TNT1 A 0 NoDelay A_QueueCorpse;
		BFOG A 0
		{
			user_size = 0.01 * final_scale;
			angle = frandom(0,360);
			user_rot = frandompick(-1.29,1.29) * frandom(0.2,1.0);
			target_alpha = 0.333333;
		}
		goto Pool;
	Pool:
		BFOG A 1 A_FogWander;
		Loop;
	}
}

class UWBloodFog : Droplets
{
	Default
	{
		Radius 4;
		Height 5;
		+FORCEXYBILLBOARD
		+NOTELEPORT
		+NOBLOCKMAP
		+MOVEWITHSECTOR
		+SLIDESONWALLS
		+NOTONAUTOMAP
		+NOGRAVITY
		Speed 0.35;
		Scale 0.25;
		MaxStepHeight 1;
		RenderStyle "Translucent";
		Alpha 1.0;
	}
	States
	{
	Spawn:
		HITB A 1 NoDelay A_ChangeVelocity(frandom(-2.0,2.0),frandom(-2.0,2.0),frandom(-2.0,2.0));
		HITB A 1 A_SetScale(scale.x + 0.05);
		HITB B 1;
		HITB B 1 A_SetScale(scale.x + 0.05);
		HITB C 1;
		HITB C 2 A_SetScale(scale.x + 0.05);
		HITB D 2;
		HITB D 2 A_SetScale(scale.x + 0.05);
		HITB E 2;
		HITB E 2 A_SetScale(scale.x + 0.05);
	Cleanup:
		HITB E 0 A_JumpIf(waterlevel < 2, "Abort");
		TNT1 A 0 A_FadeOut(0.0125,true);
		TNT1 A 0 A_SetScale(scale.x + 0.05);
		HITB E 3 A_Wander;
		Loop;
	Abort:
		"----" A 1 A_FadeOut(0.1,true);
		Loop;
	}
}