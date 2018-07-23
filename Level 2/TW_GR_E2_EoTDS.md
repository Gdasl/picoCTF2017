# TW_GR_E2_EoTDS

### Examining the code

Based on the 1st challenge (which I didn't solve from scrath hence why no write-up), we know exactly what and where the interesting files are:
- game.js
- config.js

In the first challenge the flag could be picked up as an item but would only be revealed if activated in the right spot. Examining game.js, we see the following code:

```js
case "revealFlag":
 outcome.flag = process.env["PICO_CTF_FLAG"];
 break;
```

Hm, no check is being made here. Let's go and see what's in config.js. Aha, look at this:
```js
items: [createFlag({ r: 4, c: 7 })],
				player: {
					location: {
						r: 8,
						c: 1
					}
				}
 ```
 It looks like a flag is created as soon as the player enters the 4th floor. Let's look at the outline of the level:
 ```
[ -4,  -3,  -3,  -3,  -3,  -3,  -3,  -3,  -3,  -3,  -2],
[ -5,   1,   1,   1,   1,   1,   1,   1,   1,   1,  -1],
[ -5,   1, -19, -19, -19, -19, -19, -19, -19,   1,  -1],
[ -5,   1, -19,   0, -19,   0,   0,   0, -19,   1,  -1],
[ -5,   1, -19,   0,   0,   0, -19,   0, -19,   1,  -1],
[ -5,   1, -19,   0, -19, -19, -19, -19, -19,   1,  -1],
[ -5,   1, -19,   0,   0,   0,   0,   0,  99,   1,  -1],
[ -5,   1, -19, -19, -19, -19, -19, -19, -19,   1,  -1],
[ -5,   1,   1,   1,   1,   1,   1,   1,   1,   1,  -1],
[ -5,   1,   1,   1,   1,   1,   1,   1,   1,   1,  -1],
[ -6,  -7,  -7,  -7,  -7,  -7,  -7,  -7,  -7,  -7,  -8]
```
Right away we see a curious structure in the middle: a labyrinth which - surprise - end in position (4,7=, the location of the newly created flag. [-19] is a wall so impossible to get. But there's a 99 hidden in there. Game.js reveals the following snippet:
```js
if(entity.location.r < 0
	   || entity.location.r >= state.map.height
	   || entity.location.c < 0
	   || entity.location.c >= state.map.width
	   || state.map.grid[entity.location.r][entity.location.c] < 0
	   || (state.map.grid[entity.location.r][entity.location.c] == 0 && direction % 2 == 1)
	   || (state.map.grid[entity.location.r][entity.location.c] == 99 && !entity.rubber)){
	   	entity.location.r -= directionArr[0];
		entity.location.c -= directionArr[1];
		return false;
	}
  ```
  It looks as if [99] can be traversed by an entity which has a property 'rubber' set to true.
  ```js
  enemies: [
					{
						id: 1,
						name: "Spatula",
						sprites: "spatula",
						stats: {
							attack: 35,
							defense: 15,
							hp: 60,
							maxHp: 60,
							energy: 30,
							maxEnergy: 30,
							maxItems: 1,
							modifiers: {}
						},
						attacks: [
							{
								name: "Spatulate",
								description: "",
								power: 25,
								accuracy: 90,
								cost: 0,
								effects: [],
								sfx: "physicalAttack",
								range: {
									type: "straight",
									cutsCorners: false,
									distance: 1
								}
							}
						],
						rubber: true,
						items: [],
						location: { r: 1, c: 1 }
					}
				]
 ```
 Looks like the spatula is made of rubber. So the spatula can go through but how does that help us? Let's solve the challenge.
 
 ### Playing the game
