# cyf-snippets
all stuff in one place, right?


### Linear interpolation
```lua
function lerp(a, b, t)
    return a + ((b - a) * t)
end
```

### Text that types in reverse by @AllyTally
```lua
function reversetext(text)
    texttable={}
    for i=1,#text+1 do
        texttable[i]="[instant][noskip]" .. string.sub(text,1,#text-i+1) .. "[instant:stop][w:1][next]"
    end
    return texttable
end

BattleDialog(reversetext("Reverse this."))
```

### Retromode checker by @WD200019
```lua
-- Detects if CYF is running in retromode!
local spr = CreateSprite("bg")
spr.Remove()
if spr.isactive then -- if retromode is ON
    -- do things...
```

### Hexidecimal/Decimal conversion by @WD200019
```lua
function toHex(dec)
    return string.format("%x", tostring(dec))
end

function toDec(hex)
    return tonumber(hex, 16)
end

toHex(29)    -- 1d
toHex(100)   -- 64
toHex(255)   -- ff

toDec("1d")  -- 29
toDec("64")  -- 100
toDec("ff")  -- 255
```

### Get mod name and mod path by @WD200019
```lua
local self = {}

local _, output = xpcall(function() return Misc.OpenFile("").ReadLines() end, debug.traceback)
output = output:gsub("/", "\\")
self.modPath = output:sub(output:find("\"") + 1, output:find("\\[^\\]*$") - 1)
self.modName = output:sub(0, output:find("\\[^\\]*$") - 1):sub(output:sub(0, output:find("\\[^\\]*$") - 1):find("\\[^\\]*$") + 1, output:len())

return self
```
For a version that works below CYF v0.6.3, look here: https://pastebin.com/i7mhxPBA

### sprite.color rainbow by @WD200019
```lua
local r = (math.sin((Time.time * 2) +  0) / 2) + 0.5
local g = (math.sin((Time.time * 2) +  8) / 2) + 0.5
local b = (math.sin((Time.time * 2) + 16) / 2) + 0.5
```

### table.copy by @RhenaudTheLukark
```lua
-- Copies a table
function table.copy(orig, nonest)
    local orig_type = type(orig)
    local copy
    if orig_type == 'table' then
        copy = { }
        for orig_key, orig_value in next, orig, nil do
            if nonest then
                orig_key   = table.copy(orig_key)
                orig_value = table.copy(orig_value)
            end
            copy[orig_key] = orig_value
        end
        setmetatable(copy, table.copy(getmetatable(orig)))
    else -- number, string, boolean, etc
        copy = orig
    end
    return copy
end

new_table = table.copy(old_table, false)
```

### Does a table contain an object as a key/value? by @RhenaudTheLukark

```lua
-- Checks if a table contains an object as a key or as a value
-- Returns a key of the table if this object is found
function table.containsObj(tab, obj, valueOnly)
    if type(tab) ~= "table" then
        error("Can't use table.containsObj with a " .. type(tab) .. "!")
    end
    for k, v in pairs(tab) do
        if (not valueOnly and k == obj) or v == obj then
            return k
        end
    end
    return false
end
```

### string.ends_with by @RhenaudTheLukark
```lua
function string.ends_with(str, ending)
    return ending == "" or str:sub(-#ending) == ending
end
```
(by the way, apparently Moonsharp has string.endsWith already, so I'd recommend timing this function and that built in function and seeing which is better.)

### string.split by @RhenaudTheLukark and @WD200019
```lua
-- Takes a string and returns a table of strings between the characters we were searching for
-- Ex: string.split("test:testy", ":") --> { "test", "testy" }
-- Improved by WD200019
function string.split(inputstr, sep, isPattern)
    if sep == nil then
        sep = "%s"
    end
    local t = { }
    if isPattern then
        while string.find(inputstr, sep) ~= nil do
            local matchrange = { string.find(inputstr, sep) }
            local preceding = string.sub(inputstr, 0, matchrange[1] - 1)
            table.insert(t, preceding ~= "" and preceding or nil)
            inputstr = string.sub(inputstr, matchrange[2] + 1)
        end
        table.insert(t, inputstr)
    else
        for str in string.gmatch(inputstr, "([^"..sep.."]+)") do
            table.insert(t, str)
        end
    end
    return t
end
```

### String indexing by @RhenaudTheLukark
```lua
-- Allows to get characters from a string like we'd get a value in tables
-- Ex: a = "abcdef"; a[4] --> d
getmetatable('').__index = function(str,i)
    if type(i) == 'number' then
        return string.sub(str,i,i)
    else
        return string[i]
    end
end
```
### Table debugger by @RhenaudTheLukark
```lua
-- Debugs a table's content.
function TableDebugger(tab, nonest, nest)
    if nest == nil then nest = 1 end
    local result = "{\n"
    
    -- First is used to not create a comma on the first element of a line
    local first = true 
    for k, v in pairs(tab) do
        -- If the value is a string, keep the quotes
        if type(v) == "string" then 
            v = "\"" .. tostring(v) .. "\"" 
        end
        
        -- If the value is a table, call this function recursively
        if type(v) == "table" then
            v = nonest and "(table)" or TableDebugger(v, false, nest + 1)
        end
        
        -- Add index
        v = tostring(k) .. " = " .. tostring(v) 

        -- Add comma and indents
        local stringToAdd = first and "" or ",\n"
        for i = 1, nest do 
            stringToAdd = stringToAdd .. "    "
        end

        result = result .. stringToAdd .. v
        first = false
    end
    result = result .. "\n"

    -- Indents
    for i = 1, nest - 1 do 
        result = result .. "    " 
    end

    if nest == 1 then DEBUG(result .. "}") else return result .. "}" end
end
```


### A few useful math formulas by @WD200019
```lua
-- Distance formula
math.sqrt((x2 - x1)^2 + (y2 - y1)^2)

-- Angle between two points
math.deg(math.atan2(y2 - y1, x2 - x1)) % 360

-- calculate the x position when y is (a given value) with a given slope
targety = Arena.height/2 -- the y value you want to test for
startingy = 0 -- the y value we start at
slope = math.tan(math.rad(angle in degrees)) -- or give a fraction like 0, 1, 1/2
xintercept = ((1 / slope) * targety) - ((1 / slope) * startingy) -- this is the x position where y = targety
```

### math.sign by @RhenaudTheLukark
```lua
function math.sign(number)
    return number > 0 and 1 or number < 0 and -1 or 0
end
```
