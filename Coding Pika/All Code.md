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

**Green theme sky**
```js
api.setClientOption(myId, "skyBox", {
        type: "earth",           // Use realistic sky model
        inclination: 2.4,        // Sun higher in the sky
        azimuth: 1.2,            // Direction of the sun
        turbidity: 12,            // Clear sky with soft clouds
        luminance: 21.5,          // Brighter lighting
        vertexTint: [12, 237, 121] // Cool blue tint (crystal morning)
    })
```

**Crystal Morning Sky**
```js
api.setClientOption(myId, "skyBox", {
        type: "earth",           // Use realistic sky model
        inclination: 2.4,        // Sun higher in the sky
        azimuth: 1.2,            // Direction of the sun
        turbidity: 12,            // Clear sky with soft clouds
        luminance: 20.5,          // Brighter lighting
        vertexTint: [125, 37, 191] // Cool blue tint (crystal morning)
    })
```

**Lunar Sky**
```js
api.setClientOption(myId, "skyBox", {
        type: "earth",           // Use realistic sky model
        inclination: 2.4,        // Sun higher in the sky
        azimuth: 1.2,            // Direction of the sun
        turbidity: 12,            // Clear sky with soft clouds
        luminance: 21.5,          // Brighter lighting
        vertexTint: [12, 237, 121] // Cool blue tint (crystal morning)
    })
```
**CPS COUNTER CODE**
```js
const clickTimestamps = {};
const cpsData = {};

onPlayerClick = (id) => {
  const now = Date.now();
  if (!clickTimestamps[id]) clickTimestamps[id] = [];
  clickTimestamps[id].push(now);
};

tick = () => {
  const now = Date.now();
  const cutoff = now - 1000;
  const players = api.getPlayerIds();

  for (const id of players) {
    if (!clickTimestamps[id]) clickTimestamps[id] = [];
    const timestamps = clickTimestamps[id];
    while (timestamps.length > 0 && timestamps[0] < cutoff) {
      timestamps.shift();
    }
    cpsData[id] = timestamps.length;
  }

  for (const id of players) {
    api.setClientOption(id, "middleTextLower", [
      {
        str: "CPS: ",
        style: { color: "#ffffff", fontWeight: "bold", fontSize: "12px" },
      },
      {
        str: cpsData[id] + ".0\n",
        style: { fontWeight: "bold", color: "#ffff00", fontSize: "12px" },
      },
    ]);
  }
};
```
**Allow anybody for using code**
```js
api.setClientOption(api.getPlayerId("x_Drxth"), "canEditCode", true)
```
**Note**:You Can Change **x_Drxth** to your name or your friends.

