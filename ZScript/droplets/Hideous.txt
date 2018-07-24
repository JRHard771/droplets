// Hideous Destructor compatibility classes
class DropletsTrail : BloodDroplet
{
	States
	{
	Spawn:
		TNT1 A 1 NoDelay
		{
			alpha = GetCVar("blood_alpha");
			final_scale = frandom(0.125,0.375);
			scale = (final_scale, final_scale) * 0.3333;
		}
		TNT1 A 0 A_JumpIf(waterlevel > 0, "Underwater");
		goto Death;
	}
}