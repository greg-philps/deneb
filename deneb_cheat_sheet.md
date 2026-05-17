# DENEB CHEAT SHEET
- Author: Greg Philps
- Version: 2.0
- Date: May 2026
- Deneb website: https://deneb.guide/
- Vega-Lite website: https://vega.github.io/vega-lite/


## GENERAL
The required 3 basic ***block*** types of a Deneb/Vega-Lite specification are ***data***, ***mark***, and ***encoding***:
``` json
{
  "data": { "name": "dataset" },
  "mark": { "type": "bar" },
  "encoding": {
    "y": { "field": "Country", "type": "nominal" },
    "x": { "field": "Total Sales", "type": "quantitative" },
    "color": { "field": "Channel", "type": "nominal" }
  }
}
```
There are several ***mark*** types in Vega-Lite, including:
- ***common***: bar, line, arc, circle, text, area
- ***other***: point, rect, rule, square, tick, geoshape
- ***composite***: boxplot, errorband, errorbar

https://vega.github.io/vega-lite/docs/mark.html

<br>

There are 4 ***field-mapping*** types:
- ***nominal***: the category of the data (use if string)
- ***quantitative***: the value of the data (use if numeric)
- ***temporal***: the value of the data (use if date)
- ***ordinal***: a ranked order for data sorting 

https://vega.github.io/vega-lite/docs/type.html

<hr>

## SORTING
To sort an axis by another axis, add a ***sort*** key:value pair to its’ encoding block ("-" for descending):
``` json
"sort": "-x"
```
To sort an axis by another field, add a ***sort*** block to its’ encoding block:
``` json
"sort": {
  "op": "sum",
  "field": "Total Sales",
  "order": "descending"
}
```
<hr>

## FORMATTING
To ***format*** a data value using Power BI format strings, add to its’ definition in the encoding block:
``` json
"encoding": {
  "x": {
    "field": "Total Sales",
    "type": "quantitative",
    "formatType": "pbiFormat",
    "format": "#0,,.0M"
  }
}
```

https://deneb.guide/docs/formatting#power-bi-custom-formatter

<hr>

## TOOLTIPS
To enable ***tooltips***, add a property to the mark block:
``` json
"mark": {"type": "bar", "tooltip": true}
```
<br>

To customize tooltips, add a ***tooltip*** block to the encoding block:

``` json
"tooltip": [
  {"field": "Country", "type": "nominal"},
  {"field": "Total Sales", "type": "quantitative", "title": "Sales" }
]
```

https://deneb.guide/docs/interactivity-tooltips

<hr>

## LAYERS
To create a visual using multiple overlapping marks, create a ***layer*** consisting of an array of specifications, one for each mark: 
``` json
{
  "data": { "name": "dataset" },
  "layer": [
    {
      "name": "TOTAL SALES",
      "mark": {
        "type": "bar",
        "size": { "expr": "bandwidth('y')" }
      },
      "encoding": {
        "x": { "field": "Total Sales", "type": "quantitative" },
        "y": { "field": "Country", "type": "ordinal" },
        "color": { "value": "blue" }
      }
    },
    {
      "name": "EXPORT SALES",
      "mark": {
        "type": "bar",
        "size": { "expr": "bandwidth('y') * 0.5" }
      },
      "encoding": {
        "x": { "field": "Export Sales", "type": "quantitative" },
        "y": { "field": "Country", "type": "ordinal" },
        "color": { "value": "red" }
      }
    }
  ]
}
```

To ***share encoding*** between multiple marks, move the relevant sections of the encoding block outside the layer block:
``` json
{
  "data": { "name": "dataset" },
  "encoding": {
    "y": { "field": "Country", "type": "ordinal" }
  },
  "layer": [
    {
      "name": "TOTAL SALES",
      "mark": {
        "type": "bar",
        "size": { "expr": "bandwidth('y')" }
      },
      "encoding": {
        "x": { "field": "Total Sales", "type": "quantitative" },
        "color": { "value": "blue" }
      }
    },
    {
      "name": "EXPORT SALES",
      "mark": {
        "type": "bar",
        "size": { "expr": "bandwidth('y') * 0.5" }
      },
      "encoding": {
        "x": { "field": "Export Sales", "type": "quantitative" },
        "color": { "value": "red" }
      }
    }
  ]
}

```