**Grapling Hook Code :**
```js
//by x_Drxth & Itz_Pika_YT

(function(_0x35948c,_0x2d07aa){var _0x18dde6=_0x56ce,_0x2242b0=_0x35948c();while(!![]){try{var _0x1cabcb=parseInt(_0x18dde6(0x113))/0x1*(-parseInt(_0x18dde6(0x11e))/0x2)+parseInt(_0x18dde6(0x11d))/0x3*(-parseInt(_0x18dde6(0x118))/0x4)+-parseInt(_0x18dde6(0x115))/0x5+parseInt(_0x18dde6(0x117))/0x6*(-parseInt(_0x18dde6(0x11a))/0x7)+-parseInt(_0x18dde6(0x119))/0x8+parseInt(_0x18dde6(0x121))/0x9+parseInt(_0x18dde6(0x116))/0xa*(parseInt(_0x18dde6(0x11b))/0xb);if(_0x1cabcb===_0x2d07aa)break;else _0x2242b0['push'](_0x2242b0['shift']());}catch(_0xa9b512){_0x2242b0['push'](_0x2242b0['shift']());}}}(_0x2680,0xde79f));function onPlayerUsedThrowable(_0x3cea05,_0x19acec,_0x10d66c){pPos=api['getPosition'](_0x3cea05);}var x,p,pPos,y,z,ePos;function _0x2680(){var _0x55c401=['40220090vShHPu','3160788AjbNuE','92gwmIZN','7148976bQbVdH','7KpJDHg','11xWXAdc','getPosition','28479jQnAYG','12owoZSk','sendMessage','floor','4831254obAcPO','42009ezshai','setPosition','8783695JHFiUa'];_0x2680=function(){return _0x55c401;};return _0x2680();}function onPlayerJoin(_0x203a3c){var _0x1047e6=_0x56ce;api[_0x1047e6(0x11f)](_0x203a3c,'Welcome,\x20this\x20grappling\x20hook\x20code\x20is\x20made\x20x_Drxth');}function onPlayerThrowableHitTerrain(_0x3de9a9,_0x323008,_0x46a3ff){var _0x429f10=_0x56ce;p=_0x3de9a9,ePos=api['getPosition'](_0x46a3ff),pPos=api[_0x429f10(0x11c)](_0x3de9a9),dxp=0x0,dg=0x0,g=Math[_0x429f10(0x120)](ePos[0x0]),j=Math[_0x429f10(0x120)](ePos[0x1]),l=Math[_0x429f10(0x120)](ePos[0x2]),e=Math[_0x429f10(0x120)](pPos[0x0]),k=Math[_0x429f10(0x120)](pPos[0x1]),i=Math[_0x429f10(0x120)](pPos[0x2]),x=g-e,y=j-k,z=l-i;}var dxp=0x0,dz=0x0,dy=0x0;function _0x56ce(_0x545ced,_0x23f7b8){var _0x268086=_0x2680();return _0x56ce=function(_0x56ce53,_0x821c7){_0x56ce53=_0x56ce53-0x113;var _0x2dd931=_0x268086[_0x56ce53];return _0x2dd931;},_0x56ce(_0x545ced,_0x23f7b8);}function tick(){var _0x143796=_0x56ce;timer++,timer%0x1==0x0&&(x>0x0?dxp<x&&api[_0x143796(0x11c)](p)!=ePos&&(dxp+=0x1,api[_0x143796(0x114)](p,pPos[0x0]+dxp,pPos[0x1]+y,pPos[0x2]+dz)):dxp>x&&api[_0x143796(0x11c)](p)!=ePos&&(dxp-=0x1,api[_0x143796(0x114)](p,pPos[0x0]+dxp,pPos[0x1]+dy,pPos[0x2]+dz)),z>0x0?dz<z&&api['getPosition'](p)!=ePos&&(dz+=0x1,api['setPosition'](p,pPos[0x0]+dxp,pPos[0x1]+dy,pPos[0x2]+dz)):dz>z&&api['getPosition'](p)!=ePos&&(dz-=0x1,api[_0x143796(0x114)](p,pPos[0x0]+dxp,pPos[0x1]+dy,pPos[0x2]+dz)),y>0x0?dy<y&&api[_0x143796(0x11c)](p)!=ePos&&(dy+=0x1,api[_0x143796(0x114)](p,pPos[0x0]+dxp,pPos[0x1]+dy,pPos[0x2]+dz)):dy>y&&api[_0x143796(0x11c)](p)!=ePos&&(dy-=0x1,api[_0x143796(0x114)](p,pPos[0x0]+dxp,pPos[0x1]+dy,pPos[0x2]+dz)));}timer=0x0;
```

**Snow Fall Code**
```js
let [x, y, z] = thisPos
y += 1
api.playParticleEffect({
    dir1: [1, 1, 1],
    dir2: [-1, -1,-1],
    pos1: [x - 100, y - 50, z - 100],
    pos2: [x + 100, y + 150, z + 100],
    texture: "critical_hit",
    minLifeTime: 10,
    maxLifeTime: 20,
    minEmitPower: 2,
    maxEmitPower: 2,
    minSize: 0.25,
    maxSize: 0.35,
    manualEmitCount: 20000,
    gravity: [0, -1.7, 0],
    colorGradients: [
        {
            timeFraction: 0,
            minColor: [255, 255, 255, 1],
            maxColor: [230, 230, 230, 1],
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
```

