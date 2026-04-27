**Custom Color Nametags**
```js
api.setEveryoneSettingForPlayer(myId, "nameColour", "green", true)
```

**Change into any mob**
```js
et boundaries = 600*1000;
let entities = api.getEntitiesInRect([-boundaries,-boundaries,-boundaries],[boundaries,boundaries,boundaries]);

let _break = false;

entities.forEach((eid)=>{
    if(_break)return;
    if(api.getEntityType(eid)!=="Player" && api.getEntityType(eid)!=="Item") {
        console.log(myId, eid, api.getEntityType(eid))
        api.addFollowingEntityToPlayer(myId, eid);
        const nodes = ["TorsoNode","HeadMesh","ArmRightMesh","ArmLeftMesh","LegLeftMesh","LegRightMesh"];        let obj = {};        for (const node of nodes) {
            obj[node] = [0,0,0];            api.scalePlayerMeshNodes(myId, obj);            _break = true;
        }
    }
})
```

**Change name to arthur**
```js
const playerName = "Arthur";

const content = [];
for (let i = 0; i < playerName.length; i++) {
  content.push({
    str: playerName[i],
    style: {
      color: "orange",
      fontSize: "50px",
      fontWeight: "bold"
    }
  });
}

api.setTargetedPlayerSettingForEveryone(playerId, "nameTagInfo", {
  backgroundColor: "#002244",  // official Bloxd.io dark blue color
  content: content
});
```

**Custom Colorful Capes**
```js


const rainbowColors = [

    "Red Carpet", "Orange Carpet", "Yellow Carpet", "Lime Carpet", 

    "Green Carpet", "Cyan Carpet", "Blue Carpet", "Purple Carpet", "Pink Carpet",

    "Purple Carpet", "Blue Carpet", "Cyan Carpet", "Green Carpet", "Lime Carpet", 

    "Yellow Carpet", "Orange Carpet", "Red Carpet"

];


let playerCapeIndexes = {}; // Stores each player's current cape color index

let tickCounter = 0; // Track game ticks for timing


tick = () => {

    tickCounter++; // Increment tick count


    if (tickCounter % 5 === 0) { // Every 100 ticks (~5 sec)

        const playerIds = api.getPlayerIds(); // Get all active players


        for (let myId of playerIds) {

            playerCapeIndexes[myId] = (playerCapeIndexes[myId] || 0) + 1; // Cycle colors

            playerCapeIndexes[myId] %= rainbowColors.length; // Keep within bounds


            api.updateEntityNodeMeshAttachment(

                myId, 

                "TorsoNode",

                "BloxdBlock",

                { blockName: rainbowColors[playerCapeIndexes[myId]], size: 0.5, meshOffset: [0, 0, 0] },

                [0, 0.35, -0.3], 

                [-0.5, 3.15, 1.6]

            );

        }

    }

};

function onPlayerJoin(myId) {
    // Get the player's name
    const playerName = api.getEntityName(myId);
    
    // Check if the player is the owner (Koko_XD)
    if (playerName === "Koko_XD") /* <-- Change it to your username :3 !! */ {
        // Define rainbow colors in traditional order
        const colors = [
            "#FF0000", // Red
            "#FF4500", // Red-Orange
            "#FF8C00", // Dark Orange
            "#FFA500", // Orange
            "#FFD700", // Gold
            "#FFFF00", // Yellow
            "#ADFF2F", // Yellow-Green
            "#00FF00", // Green
            "#00FA9A", // Medium Spring Green
            "#00FFFF", // Cyan
            "#1E90FF", // Dodger Blue
            "#0000FF", // Blue
            "#4B0082", // Indigo
            "#8A2BE2", // Blue Violet
            "#9400D3", // Violet
            "#FF00FF"  // Magenta
        ];
        
        let styledMessage = [];
        const message = `Birthday Dude Joined! ${playerName}`;
        
        // Split the message into two parts: the rainbow part and the entity name
        const messageWithoutName = message.slice(0, message.length - playerName.length); // All but the entity name
        const entityName = playerName; // Entity name to be colored yellow
        
        // Pre-calculate how many characters we have in the rainbow part (excluding spaces)
        const nonSpaceChars = messageWithoutName.replace(/ /g, "").length;
        
        // Calculate how many colors we need per cycle to complete one smooth transition
        const colorStep = colors.length / nonSpaceChars;
        
        // Initialize color position for rainbow part
        let colorPosition = 0;
        
        // Add the rainbow effect for the message part before the entity name
        for (let char of messageWithoutName) {
            if (char !== " ") { 
                const exactColorIndex = colorPosition % colors.length;
                const lowerColorIndex = Math.floor(exactColorIndex);
                
                styledMessage.push({ str: char, style: { color: colors[lowerColorIndex] } });
                colorPosition += colorStep;
            } else {
                styledMessage.push({ str: " " }); // Keep spaces normal
            }
        }
        
        // Add the entity name in yellow
        for (let char of entityName) {
            styledMessage.push({ str: char, style: { color: "#FFFF00" } }); // Yellow color
        }
        
        // Broadcast the styled message to all players
        api.broadcastMessage(styledMessage);
    }
    // If player is not the owner, do nothing
}
```

