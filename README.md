
# jQuery Tokeninput: A Tokenizing Autocomplete Text Entry

## About this Fork
This is a fork of the original abandoned jQuery TokenInput by LoopJ.  I had need for this 
to work under iOS/iPadOS while running Safari.

## Overview

Tokeninput is a jQuery plugin which allows your users to select multiple items
from a predefined list, using autocompletion as they type to find each item.
You may have seen a similar type of text entry when filling in the recipients
field sending messages on facebook.

## Features

- Intuitive UI for selecting multiple items from a large list
- Easy to skin/style purely in css, no images required
- Supports any backend which can generate JSON, including PHP, Rails, Django, ASP.net
- Smooth animations when results load
- Select, delete and navigate items using the mouse or keyboard
- Client-side result caching to reduce server load
- Crossdomain support via JSONP
- Callbacks when items are added or removed from the list
- Preprocess results from the server with the onResult callback
- Programatically add, remove, clear and get tokens
- Customize the output format of the results and tokens

## Screenshots

![List style](list-style.png)

Vertical list style item selection

![List style](facebook-style.png)

Facebook style item selection

## Installation & Setup

### Create a server-side script to handle search requests

Create a server-side script (PHP, Rails, ASP.net, etc) to generate your
search results. The script can fetch data from wherever you like, for
example a database or a hardcoded list. Your script must accept a GET parameter
named `q` which will contain the term to search for. E.g.
<http://www.example.com/myscript?q=query>

Your script should output JSON search results in the following format:

```javascript
[
    {"id":"856","name":"House"},
    {"id":"1035","name":"Desperate Housewives"},
    ...
]
```

You may optionally specify an attribute of "readonly" and set it to true if you
would like some of the tokens to be non-removable:

```javascript
[
    {"id":"856","name":"House","readonly":true},
    {"id":"1035","name":"Desperate Housewives"},
    ...
]
```

Note that you may omit "readonly" on entities where it is not necessary. This
attribute is acceptable wherever JSON entities are passed, e.g. .tokenInput("add")
(see Methods section below).

### Include and initialize the plugin

Include jQuery and Tokeninput Javascript and stylesheet files on your page, and
attach to your text input:
Tokeninput stylesheet:

```html
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.5.1/jquery.min.js"></script>
<script type="text/javascript" src="yourfiles/jquery.tokeninput.js"></script>
<link rel="stylesheet" type="text/css" href="yourfiles/token-input.css" />

<script type="text/javascript">
$(document).ready(function () {
    $("#my-text-input").tokenInput("/url/to/your/script/");
});
</script>
```

## Configuration

The tokeninput takes an optional second parameter on intitialization which
allows you to customize the appearance and behaviour of the script, as well as
add your own callbacks to intercept certain events. The following options are
available:

### Search Settings

| Option | Description | Default |
| ------ | ----------- | ------- |
| `method` | The HTTP method (eg. GET, POST) to use for the server request. | `"GET"` |
| `queryParam` | The name of the query param which you expect to contain the search term on the server-side. | `"q"` |
| `searchDelay` | The delay, in milliseconds, between the user finishing typing and the search being performed. | `300` |
| `minChars` | The minimum number of characters the user must enter before a search is performed. | `1` |
| `propertyToSearch` | The javascript/json object attribute to search. | `"name"` |
| `jsonContainer` | The name of the json object in the response which contains the search results. This is typically used when your endpoint returns other data in addition to your search results. Use `null` to use the top level response object. | `null` |
| `crossDomain` | Force JSONP cross-domain communication to the server instead of a normal ajax request. Note: JSONP is automatically enabled if we detect the search request is a cross-domain request. | `false` |

### Pre-population Settings

| Option | Description | Default |
| ------ | ----------- | ------- |
| `prePopulate` | Prepopulate the tokeninput with existing data. Set to an array of JSON objects, eg: `[{id: 3, name: "test"}, {id: 5, name: "awesome"}]` to pre-fill the input. | `null` |

### Display Settings

| Option | Description | Default |
| ------ | ----------- | ------- |
| `hintText` | The text to show in the dropdown label which appears when you first click in the search field. | `"Type in a search term"` |
| `noResultsText` | The text to show in the dropdown label when no results are found which match the current query. | `"No results"` |
| `searchingText` | The text to show in the dropdown label when a search is currently in progress. | `"Searching..."` |
| `deleteText` | The text to show on each token which deletes the token when clicked. If you wish to hide the delete link, provide an empty string here. Alternatively you can provide a html string here if you would like to show an image for deleting tokens. | `"&times;"` |
| `animateDropdown` | Set this to `false` to disable animation of the dropdown | `true` |
| `theme` | Set this to a string, eg "facebook" when including theme css files to set the css class suffix | |
| `resultsLimit` | The maximum number of results shown in the drop down. Use `null` to show all the matching results. |`null` |
| `resultsFormatter` | A function that returns an interpolated HTML string for each result. Use this function with a templating system of your choice, such as jresig microtemplates or mustache.js. Use this when you want to include images or multiline formatted results | `function(item){ return "<li>" + item.propertyToSearch + "</li>" }` |
| `tokenFormatter` | A function that returns an interpolated HTML string for each token. Use this function with a templating system of your choice, such as jresig microtemplates or mustache.js. Use this when you want to include images or multiline formatted tokens. Quora's people invite token field that returns avatar tokens is a good example of what can be done this option. | `function(item){ return "<li><p>" + item.propertyToSearch + "</p></li>" }` |

### Tokenization Settings

| Option | Description | Default |
| ------ | ----------- | ------- |
| `tokenLimit` | The maximum number of results allowed to be selected by the user. Use `null` to allow unlimited selections. | `null` |
| `tokenDelimiter` | The separator to use when sending the results back to the server. | `","` |
| `preventDuplicates` | Prevent the user from selecting a token that has already been selected? | `false` |
| `tokenValue` | The value of the token input when the input is submitted. Set it to `id` in order to get a concatenation of token IDs, or to `name` in order to get a concatenation of names. | `"id"` |

### Callbacks

| Option | Description | Default |
| ------ | ----------- | ------- |
| `onResult` | A function to call whenever we receive results back from the server. You can use this function to pre-process results from the server before they are displayed to the user. | `null` |
| `onAdd` | A function to call whenever the user adds another token to their selections. | `null` |
| `onDelete` | A function to call whenever the user removes a token from their selections. | `null` |
| `onReady` | A function to call after initialization is done and the tokeninput is ready to use. | `null` |

## Methods

| Method | Description |
| ------ | ----------- |
| `selector.tokenInput("add", {id: x, name: y});` | Add a new token to the tokeninput with id `x` and name `y`. |
| `selector.tokenInput("remove", {id: x});` | Remove the tokens with id `x` from the tokeninput. |
| `selector.tokenInput("remove", {name: y});` | Remove the tokens with name `y` from the tokeninput. |
| `selector.tokenInput("clear");` | Clear all tokens from the tokeninput. |
| `selector.tokenInput("get");` | Gets the array of selected tokens from the tokeninput (each item being an object of the kind `{id: x, name: y}`). |

## Reporting Bugs or Feature Requests

Please report any bugs or feature requests on the github issues page for this
project here:

<https://github.com/loopj/jquery-tokeninput/issues>

## License

Tokeninput is released under a dual license. You can choose either the GPL or
MIT license depending on the project you are using it in and how you wish to
use it.