**Aura from body**
```js
api.updateEntityNodeMeshAttachment(
  playerId,
  "TorsoNode",
  "ParticleEmitter",
  {
    emitRate: 20,
    meshOffset: 20,
    width: 1,
    height: 1,
    depth: 1,
    //dir: [1, 0, 0],
    texture: "glint",
    minLifeTime: 0.3,
    maxLifeTime: 0.4,
    minEmitPower: 1,
    maxEmitPower: 1,
    minSize: 0.2,
    maxSize: 0.4,
    manualEmitCount: 70,
    gravity: [0, 0, 0],
    colorGradients: [
      {
        timeFraction: 0,
        minColor: [255, 0, 79],
        maxColor: [255, 0, 80],
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
  },
  [0, 0, 0]
);
```

**Beatiful Hearts Code**
```js
let [x, y, z] = thisPos; // Destructures the position into x, y, z coordinates
y += 1; // Increases the y-coordinate slightly, perhaps to emit particles above the source

api.playParticleEffect({
    // Motion parameters remain the same for up-and-down movement
    dir1: [-1, 0.5, -1], // Minimum direction vector: biased upwards in Y
    dir2: [1, 1.5, 1],   // Maximum direction vector: stronger upward bias in Y
    pos1: [x, y, z],   // Starting position for particle emission
    pos2: [x + 1, y + 1, z + 1], // Ending position for particle emission
    texture: "heart",  // Using "heart" for the particle texture
    minLifeTime: 1.2,  // Minimum duration (in seconds) a particle will exist (slightly increased for more color transition time)
    maxLifeTime: 2.5,  // Maximum duration (in seconds) a particle will exist (slightly increased for more color transition time)
    minEmitPower: 5.8, // Minimum initial speed/power of emitted particles (slightly increased for higher arcs)
    maxEmitPower: 6.8, // Maximum initial speed/power of emitted particles (slightly increased for higher arcs)
    minSize: 0.5,      // Minimum size of the particle
    maxSize: 0.8,      // Maximum size of the particle
    manualEmitCount: 40, // Number of particles to emit in this burst (increased for a fuller effect)
    gravity: [0, 0, 0], // Gravity vector affecting particles (slightly reduced for a more floaty effect)

    // *** Enhanced colorGradients for beautiful, multi-stage color transitions ***
    colorGradients: [
        {
            timeFraction: 0, // At the very beginning of the particle's life (Red/Pink)
            minColor: [255, 50, 100, 1],  // Deep red
            maxColor: [255, 100, 150, 1], // Vibrant pink
        },
        {
            timeFraction: 0.25, // After 25% of its life (Orange/Yellow)
            minColor: [255, 150, 0, 1],   // Bright orange
            maxColor: [255, 200, 50, 1],  // Warm yellow
        },
        {
            timeFraction: 0.5, // At 50% of its life (Green/Cyan) - peak of the arc, potentially
            minColor: [50, 200, 50, 0.9], // Fresh green
            maxColor: [100, 255, 150, 0.9], // Soft cyan
        },
        {
            timeFraction: 0.75, // After 75% of its life (Blue/Purple)
            minColor: [0, 100, 255, 0.7], // Sky blue
            maxColor: [150, 50, 255, 0.7], // Light purple
        },
        {
            timeFraction: 1, // At the very end of its life (Fade out with a touch of color)
            minColor: [255, 0, 150, 0],   // Fading red/magenta, fully transparent
            maxColor: [255, 50, 200, 0],  // Fading pink/purple, fully transparent
        },
    ],
    velocityGradients: [ // Defines how particle velocity changes over its lifetime
        {
            timeFraction: 0, // At the beginning
            factor: 1,       // Full initial velocity
            factor2: 1,
        },
        {
            timeFraction: 0.5, // At the end
            factor: 0.1,     // Slow down almost to a stop
            factor2: 0.1,
        },

        {
            timeFraction: 1, // At the end
            factor: 1,     // Slow down almost to a stop
            factor2: 1,
        },
    ],
    blendMode: 0, // Blending mode for particles (e.g., additive blending)
});
```

