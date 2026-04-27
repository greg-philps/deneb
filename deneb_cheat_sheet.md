# DENEB CHEAT SHEET
- Author: Greg Philps
- Version: 2.0
- Date: April 2026

## GENERAL
The required 3 basic block types of a Deneb/Vega-Lite specification are data, mark, and encoding:
``` json
{
  "data": {"name": "dataset"},
  "mark": {"type":  "bar"},
  "encoding": {
    "y": {"field": "Country", "type": "nominal"},
    "x": {"field": "Total Sales" , "type": "quantitative"},
    "color": {"field": "Channel", "type": "nominal"} }
}
```
There are several mark types in Vega-Lite, including:
- common: bar, line, arc, circle, text, area
- other: point, rect, rule, square, tick, geoshape
- composite: boxplot, errorband, errorbar

https://bit.ly/3Bm9xuG

<br>

There are 4 field-mapping types:
- nominal: the category of the data (use if string)
- quantitative: the value of the data (use if numeric)
- temporal: the value of the data (use if date)
- ordinal: a ranked order for data sorting 

https://bit.ly/3Bvx7VM

<hr>

## SORTING
To sort an axis by a field, add a sort block to its’ encoding block:
``` json
"sort": {
  "op": "sum",
  "field": "Total Sales",
  "order": "descending"}
```
<hr>

## FORMATTING
To format a data value using Power BI format strings, add to its’ definition in the encoding block:
``` json
"encoding": {
  "x": {
    "field": "Total Sales",
    "type": "quantitative",
    "axis": {
        "formatType": "pbiFormat",
        "format": "#0,,.0M"
 } } }
```

https://bit.ly/3cUwwD3

<hr>

## TOOLTIPS
To enable tooltips, add a property to the mark block:
``` json
"mark": {"type": "bar", "tooltip": true}
```
<br>
To customize tooltips, add a tooltip block to the encoding block:

``` json
"tooltip": [
  {"field": "Country", "type": "nominal"},
  {"field": "Total Sales", "type": "quantitative", "title": "Sales" } ]
```

https://bit.ly/3bppOnT

<hr>

## LAYERS
To create a visual using multiple overlapping marks, create a layer consisting of an array of specifications, one for each mark: 
``` json
{
 "data": {"name": "dataset"},
 "layer": [
  {
    "name": "TOTAL SALES",
    "mark": {
      "type": "bar",
      "size": {"expr": "bandwidth('y')"} },
    "encoding": {
      "x": {"field": "Total Sales", "type": "quantitative"},
      "y": {"field": "Country", "type": "ordinal"},
      "color": {"value": "blue" } } 
  },
  {
    "name": "EXPORT SALES",
    "mark": {
      "type": "bar",
      "size": {"expr": "bandwidth('y') * 0.5" } },
    "encoding": {
      "x": {"field": "Export Sales", "type": "quantitative"},
      "y": {"field": "Country", "type": "ordinal"},
      "color": {"value": "red" } }
  }
 ]
}
```

To share encoding between multiple marks, move the relevant sections of the encoding block outside the layer block:
``` json
{
 "data": {"name": "dataset"},
 "encoding": {
   "y": {"field": "Country", "type": "ordinal"} },
 "layer": [
  {
    "name": "TOTAL SALES",
    "mark": {
      "type": "bar",
      "size": {"expr": "bandwidth('y')"} },
    "encoding": {
      "x": {"field": "Total Sales", "type": "quantitative"},
      "color": {"value": "blue" } } 
  },
  {
    "name": "EXPORT SALES",
    "mark": {
      "type": "bar",
      "size": {"expr": "bandwidth('y') * 0.5" } },
    "encoding": {
      "x": {"field": "Export Sales", "type": "quantitative"},
      "color": {"value": "red" } }
  }
 ]
}
```

> [!NOTE]
> *Layer order is respected by Deneb/Vega-Lite, and layers are rendered in order from first [i.e., bottom] to last [i.e., top])*

> [!NOTE]
> *Encoding in Deneb/Vega-Lite follows a cascading model where the inner encoding inherits the outer encoding, but can also override the outer encoding*

<hr>

## OPACITY
To set the transparency of a visual:
- adjust the opacity of the mark
``` json
"mark": {"type": "bar", "opacity": 0.3}
```

OR 

- add an opacity block to the encoding block:
``` json
"encoding": {
  "x": {"field": "Total Sales__highlight" },
  "opacity": {
    "condition": {
        "test": {"field": "__selected__", "equal": "off"},
        "value": 0},
      "value": 1} } }
```

https://bit.ly/3DxRD9c

<hr>

## TITLE
To set a visual title, add a title block:
``` json
"title": {
  "text": "Total Sales by Country",
  "anchor": "start",
  "align": "left" }
```

<hr>

## WIDGET
To create an input widget, add to the parameters block:
``` json
{ "name": "tension", "value": 0,
"bind" {"input": "range", "min": 0, "max": 1, "step": 0.1} }
```
<br>
https://bit.ly/3zIP4is

