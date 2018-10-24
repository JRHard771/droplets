class WallBloodSpot : Droplets
{
	Vector2 sector_pos;
	double[2] last_z; //(floor, ceiling)
	double user_scale;
	bool is_upper, is_lower;
	
	override void PostBeginPlay()
	{
		Super.PostBeginPlay();
		sector_pos = (pos.xy - AngleToVector(angle,4.0));
		[last_z[0], last_z[1]] = GetSectorZ();
	}
	
	override void Tick()
	{
		double new_z[2];
		Super.Tick();
		[new_z[0], new_z[1]] = GetSectorZ();
		if (pos.z < last_z[0] && last_z[0] != new_z[0])
			SetOrigin((pos.xy, pos.z + (new_z[0] - last_z[0])), true);
		else if (pos.z > last_z[1] && last_z[1] != new_z[1])
			SetOrigin((pos.xy, pos.z + (new_z[1] - last_z[1])), true);
		last_z[0] = new_z[0];
		last_z[1] = new_z[1];
	}
	
	double, double GetSectorZ()
	{
		return GetZAt(sector_pos.x, sector_pos.y, flags: GZF_ABSOLUTEPOS),
			GetZAt(sector_pos.x, sector_pos.y, flags: GZF_CEILING | GZF_ABSOLUTEPOS);
	}
	
	default
	{
		+NOINTERACTION
		+WALLSPRITE
		+ROLLSPRITE
		+NOGRAVITY
		RenderStyle "Translucent";
	}
	states
	{
	Spawn:
		TNT1 A 0 NoDelay
		{
			A_QueueCorpse();
			let run = RunnySpot(Actor.Spawn('RunnySpot',pos));
			run.wbs = self;
			run.user_scale = user_scale;
			run.translation = translation;
			run.angle = angle;
			frame = random(3,5);
			scale = (user_scale, user_scale);
			scale.x *= randompick(-1,1);
			scale.y *= randompick(-1,1);
			A_SetRoll(frandom(0.0,360.0));
		}
		WBLD "#" -1;
		stop;
	}
}

class RunnySpot : Droplets
{
	WallBloodSpot wbs;
	double user_scale;
	bool is_upper, is_lower;
	
	override void Tick()
	{
		Super.Tick();
		if (wbs)
			SetOrigin(wbs.pos,true);
		else
			Destroy();
	}
	
	default
	{
		+NOINTERACTION
		+WALLSPRITE
		+NOGRAVITY
		RenderStyle "Translucent";
	}
	states
	{
	Spawn:
		TNT1 A 0 NoDelay
		{
			A_QueueCorpse();
			frame = random(0,2);
			scale = (user_scale,0.001);
			scale.x *= randompick(-1,1);
		}
		goto Run;
	Run:
		WBLD "#" 1
		{
			if (scale.y < user_scale * 1.0)
			{
				scale.y += ((user_scale * 1.5) - scale.y) * 0.002;
				return resolveState(null);
			}
			else
				return resolveState("Done");
		}
		Loop;
	Done:
		WBLD "#" -1;
		stop;
	}
}