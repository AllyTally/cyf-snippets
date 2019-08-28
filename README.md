# cyf-snippets
all stuff in one place, right?


### Text that types in reverse.
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
