### World Code
```js
// ================== SETTINGS ==================
let fallTimeSeconds = 2.1
let fallTicks = fallTimeSeconds * 20

let scheduledExplosions = []
let fallingExplosives = []
let despawnQueue = []
let pendingDespawn = 0

// ================== TICK ==================
tick = () => {
  pendingDespawn++

  if (pendingDespawn === 3 && despawnQueue.length) {
    for (let m of despawnQueue) api.despawnMob(m)
    despawnQueue = []
  }

  // Delayed explosions
  for (let i = scheduledExplosions.length - 1; i >= 0; i--) {
    let e = scheduledExplosions[i]
    e.ticks--
    if (e.ticks <= 0) {
      createExplosion(e.pos, e.strength)
      scheduledExplosions.splice(i, 1)
    }
  }

  // Falling TNT
  for (let i = fallingExplosives.length - 1; i >= 0; i--) {
    let f = fallingExplosives[i]

    api.setBlock(f.pos[0], f.pos[1], f.pos[2], "Air")
    f.pos[1]--
    api.setBlock(f.pos[0], f.pos[1], f.pos[2], "Moonstone Explosive")

    f.ticks++
    if (f.ticks >= fallTicks) {
      api.setBlock(f.pos[0], f.pos[1], f.pos[2], "Air")
      createExplosion(f.pos, f.strength)
      fallingExplosives.splice(i, 1)
    }
  }
}

// ================== EXPLOSION ==================
function createExplosion(pos, strength = 2) {
  let m = api.attemptSpawnMob("Draugr Zombie", pos[0], pos[1], pos[2])

  api.setMobSetting(m, "attackItemName", strength == 1 ? "RPG" : "Super RPG")
  api.setMobSetting(m, "attackRadius", 1e6)
  api.setMobSetting(m, "attackInterval", 1e6)
  api.setMobSetting(m, "baseWalkingSpeed", 0)
  api.setMobSetting(m, "baseRunningSpeed", 0)
  api.setMobSetting(m, "attackImpulse", 0)
  api.setPlayerOpacity(m, 0)

  let p = api.attemptSpawnMob("Draugr Zombie", pos[0], pos[1] - 2, pos[2])
  api.setMobSetting(p, "baseWalkingSpeed", 0)
  api.setMobSetting(p, "baseRunningSpeed", 0)
  api.setMobSetting(p, "initialHealth", 1e6)
  api.setPlayerOpacity(p, 0)

  api.applyMeleeHit(p, m, [0, 0, 0])

  despawnQueue.push(m, p)
  pendingDespawn = 0
}

// ================== ARROW TNT ==================
onPlayerThrowableHitTerrain = (player, arrowName, arrowEntity) => {

  // 💥 EXPLOSION ARROW
  if (arrowName === "Arrow of Aura XP") {
    let pos = api.getPosition(arrowEntity)
    api.setPosition(arrowEntity, 0, -999, 0)

    let rings = [0, 7, 12, 16]

    for (let r of rings) {
      for (let i = 0; i < 16; i++) {
        let a = Math.PI * 2 * i / 16
        let x = Math.round(Math.cos(a) * r)
        let z = Math.round(Math.sin(a) * r)

        let p = [pos[0] + x, pos[1], pos[2] + z]

        fallingExplosives.push({
          pos: [p[0], p[1] + 40, p[2]],
          ticks: 0,
          strength: 2
        })

        scheduledExplosions.push({
          pos: p,
          strength: 2,
          ticks: fallTicks
        })
      }
    }
  }

  // 🧱 VERTICAL TNT COLUMN
  if (arrowName === "Arrow of Shield") {
    let pos = api.getPosition(arrowEntity)
    api.setPosition(arrowEntity, 0, -999, 0)

    let heights = [-4,-8,-12,-16,-20,-24,-28,-32,-36,-40]

    scheduledExplosions.push({ pos, strength: 2, ticks: 0 })

    for (let h of heights) {
      scheduledExplosions.push({
        pos: [pos[0], pos[1] + h, pos[2]],
        strength: 2,
        ticks: 0
      })
    }
  }
}

// ================== MOB SPAWN BLOCK ==================
onWorldAttemptSpawnMob = mobId => {
  if (mobId !== "Draugr Zombie") return "preventSpawn"
}
```
### Code Block 1
```js
api.giveItem(myId,"Diamond Crossbow",1,{customDisplayName:"Nuke Shot"})
```
### Code Block 2
```js
api.giveItem(myId,"Diamond Crossbow",1,{customDisplayName:"Stab Shot"})
```
