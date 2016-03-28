# Strictness

To find out what use strict does, add this garden-variety `for` loop to the bottom of any `js` file or repl:

```js
'use strict';

var array = ["a", "b", "c"];
for(i = 0; i < array.length; i++){
  console.log(array[i]);
}
```

What happens when we remove the use strict?

### Basically...

`"use strict"` forces us to write better Javascript. The big thing here is that it does not allow makes us to use a variable without first declaring it. [There are many other uses as well.](http://www.w3schools.com/js/js_strict.asp)

### Why declaring variables is important

To demonstrate why this is important, remove the `"use strict";` line. For instructional purposes only, We're going to rename the `i` variable to something more visually interesting -- `potatoSack`, in this case:

```
var array = ["a", "b", "c"];
for(potatoSack = 0; potatoSack < array.length; potatoSack++){
  console.log(array[potatoSack]);
}
```

Now We're going to `console.log(window)` in the Chrome console. Scrolling down, I see `potatoSack` is attached to `window`!

![Potatosack attached to window](http://i.imgur.com/e7IgnAY.png)

When you don't use `var` Javascript attaches the variable to the top-most object it can find. On a webpage, this is always going to be `window`. Do that a lot and it will drastically reduce the performance of our app. `use strict` prevents you from making this mistake.