> [!NOTE]
> *Layer order is respected by Deneb/Vega-Lite, and layers are rendered in order from first [i.e., bottom] to last [i.e., top])*

> [!NOTE]
> *Encoding in Deneb/Vega-Lite follows a cascading model where the inner encoding ***inherits*** the outer encoding, but can also ***override*** the outer encoding*

<hr>

## OPACITY
To set the transparency of a visual:
- adjust the **_opacity_** of the mark
``` json
"mark": {"type": "bar", "opacity": 0.3}
```

OR 

- add an ***opacity*** block to the ***encoding*** block:
``` json
"encoding": {
  "x": { "field": "Total Sales__highlight" },
  "opacity": {
    "condition": {
      "test": { "field": "__selected__", "equal": "off" },
      "value": 0
    },
    "value": 1
  }
}
```

https://vega.github.io/vega-lite/docs/mark.html#color

<hr>

## TITLE
To set a visual title, add a ***title*** block:
``` json
"title": {
  "text": "Total Sales by Country",
  "anchor": "start",
  "align": "left" }
```

https://vega.github.io/vega-lite/docs/title.html

<hr>

## WIDGET
To create an ***input widget***, add to the ***params*** block:
``` json
"params": [
  {
    "name": "tension",
    "value": 0,
    "bind": { "input": "range", "min": 0, "max": 1, "step": 0.1 }
  }
]
```

https://vega.github.io/vega-lite/examples/interactive_query_widgets.html

<hr>

## TEMPLATE
To export a ***template*** of your Deneb visual for reuse:
- click the ***Generate JSON Template*** icon in the Visual Editor toolbar
- complete the fields in the ***Template Information*** and ***Dataset (Columns and Measures)*** sections
- click the ***Download*** button and save the file (it will have a ***.json*** extension)

To use an existing ***template*** for your Deneb visual:
- create a native ***table*** visual and add all data columns
- change the table visual to a ***Deneb*** visual
- edit the Deneb visual and select the ***Existing Template*** radio-button option in the ***Create Using...*** section of the ***Create or Import New Specification*** dialog
- browse to the saved location of the desired ***.json*** file and click on the ***.json*** file
- click the ***Create*** button

https://deneb.guide/docs/templates

<hr>

## EDITOR FONT SIZE
To set the font size in the Deneb Visual Editor, use the ***Visual / Advanced Editor / JSON Editor / JSON Editor Font Size*** drop-down in the ***Format Pane***

<img width="136" height="452" alt="JSON Editor Font Size" src="https://github.com/user-attachments/assets/a8d43993-4ef4-491b-9cba-cf9eeab0630b" />

https://deneb.guide/docs/visual-editor#editor-pane

<hr>

## LINKED CHARTS
To link 2 charts together, add a ***selection brush*** to the first visual:
``` json
"selection": {"brush": {
"type": "interval", "encodings": ["x"] } }
```

And use the selection in a ***filter transform*** in the second visual:
``` json
"transform": [ { "filter": { "param":  "brush" } } ]
```

https://vega.github.io/vega-lite/examples/interactive_overview_detail.html

<hr>

## EXTEND DATA/ENHANCE VISUAL
To extend a dataset with derived fields, add a ***transform*** block:
•	To refer to an existing dataset field, use the format ***datum['field name']*** (***datum.field_name*** can be used only if there are no spaces in the field name)
``` json
"transform": [
  {"calculate": "2*PI*datum['Ring1 Percent']", "as": "Ring1 Radians" },
  {"calculate": "2*PI*datum['Ring2 Percent']", "as": "Ring2 Radians" },
  {"calculate": "2*PI*datum['Ring3 Percent']", "as": "Ring3 Radians" }
]
```

