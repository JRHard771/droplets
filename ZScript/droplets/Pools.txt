class BloodPool2 : Droplets
{
	double scale_percent, final_scale;
	
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
	Delay:
		TNT1 A 35;
	Spawn:
		TNT1 A 0 NoDelay
		{
			if (!master)
				Destroy();
			else if (master.vel.Length() > 0.028571)
				SetStateLabel("Delay");
		}
		TNT1 A 0
		{
			SetOrigin(master.pos, false);
			final_scale = (master.radius) * 0.05;
		}
		TNT1 A 0 A_JumpIf(LiquidCheck(),"Fog");
		TNT1 A 0 { scale_percent = 0.0025; }
		TNT1 A 0 A_SetAngle(frandom(0.0,360.0));
		goto Pool;
	Pool:
		PLUD C 1 A_SetScale(scale_percent * final_scale, 0.5);
		TNT1 A 0 { scale_percent += (1.1 - scale_percent) * 0.01; }
		TNT1 A 0 A_JumpIf(scale_percent < 1.0, "Pool");
		goto Done;
	Done:
		PLUD C 35 {if (!master) Destroy();}
		Wait;
	Fog:
		TNT1 A 0;
		Stop;
	}
}