**Auto Bridge Code**
```js
function tick() {
for (const id of api.getPlayerIds()) {
	if (["x_Drxth", "Itz_Pika_YT"].includes(api.getEntityName(id))){
		if (api.getHeldItem(id)){
			held = api.getHeldItem(id).name
			if (api.blockNameToBlockId(held, true)){
				pos = api.getPosition(id)
				api.setBlock(pos[0], pos[1]-1, pos[2], held)
				api.setBlock(pos[0]+1, pos[1]-1, pos[2], held)
				api.setBlock(pos[0]-1, pos[1]-1, pos[2], held)
				api.setBlock(pos[0]+1, pos[1]-1, pos[2]+1, held)
				api.setBlock(pos[0]-1, pos[1]-1, pos[2]+1, held)
				api.setBlock(pos[0]-1, pos[1]-1, pos[2]-1, held)
				api.setBlock(pos[0]+1, pos[1]-1, pos[2]-1, held)
				api.setBlock(pos[0], pos[1]-1, pos[2]-1, held)
				api.setBlock(pos[0], pos[1]-1, pos[2]+1, held)
			}
		}
	}
	}
}
```
**Fire Sword Code Block**
```js
api.giveItem(myId, "Gold Sword", 1, {
  customDisplayName: "Fire Sword",
  customDescription: "Inflicts Burning"
});
```
**Fire Sword world code**
```js
function onPlayerDamagingOtherPlayer(attacker, damaged, damage, item) {
  const held = api.getHeldItem(attacker);
  if (!held || !held.attributes || !held.attributes.customDisplayName) return;

  const name = held.attributes.customDisplayName;

  // 1. Fire Sword
  if (name === "Fire Sword") {
    api.applyEffect(damaged, "Poisoned", 5000, { icon: "" });
let [x, y, z] = api.getPosition(damaged)
y += 1
api.playParticleEffect({
    dir1: [-1, -1, -1],
    dir2: [1, 1, 1],
    pos1: [x, y, z],
    pos2: [x + 1, y + 1, z + 1],
    texture: "glint",
    minLifeTime: 1,
    maxLifeTime: 3,
    minEmitPower: 2,
    maxEmitPower: 2,
    minSize: 0.25,
    maxSize: 0.35,
    manualEmitCount: 20,
    gravity: [0, -10, 0],
    colorGradients: [
        {
            timeFraction: 0,
            minColor: [237, 210, 7, 1],
            maxColor: [247, 147, 15, 1],
        },
    ],
    velocityGradients: [
        {
            timeFraction: 0,
            factor: 1,
            factor2: 1,
        },
    ],
    blendMode: 1,
})
  }
};
```

**Super Picaxe Code Block**
```js
api.giveItem(myId, "Moonstone Pickaxe", 1, {
    customDisplayName: "Super Pickaxe"
});
```
**Super Pickaxe World Code**
```js
onPlayerChangeBlock = (playerId, x, y, z, fromBlock, toBlock, droppedItem, fromBlockInfo, toBlockInfo) => {
    if (toBlock === "Air") {
        if (api.getHeldItem(playerId)?.attributes?.customDisplayName === "Super Pickaxe") {
            for (let dx = -1; dx <= 1; dx++) {
                for (let dy = -1; dy <= 1; dy++) {
                    for (let dz = -1; dz <= 1; dz++) {
                        if (dx === 0 && dy === 0 && dz === 0) continue;
                        const newX = x + dx;
                        const newY = y + dy;
                        const newZ = z + dz;
                        const blockName = api.getBlock(newX, newY, newZ);
                        // Skip Air, Bedrock, and Code Block (same pattern as center block skip)
                        if (blockName === "Air" || blockName === "Bedrock" || blockName === "Code Block") continue;
                        api.setBlock(newX, newY, newZ, "Air");
                        api.createItemDrop(newX + 0.5, newY + 0.5, newZ + 0.5, blockName, 1, false, {});
                    }
                }
            }
        }
    }
}
```

