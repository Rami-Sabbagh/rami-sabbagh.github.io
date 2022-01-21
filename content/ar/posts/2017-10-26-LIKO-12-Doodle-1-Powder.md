---
title: "LIKO-12 Doodle #1 • PowderV1.2"
description: درس تعليمي حول صنع محاكي تراب بسيط في LIKO-12.
toc: false
authors: [rami-sabbagh]
tags: [LIKO-12]
categories: [doodles]
series: []
date: 2017-10-26T00:00:00+02:00
lastmod: 2017-10-26T00:00:00+02:00
featuredVideo:
featuredImage: images/posts/liko-12-doodle-1-powder/Preview.gif
draft: false
---

> A powder sandbox in LIKO-12

> **ⓘ هام:** هذا المنشور موجود من قبل تقديم الدعم للمتحوى العربي. حيث أن النسخة الأصلية كانت مكتوبة بالانجليزية فقط، وترجمت في بداية 2022 للعربي.


Creating a powder sandbox in LIKO-12, is not hard at all, it only took me 2 hours to write the whole doodle.

First, I had to find a way to store the powder canvas, and for this I used an _imagedata_, but you may be asking, what an imagedata is ?

---

The LIKO-12 GPU offers 2 amazing features called _images_ and _imagedatas_:

- **Images:** They are like sprites (Sprites are actually images internaly), that you can directly draw to the screen, but without being able to edit them.

- **ImageDatas:** They are the _data_ of an image that you can read pixels from, set pixels to, encode, export to a png, etc.., But without the ability to draw them.

You can convert an ImageData to an Image and vice versa.

---

So here I want to create an image with the size of the screen, but leaving some space at the bottom for a toolbar, I can simply achive this by a single call !

```lua
local sw, sh = screenSize() --Returns the size of the screen, so you won't ever have to remember the resolution of LIKO-12 screen :P
local cw, ch = sw, sh-8 --The powder canvas size, with 8 free pixels from the bottom for the toolbar.

local cimg = imagedata(cw,ch) --The imagedata of the powder canvas, we can easily create one by a single call.
```

And creating a particle is easy then

```lua
---local sw, sh = screenSize()
---local cw, ch = sw, sh-8

---local cimg = imagedata(cw,ch)

local parts = {} --A list of particle to update/move.

--Particle X, Particle Y, Particle Color
local function createParticle(x,y,c)
	if x < 0 or y < 0 or x > cw-1 or y > ch-1 then return end --Out of bounds.
	cimg:setPixel(x,y,c) --Set the pixel in the powder canvas.
	parts[#parts+1] = {x,y} --This way is faster than table.insert()
end
```

I had to store the created particles in a table so they get updated each tick until they reach their static state.

---

Next I have to hook the `createParticle` function with the mouse, but wait, what about the mobile devices with multitouch ??
I can simply handle this by storing each touch position in a table, and simulating the mouse as a touch on desktops.

```lua
Controls("touch") --I have to set the controls type to touch for it to work ! (It defaults to the controllers).
cursor("point") --Set the mouse cursor, 'point' is one of the default DiskOS cursors, note that the cursor is not visible on mobile.

---local sw, sh = screenSize()
---local cw, ch = sw, sh-8

---local cimg = imagedata(cw,ch)

---local parts = {}
local touch = {} --The touches table.

---local function createParticle(x,y,c)
---  if x < 0 or y < 0 or x > cw-1 or y > ch-1 then return end
---	cimg:setPixel(x,y,c)
---	parts[#parts+1] = {x,y}
---end

--Touch Events--
--They should be made globals for LIKO-12 to call them.

--Called when the screen is touched.
function _touchpressed(id,x,y)
	touch[id] = {x,y}
end

--Called when a touch moves.
function _touchmoved(id,x,y)
	touch[id][1] = x
	touch[id][2] = y
end

--Called when a touch is released.
function _touchreleased(id,x,y)
	touch[id] = nil
end

--Mouse Events--
--Note: LIKO-12 simulates touch as a mouse by default.

--Called when a mouse button is pressed
function _mousepressed(x,y,button,istouch)
	if istouch then return end --We already handle touch events.
	if button ~= 1 then return end --I only want to handle the left mouse button.
	_touchpressed(0,x,y)
end

--Called when the mouse moves.
function _mousemoved(x,y,dx,dy,istouch)
	if istouch then return end --We already handle touch events.
	if not touch[0] then return end --Because the mouse button is not held down.
	_touchmoved(0,x,y)
end

--Called when a mouse button is released
function _mousereleased(x,y,button,istouch)
	if istouch then return end --We already handle touch events.
	if button ~= 1 then return end --I only want to handle the left mouse button.
	_touchreleased(0,x,y)
end
```