https://vega.github.io/vega-lite/docs/calculate.html

(Many additional transformations are available, including aggregate, filter, flatten, fold, etc.)

https://vega.github.io/vega-lite/docs/transform.html

To enhance a visual with named values or expressions, add a ***params*** block:
``` json
"params": [
  { "name": "ring_max", "value": 200 },
  { "name": "ring_width", "value": 20 },
  { "name": "ring_gap", "value": 5 },
  { "name": "ring0_outer", "expr": "ring_max + 2" },
  { "name": "ring0_inner", "expr": "ring_max + 1" },
  { "name": "ring1_outer", "expr": "ring0_inner - ring_gap" }
]
```

https://vega.github.io/vega-lite/docs/parameter.html

Add common math ***constants*** to expressions:
``` json
"transform": [
  { "calculate": "2*PI*datum['Ring1 Percent']", "as": "Ring1 Radians" }
]
```

https://vega.github.io/vega/docs/expressions/#constants

<hr>

## NAMED COLOURS

To use a ***named*** colour:
``` json
"mark": {
  "type": "area",
  "line": { "color": "darkgreen" }
}
```
<br>

To use a built-in Vega-Lite ***colour scheme***:

``` json
"color": {
  "field": "series",
  "scale": { "scheme": "category20b" }
}
```
> [!NOTE]
> *There are several schemes available, including category20b, dark2, set1, set2, etc.*

https://vega.github.io/vega/docs/schemes/

<hr>

## THEME COLOURS

To use a specific ***Power BI theme colour***, use the ***pbiColor*** Deneb function in a Vega-Lite ***expression*** and set the theme index (zero-based, so theme colour - 1):

``` json
"color": { "value": { "expr": "pbiColor(0)" } }
```

https://deneb.guide/docs/schemes

<hr>

## GRADIENT COLOURS

To add Vega-Lite ***gradient*** colours, add a color sub-block to the mark block:
``` json
{
  "mark": {
    "type": "area",
    "line": { "color": "darkgreen" },
    "color": {
      "x1": 1,
      "y1": 1,
      "x2": 1,
      "y2": 0,
      "gradient": "linear",
      "stops": [
        {
          "offset": 0,
          "color": "white"
        },
        {
          "offset": 1,
          "color": "darkgreen"
        }
      ]
    }
  }
}
```

https://vega.github.io/vega-lite/docs/gradient.html#example-gradient-area-plot

<hr>

## POWER BI THEME COLOURS
There are 4 integrations with the Power BI theme colours in Deneb:
- ***pbiColorNominal***: matches the current theme colours
- ***pbiColorOrdinal***: uses a ramped scale from the MAX to MIN divergent colour of the current theme, and EXCLUDES the MIDDLE colour
- ***pbiColorLinear***: uses an interpolated gradient from the MAX to MIN divergent colour of the current theme, and EXCLUDES the MIDDLE colour
- ***pbiColorDivergent***: same as ***pbiColorLinear***, but INCLUDES the MIDDLE colour

https://deneb.guide/docs/schemes

To add Power BI ***gradient*** colours, add to the color block:
``` json
"scale": { "scheme": "pbiColorLinear" }
```

<img width="350" height="150" alt="Bar Chart 6" src="https://github.com/user-attachments/assets/635d8ca4-e827-42d3-8216-a230fc377399" />

<hr>

## LINE CHARTS
To set the smoothing and tension of a line chart, add to the ***line*** mark block:
``` json
"interpolate": "cardinal",
"tension": 0.7
```

- options include basis, monotone, natural, step, etc.

https://vega.github.io/vega-lite/docs/line.html#properties