<hr>

## TEMPLATE
To export a template of your Deneb visual for reuse:
- click the Generate JSON Template icon in the Visual Editor toolbar
- complete the fields on the Template Information and Dataset (Columns and Measures) tabs
- copy all JSON code from the Generated Template tab and paste into a text editor, saving the file with a .json extension

To use an existing template in your Deneb visual:
- when first editing a Deneb visual, click the Import From Template tab in the Create New Specification dialog
- click the Select JSON Template link in the Create New Specification dialog
- browse to the saved location of the desired .json file and click on the .json file

<br>
https://bit.ly/3Seugqe

<hr>

## EDITOR FONT SIZE
To set the font size in the Deneb Visual Editor, use the JSON Editor Font Size drop-down in the Properties Pane

<hr>
<hr>

# LINK \ EXTEND \ COLOUR \ SPECIFIC

## LINKED CHARTS
To link 2 charts together, add a selection brush to the first visual:
``` json
"selection": {"brush": {
"type": "interval", "encodings": ["x"] } }
```

And use the selection in a filter transform in the second visual:
``` json
"transform": [ { "filter": { "param":  "brush" } } ]
```

https://bit.ly/3JhxaGC

<hr>

## EXTEND DATA/ENHANCE VISUAL
To extend a dataset with derived fields, add a transform block:
•	To refer to an existing dataset field, use the format datum['field name'] (datum.field_name can be used only if there are no spaces in the field name)
``` json
"transform": [
  {"calculate": "2*PI*datum['Ring1 Percent']", "as": "Ring1 Radians" },
  {"calculate": "2*PI*datum['Ring2 Percent']", "as": "Ring2 Radians" },
  {"calculate": "2*PI*datum['Ring3 Percent']", "as": "Ring3 Radians" } ]
```
<br>
https://bit.ly/3ziGPrU

(Many additional transformations are available, including aggregate, filter, flatten, fold, etc.)

https://bit.ly/3Bu4Q21

To enhance a visual with named values or expressions, add a params block:
``` json
"params": [
  { "name": "ring_max", "value": 200 },
  { "name": "ring_width", "value": 20 },
  { "name": "ring_gap", "value": 5 },
  { "name": "ring0_outer", "expr": "ring_max + 2" },
  { "name": "ring0_inner", "expr": "ring_max + 1" },
  { "name": "ring1_outer", "expr": "ring0_inner - ring_gap" } ]
```
<br>
https://bit.ly/3Q7cPWI

Add common math constants to expressions:
``` json
"transform": [
  { "calculate": "2*PI*datum['Ring1 Percent']",
    "as": "Ring1 Radians" } ]
```
https://bit.ly/3PT59I3

<hr>

## NAMED COLOURS
To use a named colour:
``` json
"mark": {
  "type": "area",
  "line": { "color": "darkgreen" } }
```
<br>
To use a built-in Vega-Lite colour scheme:

``` json
"color": {
  "field": "series",
  "scale": { "scheme": "category20b" } }
```
(There are several schemes available, including category20b, dark2, set1, set2, etc.)

<br>
https://bit.ly/3bpoK3l

## THEME COLOURS

To use a specific Power BI theme colour, adjust the theme index (zero-based, so theme colour - 1):

``` json
"color": { "value": { "expr": "pbiColor(0)" } }
```
<br>
https://bit.ly/3vp9Ilg

<hr>

## GRADIENT COLOURS
To add Power BI gradient colours, add to the color block:
``` json
"scale": { "scheme": "pbiColorLinear" }
```

https://bit.ly/3OJXNoE

<hr>

## POWER BI THEME COLOURS

There are 4 integrations with the Power BI theme colours in Deneb:
- ***pbiColorNominal***: matches the current theme colours
- ***pbiColorOrdinal***: uses a ramped scale from the MAX to MIN divergent colour of the current theme, and EXCLUDES the MIDDLE colour
- ***pbiColorLinear***: uses an interpolated gradient from the MAX to MIN divergent colour of the current theme, and EXCLUDES the MIDDLE colour
- ***pbiColorDivergent***: same as pbiColorLinear, but INCLUDES the MIDDLE colour

https://bit.ly/3bdEkPA

<hr>

## LINE CHARTS
To set the smoothing and tension of a line chart, add to the line mark block:
``` json
"interpolate": "cardinal",
"tension": 0.7
```

- options include basis, monotone, natural, step, etc.

https://bit.ly/3RRZSRL

To set the label format for a temporal axis, 
``` json
"axis": { "format": "%y-%b-%d" }
```
<br>
https://bit.ly/3cVA480

## BAR CHARTS
To turn a bar chart into a column chart, exchange the X and Y encoding:
``` json
"encoding": {
  "x": { "field": "Country", "type": "nominal" },
  "y": { "field": "Total Sales", "type": "quantitative" } }
```

To set the column labels to horizontal, add to X encoding block:
``` json
"axis": {
  "labelAngle": 0 }
```