**Teleporter Gun Code Block**
```js
api.giveItem(myId,"Aura XP Fragment",1,{ customDisplayName: "Portal Gun"})
```
**Teleporter Gun World Code**
```js
var playerPortals = {}, playerTpCooldown = {};

onPlayerClick = (playerId, wasAltClick) => {
    if (api.getHeldItem(playerId)?.attributes?.customDisplayName !== "Portal Gun") return;
    if (!playerPortals[playerId]) playerPortals[playerId] = {blue: null, red: null};
    let {dir, camPos} = api.getPlayerFacingInfo(playerId); if (!camPos?.x) camPos = {x: api.getPosition(playerId)[0], y: api.getPosition(playerId)[1] + 1.6, z: api.getPosition(playerId)[2]}; let norm = [dir[0], dir[1], dir[2]].map(d => d / Math.sqrt(dir[0]**2 + dir[1]**2 + dir[2]**2)); 
    for (let d = 0.02; d <= 20; d += 0.05) { let [x,y,z] = [Math.floor(camPos.x + norm[0]*d), Math.floor(camPos.y + norm[1]*d), Math.floor(camPos.z + norm[2]*d)]; let block = api.getBlock(x,y,z); if (block && block !== "Air") { let spaceFound = false, placeY = y + 1; let playerPos = api.getPosition(playerId); let [playerX, playerY, playerZ] = playerPos; let isBelow = playerY < y, isAbove = playerY > y, isNorth = playerZ < z, isSouth = playerZ > z, isWest = playerX < x, isEast = playerX > x; if (isBelow && api.getBlock(x, y - 1, z) === "Air") { spaceFound = true; placeY = y - 1; } else if (isAbove && api.getBlock(x, y + 1, z) === "Air") { spaceFound = true; placeY = y + 1; } else if (isNorth && api.getBlock(x, y, z - 1) === "Air") { spaceFound = true; placeY = y; z = z - 1; } else if (isSouth && api.getBlock(x, y, z + 1) === "Air") { spaceFound = true; placeY = y; z = z + 1; } else if (isWest && api.getBlock(x - 1, y, z) === "Air") { spaceFound = true; placeY = y; x = x - 1; } else if (isEast && api.getBlock(x + 1, y, z) === "Air") { spaceFound = true; placeY = y; x = x + 1; } else if (api.getBlock(x, y + 1, z) === "Air") { spaceFound = true; placeY = y + 1; } else if (api.getBlock(x, y - 1, z) === "Air") { spaceFound = true; placeY = y - 1; } else if (api.getBlock(x, y, z + 1) === "Air") { spaceFound = true; placeY = y; z = z + 1; } else if (api.getBlock(x, y, z - 1) === "Air") { spaceFound = true; placeY = y; z = z - 1; } else if (api.getBlock(x + 1, y, z) === "Air") { spaceFound = true; placeY = y; x = x + 1; } else if (api.getBlock(x - 1, y, z) === "Air") { spaceFound = true; placeY = y; x = x - 1; } if (!spaceFound) return api.sendMessage(playerId, "No space!"); if (!playerPortals[playerId].blue) { api.setBlock(x, placeY, z, "Blue Portal"); playerPortals[playerId].blue = [x, placeY, z]; api.playParticleEffect({dir1: [-1, -1, -1], dir2: [1, 1, 1], pos1: [x+0.5-0.5, placeY+0.5-1, z+0.5-0.5], pos2: [x+0.5+0.5, placeY+0.5+1, z+0.5+0.5], texture: "glint", minLifeTime: 2, maxLifeTime: 2, minEmitPower: 3, maxEmitPower: 10, minSize: 0.25, maxSize: 0.25, manualEmitCount: 50, gravity: [0, 0, 0], colorGradients: [{timeFraction: 0.0, minColor: [0, 0, 255, 1], maxColor: [0, 0, 255, 1]}, {timeFraction: 1.0, minColor: [0, 0, 255, 1], maxColor: [0, 0, 255, 1]}], velocityGradients: [{timeFraction: 0, factor: 1, factor2: 1}], blendMode: 1}); api.playSound(playerId, "submachine_magazine_unload_01", 1, 1.5); } else { if (playerPortals[playerId].red) api.setBlock(playerPortals[playerId].red[0], playerPortals[playerId].red[1], playerPortals[playerId].red[2], "Air"); api.setBlock(x, placeY, z, "Orange Portal"); playerPortals[playerId].red = [x, placeY, z]; api.playParticleEffect({dir1: [-1, -1, -1], dir2: [1, 1, 1], pos1: [x+0.5-0.5, placeY+0.5-1, z+0.5-0.5], pos2: [x+0.5+0.5, placeY+0.5+1, z+0.5+0.5], texture: "glint", minLifeTime: 2, maxLifeTime: 2, minEmitPower: 3, maxEmitPower: 10, minSize: 0.25, maxSize: 0.25, manualEmitCount: 50, gravity: [0, 0, 0], colorGradients: [{timeFraction: 0.0, minColor: [255, 0, 0, 1], maxColor: [255, 0, 0, 1]}, {timeFraction: 1.0, minColor: [255, 0, 0, 1], maxColor: [255, 0, 0, 1]}], velocityGradients: [{timeFraction: 0, factor: 1, factor2: 1}], blendMode: 1}); api.playSound(playerId, "submachine_magazine_unload_01", 1, 1.5); } return; } }
    api.sendMessage(playerId, "No block found in your line of sight (checked up to 20 blocks)");
}

function findPortalOwner(x, y, z) {
    for (const ownerId in playerPortals) { const portals = playerPortals[ownerId]; if (portals.blue && portals.blue[0] === x && portals.blue[1] === y && portals.blue[2] === z) return { ownerId, color: 'blue' }; if (portals.red && portals.red[0] === x && portals.red[1] === y && portals.red[2] === z) return { ownerId, color: 'red' }; }
    return null;
}

tick = () => {
    for (const playerId of api.getPlayerIds()) { if (playerTpCooldown[playerId] > 0) playerTpCooldown[playerId]--; if (playerTpCooldown[playerId] > 0) continue; const coordsArray = api.getBlockCoordinatesPlayerStandingOn(playerId); if (coordsArray.length > 0) { const [x, y, z] = coordsArray[0]; const portalInfo = findPortalOwner(x, y, z); if (portalInfo) { const { ownerId, color } = portalInfo; const ownerPortals = playerPortals[ownerId]; if (!ownerPortals.blue || !ownerPortals.red) continue; const targetPortal = color === 'blue' ? ownerPortals.red : ownerPortals.blue; if (targetPortal) { let [px, py, pz] = targetPortal; let tpPos = null; if (api.getBlock(px, py + 1, pz) === "Air" && api.getBlock(px, py + 2, pz) === "Air") tpPos = [px + 0.5, py + 1, pz + 0.5]; else if (api.getBlock(px, py - 1, pz) === "Air" && api.getBlock(px, py - 2, pz) === "Air") tpPos = [px + 0.5, py - 2, pz + 0.5]; else if (api.getBlock(px, py, pz + 1) === "Air" && api.getBlock(px, py + 1, pz + 1) === "Air") tpPos = [px + 0.5, py, pz + 1.5]; else if (api.getBlock(px, py, pz - 1) === "Air" && api.getBlock(px, py + 1, pz - 1) === "Air") tpPos = [px + 0.5, py, pz - 0.5]; else if (api.getBlock(px + 1, py, pz) === "Air" && api.getBlock(px + 1, py + 1, pz) === "Air") tpPos = [px + 1.5, py, pz + 0.5]; else if (api.getBlock(px - 1, py, pz) === "Air" && api.getBlock(px - 1, py + 1, pz) === "Air") tpPos = [px - 0.5, py, pz + 0.5]; if (tpPos) { api.setPosition(playerId, tpPos[0], tpPos[1], tpPos[2]); playerTpCooldown[playerId] = 10; api.playSound(playerId, "cloth", 1, 1.5); } else api.sendMessage(playerId, "No space to teleport!"); } } } }
}
```