To set the label format for a temporal axis, 
``` json
"axis": { "format": "%y-%b-%d" }
```

https://github.com/d3/d3-time-format#locale_format

<hr>

## BAR CHARTS
To turn a bar chart into a ***column*** chart, exchange the X and Y encoding:
``` json
"encoding": {
  "x": { "field": "Country", "type": "nominal" },
  "y": { "field": "Total Sales", "type": "quantitative" }
}
```

To set the column labels to ***horizontal***, set the ***labelAngle*** value in the axis encoding block:
``` json
"axis": {
  "labelAngle": 0
}
```

To ***round*** all bar corners, add to bar mark:
``` json
"cornerRadius": 10 
```
To ***round*** only the ending bar corners, add to bar mark:
``` json
"cornerRadiusEnd": 25
```
To control the corner rounding even further, set the ***cornerRadiusTopLeft***, ***cornerRadiusTopRight***, ***cornerRadiusBottomRight***, and ***cornerRadiusBottomLeft*** keys.

https://vega.github.io/vega-lite/docs/bar.html#bar-chart-with-rounded-corners

To turn a bar chart into a ***stacked bar chart***, add a color block to the encoding block:
``` json
"color": {
  "field": "Channel",
  "type": "nominal"
}
```

https://vega.github.io/vega-lite/docs/bar.html#stack

To turn a stacked bar chart into a ***100% stacked bar chart***, add to the Y encoding block:
``` json
"stack": "normalize"
```

https://vega.github.io/vega-lite/docs/bar.html#normalized-stacked-bar-chart

<hr>

## CIRCULAR CHARTS
To set the size (outer radius) of a ***pie*** chart, add a radius or outer radius property to the arc mark block:
``` json
"mark": { 
  "type": "arc",
  "outerRadius": 100
}
```
<br>

To turn a pie chart into a ***donut*** chart, add a radius2 or inner radius property to the arc mark block:

``` json
"innerRadius" :  75
```

https://vega.github.io/vega-lite/docs/arc.html#pie-and-donut-charts

To set the start and end of an arc segment (in radians; 0 = “north/up”), use the ***theta*** and ***theta2*** properties.

``` json
"mark": {
  "type": "arc",
  "radius": 200,
  "radius2": 100,
  "theta": 0,
  "theta2": 5.9
}
```

https://vega.github.io/vega-lite/docs/arc.html#properties

<img width="400" height="400" alt="Ring Chart" src="https://github.com/user-attachments/assets/28d3ba88-022e-48f1-a6f6-ff1e66bbea4e" />

# COMPOSITION \ CONDITION \ MODEL

## VIEW COMPOSITION
There are 4 different types of composite views:
- ***layering***: a set of overlapping specifications (described in more detail on the GENERAL page)
``` json
"layer": [
{ << SPECIFICATION 1 >> },
{ << SPECIFICATION 2 >> }
]
```
- ***concatenation***: a set of specifications concatenated vertically (***vconcat***), horizontally (***hconcat***), or so they wrap (***concat***)
``` json
"hconcat": [
{ << SPECIFICATION 1 >> },
{ << SPECIFICATION 2 >> }
]
```
- ***faceting***: a set of repeating marks each with a subset of the data (like small multiples) using a common specification
    - use the ***facet*** operator or use the ***facet***, ***column***, or ***row*** encoding channels
``` json
{
  "data": { "name": "dataset" },
  "facet": {
    "column": {
      "field": "Origin"
    }
  },
  "spec": {
    "height": 400,
    "width": 300,
    "mark": "bar",
    "encoding": {
      "x": {
        "bin": { "maxbins": 10 },
        "field": "Horsepower",
        "type": "quantitative"
      },
      "y": {
        "aggregate": "count",
        "type": "quantitative"
      },
      "color": {
        "field": "Origin",
        "scale": { "scheme": "pbiColorNominal" },
        "legend": null
      }
    }
  }
}
```
> [!NOTE]
> *Faceting doesn't work in an encoding channel for layers; is often an issue when converting a single view to a layered view with facets in them*
> *A ***layer*** cannot contain a ***facet***, but a ***facet*** can contain a ***layer****