Now I will create a ticks system and call createParticle in it.

```lua
---Controls("touch")
---cursor("point")

---local sw, sh = screenSize()
---local cw, ch = sw, sh-8

---local cimg = imagedata(cw,ch)

---local parts = {}
---local touch = {}

---local function createParticle(x,y,c)
---  if x < 0 or y < 0 or x > cw-1 or y > ch-1 then return end
---	cimg:setPixel(x,y,c)
---	parts[#parts+1] = {x,y}
---end

local function updateTouch()
	for touchid, pos in pairs(touch) do
		createParticle(pos[1],pos[2],7) --Create white particles
	end
end

local function tick()
	updateTouch()
end

--Hook the tick function with LIKO-12 update
function _update(dt) --The delta time between the update calls.
	tick()
end

--Touch Events--
--[[
...
]]

--Mouse Events--
--[[
...
]]
```

Now it's time to add the powder canvas drawing code.

```lua
--[[
---Controls("touch")
---cursor("point")

...

---local function tick()
---	updateTouch()
---end
]]

--Draws the powder canvas.
local function drawSandbox()
	cimg:image():draw(0,0) --Convert it to an image and draw it, it works superfast, even on mobile.
end

---function _update(dt)
---	tick()
---end

function _draw()
	clear() --Clear the pervious screen.
	drawSandbox()
end

--Touch Events--
--[[
...
]]

--Mouse Events--
--[[
...
]]
```

---

It's time for a test run !

![Test Run 1 GIF](/images/posts/liko-12-doodle-1-powder/TestRun1.gif)

---

After that I had to write the particles movement function, and call it in tick:

```lua
--[[
Controls("touch")

...

local function updateTouch() ... end
]]

local function updateParticle(x,y)
	local pcol = cimg:getPixel(x,y) --The particle color
	if cimg:getPixel(x,y+1) == 0 then --If the pixel under the particle is empty
```
![Particle Case 1](/images/posts/liko-12-doodle-1-powder/PState1.png)
```lua
		--Move the particle
		cimg:setPixel(x,y+1,pcol):setPixel(x,y,0)
			
		--Return the new position
		return x,y+1
		
	else --Otherwise check the left and right pixels under the particle
	
		if cimg:getPixel(x-1,y+1) == 0 then --Check if the particle can move to the left pixel under it.
```
![Particle Case 2](/images/posts/liko-12-doodle-1-powder/PState2.png)
```lua
			--Move the particle
			cimg:setPixel(x-1,y+1,pcol):setPixel(x,y,0)
			
			--Return the new position
			return x-1,y+1
			
		elseif cimg:getPixel(x+1,y+1) == 0 then --If not then check if the particle can move to the right pixel under it.
```
![Particle Case 3](/images/posts/liko-12-doodle-1-powder/PState3.png)
```lua
			--Move the particle
			cimg:setPixel(x+1,y+1,pcol):setPixel(x,y,0)
			
			--Return the new position
			return x+1,y+1
			
		end
	end
end

local function updateSandbox()
	local nparts, tid = {}, 1 --The particles that are still moving.
	for i=1,#parts do
		local nx, ny = updateParticle(unpack(parts[i]))
		if nx then
			nparts[tid] = {nx,ny}
			tid = tid + 1
		end
		--If a particle didn't move then it's in a static state -> there's no longer a need to update/move it !
	end
	parts = nparts
end

local function tick()
	updateTouch()
	updateSandbox()
end

--[[
function _update() ... end

....

End-Of-File
]]
```

But there's 1 thing, when the particle is created on the sides of the screen, or when it reaches the bottom of the canvas it will error because of out-of-bound `getPixel` !
To avoid that I made 2 edits:

#### Create a border around the cavnas:

