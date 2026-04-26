# Code Block
```js
api.giveItem(myId, "Diamond Sword", 10, {customDisplayName: "Trident", customDescription: "Throw at your enemies"})
```

# World Code
```js
const LASER_MAX_RANGE = 700;
const LASER_STEP = 7;
const LASER_DAMAGE = 70;
const LASER_DAMAGE_RADIUS = 3.0;
const LASER_PARTICLE_COUNT = 60;
const LASER_PARTICLE_LIFETIME = 0.1;
const LASER_BEAM_SPEED = 1;
const LASER_GRAVITY = -0.005; 

function removeHeldItem(playerId) {
  const slot = api.getSelectedInventorySlotI(playerId);
  try {
    if (typeof api.setSlot === "function") {
      try { api.setSlot(playerId, "Air"); } catch (e) { api.setSlot(playerId, slot, "Air"); }
    } else {
      api.setItemSlot(playerId, slot, "Air");
    }
  } catch (e) {
    try { api.setItemSlot(playerId, slot, "Air"); } catch (err) { api.log("removeHeldItem failed: " + err); }
  }
}

function playParticlesStandard(x, y, z, dir, count) {
  try {
    api.playParticleEffect({
      dir1: [dir[0] * 0.9, dir[1] * 0.9, dir[2] * 0.9],
      dir2: [dir[0] * 1.1, dir[1] * 1.1, dir[2] * 1.1],
      pos1: [x - 0.2, y - 0.2, z - 0.2],
      pos2: [x + 0.2, y + 0.2, z + 0.2],
      texture: "glint",
      minLifeTime: LASER_PARTICLE_LIFETIME,
      maxLifeTime: LASER_PARTICLE_LIFETIME,
      minEmitPower: 2,
      maxEmitPower: 3,
      minSize: 0.15,
      maxSize: 0.9,
      manualEmitCount: count,
      gravity: [0, -1, 0],
      colorGradients: [
        { timeFraction: 0, minColor: [0, 255, 200, 1], maxColor: [0, 200, 255, 1] },
        { timeFraction: 1, minColor: [0, 80, 120, 0.2], maxColor: [0, 80, 120, 0.2] }
      ],
      velocityGradients: [
        { timeFraction: 0, factor: 1, factor2: 1 }
      ],
      blendMode: 1
    });
    return true;
  } catch (e) {
    return false;
  }
}

onPlayerClick = (playerId) => {
  const held = api.getHeldItem(playerId);
  if (!held) return;
  const isDiamondSword = held.name && held.name.toLowerCase().includes("diamond");
  const customName = held.attributes?.customDisplayName;
  if (!isDiamondSword || customName !== "Trident") return;
  removeHeldItem(playerId);
  try { api.broadcastSound("cannonFire3", 1, 5, { playerIdOrPos: playerId, maxHearDist: 30 }); } catch (e) {}
  const pos = api.getPosition(playerId);
  const facing = api.getPlayerFacingInfo(playerId).dir;
  const mag = Math.sqrt(facing[0]*facing[0] + facing[1]*facing[1] + facing[2]*facing[2]) || 1;
  const dir = [facing[0]/mag, facing[1]/mag, facing[2]/mag];
  let px = pos[0];
  let py = pos[1] + 1;
  let pz = pos[2];
  let velx = dir[0] * LASER_BEAM_SPEED;
  let vely = dir[1] * LASER_BEAM_SPEED;
  let velz = dir[2] * LASER_BEAM_SPEED;
  let traveled = 0;

  while (traveled <= LASER_MAX_RANGE) {
    px += velx * LASER_STEP;
    py += vely * LASER_STEP;
    pz += velz * LASER_STEP;
    vely += LASER_GRAVITY * LASER_STEP;
    traveled += LASER_STEP;

    const vmag = Math.sqrt(velx*velx + vely*vely + velz*velz) || 1;
    const ndir = [velx/vmag, vely/vmag, velz/vmag];

    playParticlesStandard(px, py, pz, ndir, LASER_PARTICLE_COUNT);

    const searchMin = [Math.floor(px - LASER_DAMAGE_RADIUS - 1), Math.floor(py - LASER_DAMAGE_RADIUS - 1), Math.floor(pz - LASER_DAMAGE_RADIUS - 1)];
    const searchMax = [Math.ceil(px + LASER_DAMAGE_RADIUS + 1), Math.ceil(py + LASER_DAMAGE_RADIUS + 1), Math.ceil(pz + LASER_DAMAGE_RADIUS + 1)];
    let hits = [];
    try { hits = api.getEntitiesInRect(searchMin, searchMax) || []; } catch (e) { hits = []; }

    if (hits.length > 0) {
      for (let h = 0; h < hits.length; h++) {
        const target = hits[h];
        if (target === playerId) continue;
        const tpos = api.getPosition(target);
        if (!tpos) continue;
        const dx = tpos[0] - px;
        const dy = tpos[1] - py;
        const dz = tpos[2] - pz;
        const dist = Math.sqrt(dx*dx + dy*dy + dz*dz);
        if (dist <= LASER_DAMAGE_RADIUS) {
          try {
            api.attemptApplyDamage({
              eId: playerId,
              hitEId: target,
              attemptedDmgAmt: LASER_DAMAGE,
              withItem: "Diamond Sword",
              attackDir: ndir,
              showCritParticles: true,
              reduceVerticalKbVelocity: false
            });
          } catch (e) { api.log("damage error: " + e); }
          return;
        }
      }
    }

    const bx = Math.floor(px);
    const by = Math.floor(py);
    const bz = Math.floor(pz);
    try {
      const block = api.getBlock([bx, by, bz]);
      if (block && block !== "Air") {
        playParticlesStandard(bx + 0.5, by + 0.5, bz + 0.5, ndir, LASER_PARTICLE_COUNT * 2);
        return;
      }
    } catch (e) {}
  }
};
```