- ***repeating***: a set of repeating marks each with the full dataset using a common specification
``` json
{
  "data": {
    "name": "dataset"
  },
  "repeat": ["Horsepower", "Miles_per_Gallon", "Acceleration", "Displacement"],
  "columns": 2,
  "spec": {
    "mark": {
      "type": "bar"
    },
    "encoding": {
      "x": {
        "field": {
          "repeat": "repeat"
        },
        "bin": true
      },
      "y": {
        "aggregate": "count"
      },
      "color": {
        "field": "Origin",
        "scale": {
          "scheme": "pbiColorNominal"
        }
      }
    }
  }
}

```

https://vega.github.io/vega-lite/docs/composition.html

<img width="500" height="500" alt="View Composition - Repeat" src="https://github.com/user-attachments/assets/23c93c81-a413-44c8-b74d-1c5d0c8af462" />

<hr>

## CONDITIONS
To use a condition to set the value of a property:
- To refer to an existing dataset field, use the format ***datum['field name']*** (***datum.field_name*** can be used only if there are no spaces in the field name)
- Colour of a mark (based on tests):
``` json
"color": {
  "condition": [
    { "test": "datum['Temp'] <= -20", "value": "#31578C" },
    { "test": "datum['Temp'] <= 5", "value": "#B7D4EB" },
    { "test": "datum['Temp'] <= 25", "value": "#0E5358" }
  ],
  "value": "#850000"
}
```
- Colour of a mark (based on parameter):
``` json
"color": {
  "condition": {
    "param": "brush",
    "field": "Cylinders",
    "type": "ordinal"
  },
  "value": "grey"
}
```
- Size of a mark (based on mouse selection):
``` json
"size": {"condition": {
    "param": "paintbrush", "value": 600 }, "value": 50 }
```

https://vega.github.io/vega-lite/docs/condition.html

<hr>

## IF-THEN-ELSE
To add an if-then-else calculation, use the standard form [if( test, thenValue, elseValue )]
``` json
"mark": {
  "type": "bar",
  "color": { "expr": "if( datum['Sales'] > 10, 'green', 'red' )" }
}
```
OR the ternary form [ test ? thenValue : elseValue ]
``` json
"mark": {
  "type": "bar",
  "color": { "expr": "datum['Sales'] > 10 ? 'green' : 'red'" }
}
```

https://vega.github.io/vega/docs/expressions/

<hr>

## EXPRESSIONS
To use a parameter, add an ***expr*** block:
``` json
"params": [{"name": "my_colour", "value": "blue"}],
...
"mark": {"type": "bar", "color": {"expr": "my_colour" }},
```

https://vega.github.io/vega-lite/docs/parameter.html#expr

<hr>

## DENEB/VEGA-LITE OBJECT MODEL
As Deneb/Vega-Lite is so flexible, a comprehensive ***object model*** (DVLOM?) would be quite large with many optional and alternate paths, and it’s complexity might make it less useful. A partial, simplified, and high-level object model showing only a few of the common objects in Deneb/Vega-Lite can provide an overview.

<img width="600" height="750" alt="Deneb_Vega-Lite Object Model - V2" src="https://github.com/user-attachments/assets/efb16b25-ddb6-48be-97ae-33283a5f169f" />

A few notes:
- Many key-value pairs can be used in multiple locations (e.g., opacity in mark or encoding, etc.)
- Many key-value pairs are commonly used with only specific marks (e.g., the “text” encoding is used with a “text” mark, etc.)
- Aside from the common ***data***, ***mark***, and ***encoding*** blocks, a specification does not require many of the key-value pairs (thus they are optional) (e.g., formatType, format, title, etc.)

*- eof*
