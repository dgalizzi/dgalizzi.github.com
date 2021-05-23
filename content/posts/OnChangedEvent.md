---
title: "Neovim harpoon plugin 'on change' event"
date: 2021-05-23
draft: false
---

[Harpoon](https://github.com/ThePrimeagen/harpoon) exposes an 'on' event where you can subscribe callbacks to extend its functionality.

Here's the exposed 'on' function at [harpoon's code](https://github.com/ThePrimeagen/harpoon/blob/55d5d808d6b96fc4f951d4c265c933dfb708411a/lua/harpoon/mark.lua#L353) at the time of this writing
```lua
M.on = function(event, cb)
    log.trace("on():", event)
    if not callbacks[event] then
        log.debug("on(): no callbacks yet for", event)
        callbacks[event] = {}
    end

    table.insert(callbacks[event], cb)
    log.debug("on(): All callbacks:", callbacks)
end
```

And here's the [only event implemented](https://github.com/ThePrimeagen/harpoon/blob/55d5d808d6b96fc4f951d4c265c933dfb708411a/lua/harpoon/mark.lua#L12) so far
```lua
local function emit_changed()
    log.trace("_emit_changed()")
    if harpoon.get_global_settings().save_on_change then
        harpoon.save()
    end

    if not callbacks["changed"] then
        log.trace("_emit_changed(): no callbacks for 'changed', returning")
        return
    end

    for idx, cb in pairs(callbacks["changed"]) do
        log.trace(string.format(
            "_emit_changed(): Running callback #%d for 'changed'",
            idx
        ))
        cb()
    end
end
```
You can look through the file to see where this event is emitted.

### Go to last 'harpooned' file
On my workflow it is very common to work a lot back and forth between just two files, so I need a quick way to go back to the previous file. But, many times I also jump around multiple files in between looking for something.

Then, when I end with that, I want to go back to the previous *important* file, that is, the last file I was working on that is tracked by harpoon.

This can be done by subscribing to the on("change") event on harpoon and keeping track of the last harpoon index.

To get the harpoon index there's an exposed function call [get_current_index](https://github.com/ThePrimeagen/harpoon/blob/55d5d808d6b96fc4f951d4c265c933dfb708411a/lua/harpoon/mark.lua#L348)

```lua
M.get_current_index = function()
    log.trace("get_current_index()")
    return M.get_index_of(vim.fn.bufname(vim.fn.bufnr()))
end
```

Then, a solution could be the following

```lua
local harpoonMark = require("harpoon.mark")

MyHarpoonLastIndex = 0
harpoonMark.on("changed", function()
  lastIndex = harpoonMark.get_current_index()
  if lastIndex ~= nil then
      MyHarpoonLastIndex = lastIndex
  end
end)
```
This works by setting a callback on the changed event, and updating the *MyHarpoonLastIndex* variable by calling *get_current_index* everytime the changed event is emitted.

Finally, by doing
```
:lua require("harpoon.ui").nav_file(MyHarpoonLastIndex)<Cr>
```
you go back to the last file you were working on that is tracked by harpoon.