`imagedata:map(func)` call func for each pixel in the imagedata.
{: .notice_info}

```lua
--[[
Controls("touch")

...

local cimg = imagedata(cw,ch)
]]

cimg:map(function(x,y,c)
	if x == 0 or y == 0 or x == cw-1 or y == ch-1 then
		return 15
	end
end)

--The rest of the code
```

#### Set the limits of `createParticle` to only work inside the border (inorder to not replace the border)

```lua
---....
local function createParticle(x,y,c)
	if x < 1 or y < 1 or x > cw-2 or y > ch-2 then return end --This line have been changed !
	--...
end
---....
```

---

It's time for another test run !

![Test Run 2 GIF](/images/posts/liko-12-doodle-1-powder/TestRun2.gif)

---

**The final task:** Create the bottom toolbar !

The bottom toolbar would have:
- A color box, to show the current selected color
- A colors bar with 15 color (black can't be used)

#### The color box:

I'm going to draw a sprite, and use `pal()` to change the color inside of it:

`pal(c1,c2)` Changes `c1` to have the color of `c2`.
{: .notice_info}

![Colorbox Sprite](/images/posts/liko-12-doodle-1-powder/ColorBox.png)

#### The colors bar:

I'll use a useful function provided by DiskOS named `whereInGrid(mx,my,grid)`

The grid definition: `{Grid Top-Left X, Grid Top-Left Y, Grid Width in Pixels, Grid Height in Pixels, The number of the columns, The number of rows}`
{: .notice_info}

That grid function will take a mouse position, and return the position of the cell that the mouse is hovering over in cells.

And for the colors bar drawing I'll create an image with the 15 color, and scale it by 8 when drawing it.

---

```lua
---....
---cimg:map(...)

local pimg = imagedata(15,1)
local __c = 0 --A temp variable
pimg:map(function(x,y,c)
	__c = __c + 1
	return __c
end)
__c = nil
pimg = pimg:image() --Convert it to a drawable image

local palgrid = {sw-8*15,sh-8, 8*15,8, 15, 1}

---local parts...
---local touch...

local selcol = 4 --The selected color id

---....

local function updateTouch()
 for touchid, pos in pairs(touch) do
  createParticle(pos[1],pos[2],selcol) --Now passes selcol instead of 7.
 end
end

---....

---local function drawSandbox() ... end

--Draws the toolbar
local function drawToolbar()
	rect(0,sh-8,sw,8,false,9) --Draws an orange rectangle bar.
	pal(15,selcol) --Change the color 15 to the selection color.
	Sprite(0, 0, sh-8) --Draw the sprite 0 (ColorBox) at the left side of the toolbar.
	pal(15,15) --Set color 15 back to it's original color.
	pimg:draw(palgrid[1],palgrid[2], 0, 8,8) --Draw the palette/colors bar image.
end

---function _update() ... end

function _draw()
 clear() --Clear the pervious screen.
 drawSandbox()
 drawToolbar()
end

---...

--The toolbar doesn't have to handle multitouch, so I will put it's handling code in _mousepressed.
function _mousepressed(x,y,button,istouch)
 --Since it's handled before the istouch conditional then it will work for both mouse and touch.
 local cx, cy = whereInGrid(x,y,palgrid)
 if cx then selcol = cx end
 
 if istouch then return end --We already handle touch events.
 if button ~= 1 then return end --I only want to handle the left mouse button.
 _touchpressed(0,x,y)
end

--- To the end of the file.
```

---

That's it, you got a full clone of my powder doodle !

![Result GIF](/images/posts/liko-12-doodle-1-powder/Result.gif)

---

Oh, and you may notice that the sandbox ticks so slow, for that we can do a very simple patch in `_update`:

```lua
function _update(dt)
 for i=1,5 do --To speedup the simulation, you can change 5 to whatever you want (but not negative !).
  tick()
 end
end
```

![Final GIF](/images/posts/liko-12-doodle-1-powder/Final.gif)

You can get the final version by typing in LIKO-12 terminal:

```
pastebin get x1yDaTxk powderV1.2.lk12
load powderV1.2
run
```

---

Thanks for reading !

I hope you enjoyed it, Feel free to support my work by donating to [LIKO-12](http://ramilego4game.itch.io/liko12) :)

---
