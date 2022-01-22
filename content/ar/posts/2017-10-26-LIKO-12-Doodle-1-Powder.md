---
title: "LIKO-12 Doodle #1 • PowderV1.2"
description: شرح حول كيفيّة صنع محاكي بسيط للمسحوق في LIKO-12.
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

> بيئة بسيطة لمحاكاة مسحوق في LIKO-12

> **ⓘ هام:** هذا المنشور موجود من قبل توفير الدعم للمحتوى العربي. حيث أن النسخة الأصلية مكتوبة بالانجليزية فقط، وترجمت في بداية عام 2022 إلى العربيّة.

إنّ إنشاء بيئة محاكاة بسيطة للمسحوق في LIKO-12 ليست بالشيء الصعب على الإطلاق، حيث أنها احتاجت منّي فقط ساعتين لكتابة كل الـ"doodle".

بدايةً كان عليَّ إيجاد طريقة لتخزين "لوح" المسحوق، ولهذا الغرض استخدمت _imagedata_، ولكن ربّما أنت تسأل، ماذا تكون الـimagedata؟

---

محرّك رسوميّات الـLIKO-12 يقدّم ميزتين رائعتين معروفتين باسمي _images_ (الصور) و _imagedatas_ (بيانات الصور):

- **الصور Images:** انهم مثل الأشباح sprites (الأشباح هم عبارة عن صور داخل المحرّك)، والتي يمكنك رسمهم مباشرةً على الشاشة، مقابل عدم القدرة على تعديلهم.

- **بيانات الصور ImageDatas:** إنّهم _البيانات_ المعبّرة عن الصور، والّتي تستطيع أن تقرأ ألوان النقاط منهم، تعدّل النقاط فيهم، ترميزهم إلى سلسلة محارف أو تخرّجهم إلى ملفات png، الخ..، لكن من دون القدرة على عرضهم.

وتستطيع التحويل من ImageData إلى Image وبالعكس أيضاً.

---

فهنا أريد إنشاء صورة بحجم الشاشة، مع إتاحة المجال في أسفل الشاشة لشريط أدوات، ويمكنني تحقيق هذا ببساطة:


```lua
local sw, sh = screenSize() --Returns the size of the screen, so you won't ever have to remember the resolution of LIKO-12 screen :P
local cw, ch = sw, sh-8 --The powder canvas size, with 8 free pixels from the bottom for the toolbar.

local cimg = imagedata(cw,ch) --The imagedata of the powder canvas, we can easily create one by a single call.
```

ويكون إنشاء حبيبات المسحوق سهلا:

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

توجّب عليَّ تخزين الحبيبات في مصفوفة حتى يتمّ تحديثهم في كل تكّة حتى تصل الحبيبات إلى وضعها الثابت.

---

ومن ثمّ احتاج إلى ربط تابع `createParticle` مع الـmouse، لكن انتظر، ماذا عن الأجهزة المحمولة مع اللّمس المتعدّد ؟؟ يمكن التعامل معهم عن طريق تخزين موقع كل "لمس" (اصبع) في مصفوفة، ومحاكاة الـmouse على أنّها لمس في الأجهزة المكتبيّة.

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

الآن سأنشئ نظام تكتكة واستدعي `createParticle` فيه:

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

حان الموعد لإضافة كود رسم "اللوح":

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

إنّه الوقت لتشغيل تجريبي!

![Test Run 1 GIF](/images/posts/liko-12-doodle-1-powder/TestRun1.gif)

---

بعد ذلك توجّب علي كتابة تابع حركة الحبيبات، واستدعائه عند كل تكّة:

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

لكن هنالك شيءٌ واحد، والّذي توقّف البرنامج نتيجة خطأ عندما يتم إنشاء حبيبات عند أطراف الشاشة أو عندما تصل الحبيبات لأسفل "اللّوح" مع رسالة `getPixel` "خارج المجال"!

لتجنّب هذه المشكلة توجّب إجراء تعديلين:

#### إنشاء إطار حول اللّوح:

> (`imagedata:map(func)` يستدعي التابع `func` لكل نقطة في الـimagedata).

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

#### جعل `createParticle` تعمل فقط داخل الإطار (من أجل عدم استبدال حبيبات الإطار الثابتة):

```lua
---....
local function createParticle(x,y,c)
	if x < 1 or y < 1 or x > cw-2 or y > ch-2 then return end --This line have been changed !
	--...
end
---....
```

---

حان الوقت لتشغيل تجريبي أخر!

![Test Run 2 GIF](/images/posts/liko-12-doodle-1-powder/TestRun2.gif)

---

**المهمّة النهائيّة:** إنشاء شريط الأدوات السفلي!

سيحتوي شريط الأدوات السفلي على:

- صندوق لون، لإظهار اللّون المختار حاليّاً.
- شريط ألوان بـ15 لون (لا يمكن استخدام اللّون الأسود).

#### صندوق اللّون:

سأرسم شبح وأستخدم `pal()` لتغيير اللّون داخله:

> `pal(c1,c2)` تغيّر `c1` ليصبح باللّون `c2`.

![Colorbox Sprite](/images/posts/liko-12-doodle-1-powder/ColorBox.png)

#### شريط الألوان:

سأستخدم تابع مفيد مزوّد من قبل DiskOS (نظام تشغيل الـLIKO-12) يدعى `whereInGrid(mx,my,grid)`

تعريف الشبكة: `{X الزاوية العلياء-اليسراء للشبكة, Y الزاوية العلياء-اليسراء للشبكة, عرض الشبكة بالنقاط, ارتفاع الشبكة بالنقاط, عدد الأعمدة, عدد الأسطر}`

هذا التابع يأخذ إحداثيّات المؤشّر وتعريف للشبكة، ويعطي بالمقابل موضع الخليّة التي يحوم حولها المؤشّر باستخدام واحدة الخلايا (وليس النقاط).

ولرسم شريط الألوان سأنشئ صورة بـ15 لون وأرسمها بتكبير 8 مرّات.

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

هذه هي، لقد حصلت على نسخة كاملة من بيئة محاكاة المسحوق البسيطة!

![Result GIF](/images/posts/liko-12-doodle-1-powder/Result.gif)

---

أه، ويمكنك أيضا أن تلاحظ أن المحاكات تتم ببطء، لحلّ هذا نستطيع إجراء تعديل بسيط جدّا بحيث نجري 5 تكّات عند كل تحديث للمحاكاة في `_update`:

```lua
function _update(dt)
 for i=1,5 do --To speedup the simulation, you can change 5 to whatever you want (but not negative !).
  tick()
 end
end
```

![Final GIF](/images/posts/liko-12-doodle-1-powder/Final.gif)

وتستطيع الحصول على النسخة النهائيّة بإدخال التالي في الـLIKO-12 Terminal:

```
pastebin get x1yDaTxk powderV1.2.lk12
load powderV1.2
run
```

---

شكراً للقراءة!

أتمنّى أنّك قد استمتعت بالمنشور، ويمكنك دوما دعم أعمالي عن طريق التبرّع لـ[LIKO-12](http://ramilego4game.itch.io/liko12) :)

> **تحديث 2021:** تمّ إغلاق التبرّعات عن مشروع الـLIKO-12.


---
