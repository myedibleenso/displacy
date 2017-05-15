# displacy-processors.js

`displacy-processors.js` is a fork of [displaCy.js](https://demos.explosion.ai/displacy) adapted for use with [`clulab/processors`](https) and [`py-processors`]().  


[![npm](https://img.shields.io/npm/v/displacy-processors.svg)](https://www.npmjs.com/package/displacy-processors)


## Using `displacy-processors.js`

To use the visualizer in your project, download [`displacy-processors.js`](displacy-processors.js) from GitHub or via npm:

```bash
npm install myedibleenso/displacy-processors
```

Include the file and initialize a new `dispaCyProcessors` instance (see the [settings](#settings) table for a description of all available settings):

```javascript
const visualizer = new displaCyProcessors({
    container: '#displacy',
    distance: 300,
    offsetX: 100
});
```

`displacy-processors` operates on the `json` representation of a `Sentence` (refer to the [schema](https://github.com/myedibleenso/processors-server/blob/master/src/main/resources/json/schema/sentence.json) for more details).

```javascript
var sentence = {
  "words": ["To", "be", "loved", "by", "unicorns", "is", "the", "greatest", "gift", "of", "all", "."],
  "lemmas": ["to", "be", "love", "by", "unicorn", "be", "the", "greatest", "gift", "of", "all", "."],
  "tags": ["TO", "VB", "VBN", "IN", "NNS", "VBZ", "DT", "JJS", "NN", "IN", "DT", "."],
  "entities": ["O", "O", "O", "O", "O", "O", "O", "O", "O", "O", "O", "O"],
  "startOffsets": [0, 3, 6, 12, 15, 24, 27, 31, 40, 45, 48, 51],
  "endOffsets": [2, 5, 11, 14, 23, 26, 30, 39, 44, 47, 51, 52],
  "graphs": {
    "stanford-basic": {
      "edges": [
        {"destination": 0, "relation": "aux", "source": 2},
        {"destination": 1, "relation": "auxpass", "source": 2},
        {"destination": 3, "relation": "prep", "source": 2},
        {"destination": 4, "relation": "pobj", "source": 3},
        {"destination": 2, "relation": "csubj", "source": 8},
        {"destination": 5, "relation": "cop", "source": 8},
        {"destination": 6, "relation": "det", "source": 8},
        {"destination": 7, "relation": "amod", "source": 8},
        {"destination": 9, "relation": "prep", "source": 8},
        {"destination": 11, "relation": "punct", "source": 8},
        {"destination": 10, "relation": "pobj", "source": 9}
      ],
      "roots": [8]
    },
    "stanford-collapsed": {
      "edges": [
        {"destination": 0, "relation": "aux", "source": 2},
        {"destination": 1, "relation": "auxpass", "source": 2},
        {"destination": 4, "relation": "agent", "source": 2},
        {"destination": 2, "relation": "csubj", "source": 8},
        {"destination": 5, "relation": "cop", "source": 8},
        {"destination": 6, "relation": "det", "source": 8},
        {"destination": 7, "relation": "amod", "source": 8},
        {"destination": 10, "relation": "prep_of", "source": 8},
        {"destination": 11, "relation": "punct", "source": 8}
      ],
     "roots": [8]
   }
 }
}
```

The `render` method is used to display a selected graph.  Note that a `Sentence` may have several associated graphs (parses).  Here we'll display the syntactic dependency parse using the [Stanford collapsed representation](https://nlp.stanford.edu/software/dependencies_manual.pdf).
```javascript
visualizer.render(sentence, graphName = "stanford-collapsed")
```

!["example-parse.svg"](examples/example-parse.svg)
# Settings
The following settings are available:

| Setting | Description | Default |
| --- | --- | --- |
| **container** | element in which to render the specified graph.  This can be any query selector | `#displacy` |
| **distance** | distance between words in px | `300` |
| **offsetX** | spacing on left side of the SVG in px | `50` |
| **arrowSpacing** | spacing between arrows in px to avoid overlaps | `20` |
| **arrowWidth** | width of arrow head in px | `10` |
| **arrowStroke** | width of arc in px | `2` |
| **wordSpacing** | spacing between words and arcs in px | `50` |
| **font** | font face for all text | `'inherit'` |
| **color** | text color, HEX, RGB or color names | `'#000000'` |
| **bg** | background color, HEX, RGB or color names | `'#ffffff'` |


## Changing the theme and colors

You can find the current theme settings in [`css/_displacy-theme.sass`](css/_displacy-theme.sass). All elements contained in the SVG output come with tags and data attributes and can be styled flexibly using CSS. By default, the `currentColor` of the element is used for coloring, meaning only need to change the `color` property in CSS.

The following classes are available:

| Class name | Description |
| --- | --- |
| **.displacy-word** | word text
| **.displacy-tag** | POS tag
| **.displacy-token** | container of word and POS tag
| **.displacy-arc** | arrow arc (without label or arrow head)
| **.displacy-label** | relation type (arrow label)
| **.displacy-arrowhead** | arrow head
| **.displacy-arrow** | container of arc, label and arrow head

Additionally, you can use these attributes as attribute selectors:

| Attribute | Value | On Element |
| --- | --- | --- |
| **data-tag** | POS tag value | `.displacy-token`, `.displacy-word`, `.displacy-tag` |
| **data-label** | relation type value | `.displacy-arrow`, `.displacy-arc`, `.displacy-arrowhead`, `.displacy-label` |
| **data-dir** | direction of arrow | `.displacy-arrow`, `.displacy-arc`, `.displacy-arrowhead`, `.displacy-label` |

Using a combination of those selectors and some basic CSS logic, you can create pretty powerful templates to style the elements based on their role and function in the parse. Here are a few examples:

```css
/* Format all words in 12px Helvetica and grey */

.displacy-word {
    font: 12px Helvetica, Arial, sans-serif;
    color: grey;
}


/* Make all noun phrases (tags that start with "NN") green */

.displacy-tag[data-tag^="NN"] {
    color: green;
}


/* Make all right arrows red and hide their labels */

.displacy-arc[data-dir="right"],
.displacy-arrowhead[data-dir="right"] {
    color: red;
}

.displacy-label[data-dir="right"] {
    display: none;
}


/* Hide all tags for verbs (tags that start with "VB") that are NOT the base form ("VB") */

.displacy-tag[data-tag^="VB"]:not([data-tag="VB"]) {
    display: none;
}


/* Only display tags if word is hovered (with smooth transition) */

.displacy-tag {
    opacity: 0;
    transition: opacity 0.25s ease;
}

.displacy-word:hover + .displacy-tag {
    opacity: 1;
}
```

## Adding custom data attributes

`displaCy` lets you define custom attributes via the JSON representation of the parse on both `words` and `arcs`:

```json
"words": [
    {
        "tag": "NNS",
        "text": "Robots",
        "data": [
            [ "custom", "your value here" ],
            [ "example", "example text here" ]
        ]
    }
]
```

Custom attributes are added as data attributes prefixed with `data-`, so their names shouldn't contain spaces or special characters. If added to `words`, the data attributes are attached to the token (`.displacy-token`), if added to `arcs`, they're attached to the arrow (`.displacy-arrow`):

```html
<text class="displacy-token" data-custom="your value here" data-example="example text here">...</text>
```
