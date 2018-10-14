class DropletsEventHandler : EventHandler
{
	bool blood_gibs, blood_pools;
	
	override void OnRegister()
	{
		blood_gibs = CVar.FindCVar("blood_gibs").GetBool();
		blood_pools = CVar.FindCVar("blood_pools").GetBool();
		Super.OnRegister();
	}
	
	override void WorldTick()
	{
		blood_gibs = CVar.FindCVar("blood_gibs").GetBool();
		blood_pools = CVar.FindCVar("blood_pools").GetBool();
		Super.WorldTick();
	}
	
	override void WorldThingDamaged(WorldEvent e)
	{
		let mo = e.Thing;
		if (!mo.bISMONSTER || mo.bNOBLOOD)
			return;
		
		if (mo.health <= 0)
		{
			if (blood_gibs &&
				(!(e.Inflictor && e.Inflictor.bNoExtremeDeath) &&
				(mo.health < mo.GetGibHealth() ||
				(e.Inflictor && e.Inflictor.bExtremeDeath)) &&
				(mo.FindState("XDeath") || mo.FindState("Death.Extreme"))) ||
				blood_alwaysgib)
			{
				let giblets = Actor.Spawn('GibSpray',mo.pos + (0,0,32));
				giblets.master = mo;
				giblets.translation = mo.bloodtranslation;
				giblets.vel = mo.vel;
			}
				
			if (!blood_pools)
				return;
			
			let pool = Actor.Spawn('BloodPool2',mo.pos);
			pool.translation = mo.bloodtranslation;
			pool.master = mo;
		}
	}
}