To round all bar corners, add to bar mark:
``` json
"cornerRadius": 10 
```
To round only the ending bar corners, add to bar mark:
``` json
"cornerRadiusEnd": 25
```

https://bit.ly/3DzuXpi

To turn a bar chart into a stacked bar chart, add a color block to the encoding block:
``` json
"color": {
  "field": "Channel",
  "type": "nominal" }
```

https://bit.ly/3zKU4D3

To turn a stacked bar chart into a 100% stacked bar chart, add to the Y encoding block:
``` json
"stack": "normalize"
```

https://bit.ly/3oJKx9e

<hr>

## CIRCULAR CHARTS
To set the size (outer radius) of a pie chart, add a radius or outer radius property to the arc mark block:
``` json
"mark": { 
  "type": "arc",
  "outerRadius": 100 }
```
<br>
To turn a pie chart into a donut chart, add a radius2 or inner radius property to the arc mark block:

``` json
"innerRadius" :  75
```

https://bit.ly/3cUYRcj

To set the start and end of an arc segment (in radians; 0 = “north/up”), use the theta and theta2 properties.

``` json
"mark": {
  "type": "arc",
  "radius": 200,
  "radius2": 100,
  "theta": 0,
  "theta2": 5.9 }
```

https://bit.ly/3vvE9X0

# COMPOSITION \ CONDITION \ MODEL

## VIEW COMPOSITION
There are 4 different types of composite views:
- ***layering***: a set of overlapping specifications (described in more detail on the GENERAL page)
``` json
"layer": [
{ << SPECIFICATION 1 >> },
{ << SPECIFICATION 2 >> } ]
```
- ***concatenation***: a set of specifications concatenated vertically (vconcat), horizontally (hconcat), or so they wrap (concat)
``` json
"hconcat": [
{ << SPECIFICATION 1 >> },
{ << SPECIFICATION 2 >> } ]
```
- ***faceting***: a set of repeating marks each with a subset of the data (like small multiples) using a common specification
``` json
"encoding": {
  "facet": { 
    "field": "Country", "type": "ordinal", "columns": 2 } }
```
> [!NOTE]
> *Faceting doesn't work in an encoding channel for layers; is often an issue when converting a single view to a layered view with facets in them*

- ***repeating***: a set of repeating marks each with the full dataset using a common specification
``` json
{
  "data": {"name": "dataset"},
  "repeat": [
    "Horsepower", "Acceleration", "Displacement"],
  "columns": 2,
  "spec":
  {
    "mark": "bar",
    "encoding": {
      "x": {
        "field": {"repeat": "repeat"}, "bin": true},
      "y": {
        "aggregate": "count"},
      "color": {
        "field": "Origin",
        "scale": {"scheme": "pbiColorNominal"} }
    }
  }
}
```

<hr>

## CONDITIONS
To use a condition to set the value of a property:
- To refer to an existing dataset field, use the format datum['field name'] (datum.field_name can be used only if there are no spaces in the field name)
- Colour of a mark (based on tests):
``` json
"color": { "condition": [
    { "test": "datum['Temp'] <= -20", "value": "#31578C" },
    { "test": "datum['Temp'] <= 5", "value": "#B7D4EB" },
    { "test": "datum['Temp'] <= 25", "value": "#0E5358" } ], 
  "value": "#850000" }
```
- Colour of a mark (based on parameter):
``` json
"color": { "condition": {
    "param": "brush", "field": "Cylinders", "type": "ordinal" },
  "value": "grey" }
```
- Size of a mark (based on mouse selection):
``` json
"size": {"condition": {
    "param": "paintbrush", "value": 600 }, "value": 50 }
```

<hr>

## IF-THEN-ELSE
To add an if-then-else calculation, use the (ifTest ? thenValue : elseValue) form (ternary operator):
``` json
"mark": {
  "type": "bar", 
  "color": {"expr": "datum['Sales'] > 10 ? 'green' : 'red'" }},
```

<hr>

## EXPRESSIONS
To use a parameter, add an expr block:
``` json
"params": [{"name": "my_colour", "value": "blue"}],
...
"mark": {"type": "bar", "color": {"expr": "my_colour" }},
```

<hr>

## DENEB/VEGA-LITE OBJECT MODEL
As Deneb/Vega-Lite is so flexible, a comprehensive object model (DVLOM?) would be quite large with many optional and alternate paths, and it’s complexity might make it less useful. A partial, simplified, and high-level object model showing only a few of the common objects in Deneb/Vega-Lite can provide an overview.

A few notes:
- Many key-value pairs can be used in multiple locations (e.g., opacity in mark or encoding, etc.)
- Many key-value pairs are commonly used with only specific marks (e.g., the “text” encoding is used with a “text” mark, etc.)
- Aside from the common data, mark, and encoding blocks, a specification does not require many of the key-value pairs (thus they are optional) (e.g., formatType, format, title, etc.)	 
