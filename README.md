# Truncate copy blocks

This may be a simple one to most, but it's one of those things that have annoyed me to no end.

Use case; a the designer asks for a constrained UI with a set width and height, then restrict the characters to, say 128, and set an ellipsis (`...`) at the end.

We all know that there is a simple ellipsis [CSS trick we can use](https://css-tricks.com/snippets/css/truncate-string-with-ellipsis/), but it has one major constraint, it has to be a single line. The design being requested is for that to be a block of text contained in a constrained width and wrapped lines of text. So, CSS alone won't work.

### JavaScript to the rescue

Yes, we need to lean on JavaScript here. There are a lot of examples out there to pull from, but most lean heavily on jQuery. We don't need to load a library to make this work.

The solution I will illustrate is simple enough to follow;

* loop through some DOM nodes
* find string
* determine length
* truncate if needed
* add ellipsis UI if needed

### The HTML

For this example, we will use something simple like:

```
<div data-truncate class="copy-block">
  You probably haven't heard of them af brooklyn, crucifix pour-over cardigan fixie microdosing. 3 wolf moon VHS fashion axe, four dollar toast edison bulb meggings shabby chic normcore banjo tbh hexagon photo booth vape PBR&B.
</div>
<div data-truncate class="copy-block">
  Why Hi Master Blaster! Are you hear to take over the planet and destroy all living things?
</div>

```

### The CSS

To get the effect we want, we just need to make a couple selectors. First one sets the `max-width` of a content box, so that the content wraps, and then the spacing between elements. The other will add the `...` after the truncated content.

```
.copy-block {
  width: 300px;
  margin-bottom: 1rem;
}

.ellipsis:after {
  content: ' ...';
}
```

### The JavaScript

Note: the following examples are using ES6 constructs, so if you need to support < ES6, simply replace the variable keywords from `const` and `let` to `var` and it will work.

To get this started, we need to find all the DOM nodes that we want to target. For this example, I am using a `data-` element so that we are maintaining a separation of concerns between presentation and functionality.

```
const fullString = document.querySelectorAll('[data-truncate]');
```

Now that we have found all the selector(s) and JavaScript placed that into an array, we need to loop through that data. The array of `fullString` has a length of the number of nodes discovered in the DOM. That value will be the number of times this loops through the commands.

```
for (let i = 0; i < fullString.length; i++) {
  ...
}
```

What will this loop do? The first question we need to know is, what is the string of text per node returned per this loop. To do this we construct another variable, `truncateString`.

The `[i]` variable is in reference to the `i` object used in the for loop. Each time the code loops through the array, it assigns a value to `i`. For this new variable, we are using that assigned value to target that part of the array and get it's `innerText`.

```
let truncateString = fullString[i].innerText;
```

Now that we know a string of text, we need to know if it's longer then the max number of characters we want in the UI. To do this we need to use an `if` statement.

To write out the question, we need to know ...

> What is the length of the text string?
> Is it greater or less than the max value we are targeting?
> If it's greater, then what?

How does this look in JavaScript?

```
// if the value of truncateString is greater than 128
if(truncateString.length > 128) {

  // reset value of truncateString using substring() function
  // 0 = start character, 128 = last character
  truncateString = truncateString.substring(0,128);

  // add CSS class to DOM node
  fullString[i].className += ' ellipsis';
}
```

Great. We have found all the DOM nodes that we are targeting, and the content within each node. We have determined if the string is greater than 128 characters. If so, we have updated the string and added a new CSS class to that same DOM node.

Last thing we need to do is; within the the scope of the `if` loop, we need to update the value of `fullString`'s `innerText` within this iteration to that of `truncateString`.

It sounds worse then it is.

```
fullString[i].innerText = truncateString;
```

#### Turn this up to 11

This code works and will work by simply adding this to the page with the `<script>` tags. But ... it's all global and will fire immediately on the page. Any responsible JavaScript developer should wrap this code into a function to maintain scope and control use.

Looking at the previous code we wrote, we can abstract two things that make this more flexible. The `selector` that we are targeting and the `characterLimit` that we want to enforce.

Let's make an anonymous function called `truncateBlock` and we will pass in these two arguments.

```
function truncateBlock(selector, characterLimit) {
  ...
}
```

Then move all the code from above into that function, and update hard values like `[data-truncate]` and `128` with the appropriate variable references.

It's now up to you where you want to put that function. Most common, you may have a .js file called utils.js or something similar. You may also have this block of code inside a larger function that is already associated with this view. In the end, the idea is to have this code placed inside a function as not to allow it to pollute the global scope of the document, window or app.

To use this function, simply reference the function name and pass in values for the arguments, like the following:

```
truncateBlock('[data-truncate]', 128);
```

### Conclusion

To see the final version of this code, you can easily review the [index.html](https://github.com/blackfalcon/truncate-content/blob/master/index.html) page in this repo.

To make this run in the browser, you can simply open the HTML file or run any local server.

Personally, I have really begun to love [live-server](https://github.com/tapio/live-server). And you should too.