**Snowman skin Code**
```js
api.scalePlayerMeshNodes(myId, {
  "TorsoNode":[.5,.5,.5],
  "HeadMesh":[2,2,2],
  "LegRightMesh":[.5,.5,.5],
  "LegLeftMesh":[.5,.5,.5]
})
api.updateEntityNodeMeshAttachment(myId, "HeadMesh", "BloxdBlock", {blockName:"Carved Pumpkin", size:0.6, meshOffset:[0, 0, 0]}, [0, 0.25, 0], [3.14, 0, 0])
api.updateEntityNodeMeshAttachment(myId, "TorsoNode", "BloxdBlock", {blockName:"Snow", size:0.9, meshOffset:[0, 0, 0]}, [0, 0.2, 0], [0, 0, 0])
api.updateEntityNodeMeshAttachment(myId, "LegRightMesh", "BloxdBlock", {blockName:"Snow", size:1.1, meshOffset:[0, 0, 0]}, [0.22, -0.8, 0], [0, 0, 0])
api.updateEntityNodeMeshAttachment(myId, "LegLeftMesh", "BloxdBlock", {blockName:"Block of Coal", size:0.1, meshOffset:[0, 0, 0]}, [-0.2, 0.4, 0.45], [0, 0, 0])
api.updateEntityNodeMeshAttachment(myId, "ArmRightMesh", "BloxdBlock", {blockName:"Dangling Rope", size:0.5, meshOffset:[0, 0, 0]}, [0, -0.5, 0], [0, 0, 2])
api.updateEntityNodeMeshAttachment(myId, "ArmLeftMesh", "BloxdBlock", {blockName:"Dangling Rope", size:0.5, meshOffset:[0, 0, 0]}, [0, -0.5, 0], [0, 0, -2])
```

more can get added after im not to **LAZY**
