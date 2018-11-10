class GibletA : Droplets
{
	double rollFactor;

	action void A_Drip()
	{
		let droplet = BloodDroplet(Actor.Spawn('BloodDroplet',invoker.pos + (frandom(-1.5,1.5),frandom(-1.5,1.5),0)));
		droplet.final_scale = frandom(0.5,1.0);
		droplet.scale = (droplet.final_scale, droplet.final_scale) * 0.25;
		droplet.vel = (invoker.vel.xy * cos(invoker.roll) * 1.5, abs(invoker.vel.z) * sin(invoker.roll) * 1.5);
		droplet.translation = invoker.translation;
	}
	
	override void BeginPlay()
	{
		rollFactor = frandom(-45.0,45.0);
		roll = frandom(0,360);
		gravity = blood_grav;
		scale.x = randompick(-1,1);
		Super.BeginPlay();
	}
	
	override void Tick()
	{
		if (vel.length() > 0)
			roll += rollFactor;
		Super.Tick();
	}
	
	Default
	{
		+MISSILE
		+FLOORCLIP
		+FORCEXYBILLBOARD
		+NOBLOCKMAP
		+MOVEWITHSECTOR
		+THRUACTORS
		+INTERPOLATEANGLES
		+ROLLSPRITE
		+USEBOUNCESTATE
		BounceType "Doom";
		BounceFactor 0.416667;
		WallBounceFactor 0.833333;
		Gravity 1.0;
		Radius 2;
		Height 4;
		DeathHeight 4;
		Speed 4;
		Droplets.SnapToFloor false;
	}
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL A random(7,11) A_Drip;
		Wait;
	Death:
		"----" A -1
		{
			A_FloorSplat(1.0);
			bFORCEXYBILLBOARD = false;
			frame += 13;
			roll = 0;
			if (random(0,2))
				A_QueueCorpse();
		}
		Stop;
	Bounce:
		"----" A 1 { rollFactor *= -0.833333; }
		"----" A random(7,11) A_Drip;
		Wait;
	}
}

class GibletB : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL B random(7,11) A_Drip;
		Wait;
	}
}

class GibletC : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL C random(7,11) A_Drip;
		Wait;
	}
}

class GibletD : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL D random(7,11) A_Drip;
		Wait;
	}
}

class GibletE : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL E random(7,11) A_Drip;
		Wait;
	}
}

class GibletF : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL F random(7,11) A_Drip;
		Wait;
	}
}

class GibletG : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL G random(7,11) A_Drip;
		Wait;
	}
}

class GibletH : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL H random(7,11) A_Drip;
		Wait;
	}
}

class GibletI : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL I random(7,11) A_Drip;
		Wait;
	}
}

class GibletJ : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL J random(7,11) A_Drip;
		Wait;
	}
}

class GibletK : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL K random(7,11) A_Drip;
		Wait;
	}
}

class GibletL : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL L random(7,11) A_Drip;
		Wait;
	}
}

class GibletM : GibletA
{
	States
	{
	Spawn:
		TNT1 A 0;
		GIBL M random(7,11) A_Drip;
		Wait;
	}
}

class RandGiblet : GibletA
{
	States
	{
	Spawn:
		GIBL A 0 NoDelay { frame = random(0,12); }
		"----" A random(7,11) A_Drip;
		Wait;
	}
}

class GibSpray : Droplets
{
	action void A_DropletsGib(class<Actor> type)
	{
		Actor giblet;
		if (master)
			giblet = Actor.Spawn(type,(pos.xy + AngleToVector(frandom(0,360),frandom(0,master.radius)), pos.z + frandom(0,master.height)));
		else
			giblet = Actor.Spawn(type,(pos.xy + AngleToVector(frandom(0,360),frandom(0,16)), pos.z + frandom(0,32)));
		giblet.Vel3DFromAngle(frandom(6,10),frandom(0,360),frandom(-90,0));
		double ratio = frandom(0.5,1.0);
		giblet.vel = (giblet.vel * ratio) + (vel * (1.0 - ratio));
		giblet.translation = translation;
	}
	
	Default
	{
		+NOGRAVITY
		+NOBLOCKMAP
		+THRUACTORS
		+NOTELEPORT
	}
	States
	{
	Spawn:
		TNT1 A 5;
		TNT1 A 0
		{
			for (int i = 0; i < 2 + blood_amt * 2; i++)
			{
				Actor mist;
				if (master)
					mist = Spawn('BigBloodMist',(pos.xy + AngleToVector(frandom(0,360),frandom(0,master.radius)), pos.z));
				else
					mist = Spawn('BigBloodMist',(pos.xy + AngleToVector(frandom(0,360),frandom(0,16)), pos.z));
				mist.vel += vel * frandom(0.03, 0.06);
				mist.translation = translation;
			}
			A_DropletsGib("GibletA");
			A_DropletsGib("GibletB");
			A_DropletsGib("GibletC");
			A_DropletsGib("GibletD");
			A_DropletsGib("GibletE");
			A_DropletsGib("GibletF");
			A_DropletsGib("GibletG");
			A_DropletsGib("GibletH");
			A_DropletsGib("GibletI");
			A_DropletsGib("GibletJ");
			A_DropletsGib("GibletK");
			A_DropletsGib("GibletL");
			A_DropletsGib("GibletM");
			A_FloorSplat(3.0);
		}
		Stop;
	}
}