**Instant Aura Code**
```js
api.setAuraLevel(myId, 300)
```
**Damage V(5) Sword**
```js
api.giveItem(
  myId,
  "Diamond Sword",
  1,
  {
    customAttributes: {
      enchantments: {
        "Damage": 5,
        "Attack Speed": 5,
        "Critical Damage": 5
      },
      enchantmentTier: "Tier 5"
    }
  }
);
```

**New nether portal outlook code**
```js
let [x, y, z] = thisPos

y += 1

api.playParticleEffect({

dir1: [0,0,0],

dir2: [0,0,0],

pos1: [x + 4.5, y + 3.5, z + 0.5],

pos2: [x + 1.5, y - 1.5, z + 0.5],

texture: "square_particle",

minLifeTime: 99999999999,

maxLifeTime: 99999999999,

minEmitPower: 2,

maxEmitPower: 2,

minSize: 0.5,

maxSize: 0.6,

manualEmitCount: 1000,

gravity: [0, 0, 0],

colorGradients: [

{

timeFraction: 0,

minColor: [40, 0, 300, 1],

maxColor: [50, 15, 300, 1],

},

],

velocityGradients: [

{

timeFraction: 0,

factor: 1,

factor2: 1,

},

],

blendMode: 4,

})
```
**Custom Capes code**
```js
api.updateEntityNodeMeshAttachment(myId, "TorsoNode", "BloxdBlock", {blockName:"Red Carpet", size:0.5, meshOffset:[0, 0, 0]}, [0, 0.35, -0.3], [-0.4, 3.15, 0.0])
```
**Mace code**
```js
onPlayerDamagingOtherPlayer = (damagedId, pid, d, w)=>{

  if(w==="Moonstone Axe"){api.applyHealthChange(pid, -30)

api.applyEffect(pid,"Slowness",500,{inbuiltLevel:2})};

}

onPlayerJump=(pid)=>{let item=api.getHeldItem(pid)

    if(item && item.name==="Moonstone Axe")

api.setVelocity(pid,0,19,0)}
```
**Note**:You Can Search In **[Here](https://www.bloxdhub.com/mods)** for another Mace Code!

**Aura player code**
```js
const dealMeleeDamage = (hittingEId) => {

    const playerPosition = api.getPosition(hittingEId);

    const playerIds = api.getPlayerIds();



    for (const targetId of playerIds) {

        if (targetId !== hittingEId) {

            const targetPosition = api.getPosition(targetId);

            const distance = Math.sqrt(

                Math.pow(playerPosition[0] - targetPosition[0], 2) +

                Math.pow(playerPosition[1] - targetPosition[1], 2) +

                Math.pow(playerPosition[2] - targetPosition[2], 2)

            );



            if (distance < 5) {

                api.applyMeleeHit(hittingEId, targetId, [0, 0, 0]); // Direction can be adjusted;

            }

        }

    }

};

onPlayerClick = (playerId) => {

        dealMeleeDamage(playerId);

};
```
**Enchanted Apple Code**
```js
onPlayerFinishChargingItem=(id)=>{
let item=api.getHeldItem(id)

    if(item && item.name==="Apple"){



api.applyEffect(id,"Health Regen",10000,{inbuiltLevel:2})

api.applyEffect(id,"Speed",50000,{inbuiltLevel:2})

api.applyEffect(id,"Damage Reduction",50000,{inbuiltLevel:2})

api.applyEffect(id,"Damage",30000,{inbuiltLevel:2})

api.setShieldAmount(id,200)

const oldHealth = api.getHealth(id)

if(oldHealth<130){

api.setHealth(id,oldHealth+30,undefined,true)}}}





2. Laser beam code



onPlayerClick = (id) =>{

    let facing = api.getPlayerFacingInfo(id).dir;

    let pos = api.getPosition(id);

let amount = 8;

let held = api.getHeldItem(id);

let name = held?.attributes?.customDisplayName;

if (name == "Laser"){

    api.broadcastSound("cannonFire3",1,5,{

        playerIdOrPos: id,

        maxHearDist: 15

    });

    for (let i = 0; i < amount; i++){

        let hit = [];

        hit = [];

api.playParticleEffect({

    dir2: [1, 0, 1],

    dir1: [facing[0], facing[0], facing[2]],

    pos1: [pos[0] += ((i+2)*facing[0]) , pos[1] +1, pos[2] += ((i+2)*facing[2])],

    pos2: [pos[0] += ((i+2)*facing[0]), pos[1] +1, pos[2] +=((i+2)*facing[2])],

    texture: "glint",

    minLifeTime: 0.05 + (i/100),

    maxLifeTime: 0.05 + (i/100),

    minEmitPower: 2,

    maxEmitPower: 2,

    minSize: 0.5,

    maxSize: 3 - (i/10),

    manualEmitCount: 10,

    gravity: [0, -2, 0],

    colorGradients: [

        {

            timeFraction: 0,

            minColor: [255, 81, 0, 1],

            maxColor: [253, 3, 3, 1],

            //rgb(255, 81, 0)

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

});

const magnitude = Math.sqrt(

    facing[0] * facing[0] +

    facing[1] * facing[1] +

    facing[2] * facing[2]

);

const normalized = [

    facing[0] / magnitude,

    facing[1] / magnitude,

    facing[2] / magnitude

];

let x = Math.floor(pos[0] + normalized[0]* i);

            let z = Math.floor(pos[2] + normalized[2] * i);

            let y = Math.floor(pos[1]);

hit = api.getEntitiesInRect([x- 3,y - 3,z - 3],[x + 3,y + 3,z + 3]);



if (hit){

for (let h = 0; h < hit.length; h++){

    let target = hit[h];

    if (hit[h] != id){

    api.attemptApplyDamage({

        eId: id,

        hitEId: target,

        attemptedDmgAmt: 100,

        withItem: "Red Ceramic",

        attackDir: [facing[0],facing[1],facing[2]],

        showCritParticles : true,

        reduceVerticalKbVelocity : false

    });

}

}

}

    }

}

}
```
**Morph into any block code**
```js
api.updateEntityNodeMeshAttachment(myId, "TorsoNode", "BloxdBlock", {blockName:"Protector", size:1, meshOffset:[0, -0.0111, 0]}, [0, -0.122, 0], [0, -0.0111, 0]) api.scalePlayerMeshNodes(myId, { "TorsoNode":[0.99,0.99,0.99], "HeadMesh":[0.0,0.0,0.0,], "ArmRightMesh":[0.0,0.0,0.0], "ArmLeftMesh":[0.0,0.0,0.0], "LegLeftMesh":[0.0,0.0,0.0], "LegRightMesh":[0.0,0.0,0.0] }); api.applyEffect(myId,"Invisible",null,{inbuiltLevel:9999999999999999999})
```
**Note**:You Can Change **Protector** To any block you want!

**Hacker Code(I dont know why its call that)**
```js
let player1= api.getPlayerId("SHADOW_WARRIOR45")

let player2= api.getPlayerId("Itz_Pika_YT")



let timeToSwap = 150



//Hidden variables

var tmr = 0





function tick(dt){

tmr += 1

pos1 = api.getPosition(player1)

pos2 = api.getPosition(player2)







if(tmr >timeToSwap){

api.setPosition(player1,pos2[0],pos2[1],pos2[2])

api.setPosition(player2,pos1[0],pos1[1],pos1[2])



api.broadcastMessage("swap sucess")

tmr = 0;

}

}

//thew above code is for death swap

let PLAYER_NAME = "Owner257";

PLAYER_NAME = "SHADOW_WARRIOR45";

let last_tick_time = 0;

ordinary_tick_function=()=>{

    if(Date.now()-last_tick_time<1000)return ;//the code should only run once every second

    last_tick_time=Date.now();

    let ownerId = api.getPlayerId(PLAYER_NAME);

    if(!ownerId)return ;

    api.getPlayerIds().forEach(pid=>{

        let item = api.getHeldItem(pid);

        if(item!==null && item.name.substr(0,7)==="Compass") {

            let attributes = item.attributes.customAttributes;

            if(attributes.tracksOwner257===true) {

                let userPos = api.getPosition(ownerId);

                let slotI = api.getSelectedInventorySlotI(pid);

                attributes.target = userPos;

                api.setItemSlot(pid, slotI, "Compass", null, {

                    customDisplayName:item.attributes.customName,

                    customDescription:item.attributes.customName,

                    customAttributes:attributes,

                })

            }

        }

})

}

onPlayerJump=(id)=>{let item=api.getHeldItem(id)

    if(item && item.name==="Moonstone Axe")api.setVelocity(id,0,20,0)}







/* tick onClose onPlayerJoin onPlayerLeave onPlayerJump onRespawnRequest

playerCommand onPlayerChat onPlayerChangeBlock onPlayerDropItem

onPlayerPickedUpItem onPlayerSelectInventorySlot onBlockStand

onPlayerAttemptCraft onPlayerCraft onPlayerAttemptOpenChest

onPlayerOpenedChest onPlayerMoveItemOutOfInventory onPlayerMoveInvenItem

onPlayerMoveItemIntoIdxs onPlayerSwapInvenSlots onPlayerMoveInvenItemWithAmt

onPlayerAttemptAltAction onPlayerAltAction onPlayerClick

onClientOptionUpdated onInventoryUpdated onChestUpdated onWorldChangeBlock

onCreateBloxdMeshEntity onEntityCollision onPlayerAttemptSpawnMob

onWorldAttemptSpawnMob onPlayerSpawnMob onWorldSpawnMob onMobDespawned

onPlayerAttack onPlayerDamagingOtherPlayer onPlayerDamagingMob

onPlayerKilledOtherPlayer onMobKilledPlayer onPlayerKilledMob

onPlayerPotionEffect onPlayerDamagingMeshEntity onPlayerBreakMeshEntity

onPlayerUsedThrowable onPlayerThrowableHitTerrain onTouchscreenActionButton

onTaskClaimed onChunkLoaded onPlayerRequestChunk onItemDropCreated

onPlayerStartChargingItem onPlayerFinishChargingItem doPeriodicSave



To use a callback, just assign a function to it in the world code!

tick = () => {} or function tick() {}

*/

const dealMeleeDamage = (hittingEId) => {

    const playerPosition = api.getPosition(hittingEId);

    const playerIds = api.getPlayerIds();



    for (const targetId of playerIds) {

        if (targetId !== hittingEId) {

            const targetPosition = api.getPosition(targetId);

            const distance = Math.sqrt(

                Math.pow(playerPosition[0] - targetPosition[0], 2) +

                Math.pow(playerPosition[1] - targetPosition[1], 2) +

                Math.pow(playerPosition[2] - targetPosition[2], 2)

            );



            if (distance < 500) {

                api.applyMeleeHit(hittingEId, targetId, [100, 20, -200]); // Direction can be adjusted;

            }

        }

    }

};

onBlockStand = (playerId) => {

        dealMeleeDamage(playerId);

};



api.log("World code callbacks go here!")



tick=()=>{

    let players = api.getPlayerIds();

    if(players.length===0)return ;

    let pid = players[Math.floor(Math.random()*players.length)];

    let item = api.getHeldItem(pid);

    if(item!==null && item.attributes?.customAttributes?.shotsLeft>=1){

        item.attributes.customAttributes.shotsLeft=100000;

        api.setItemSlot(pid, api.getSelectedInventorySlotI(pid), item.name, null, item.attributes);

    }

}
```
**Note**:You can change **SHADOW_WARRIOR45**,**Itz_Pika_YT**,**Owner257** to any player you want maybe your friend?

More later im to **Lazy** Sorry.
