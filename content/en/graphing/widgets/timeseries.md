---
title: Timeseries Widget
kind: documentation
further_reading:
- link: "graphing/dashboards/timeboard/"
  tag: "Documentation"
  text: "Timeboards"
- link: "graphing/dashboards/screenboard/"
  tag: "Documentation"
  text: "Screenboard"
---

The timeseries visualization allows you to show the evolution of one or more metrics, log events, or APM events over time. The time window depends on what is selected on the [timeboard][1] or [screenboard][2]:

{{< img src="graphing/widgets/timeseries/timeseries.png" alt="Timeseries" responsive="true">}}

## Setup

{{< img src="graphing/widgets/timeseries/timeseries_setup.png" alt="Timeseries setup" responsive="true" style="width:80%;" >}}

### Configuration

1. Choose the data to graph:
    * Metric: See [the main graphing documentation][3] to configure a metric query.
    * APM Events: See [the trace search documentation][4] to configure an APM event query.
    * Log Events: See [the log search documentation][5] to configure an APM event query.

2. Customize your graph with the available [options](#options).

### Options
#### Line graphs

Line graphs include two additional parameters:

| Parameter | Options               |
|-----------|-----------------------|
| Style     | Solid, Dashed, Dotted |
| Stroke    | Normal, Thin, Thick   |

#### Appearance

Graphs can be displayed as areas, bars, or lines. For all graph types, Datadog offers various color options to differentiate multiple metrics displayed on the same graph:

| Palette   | Description                                                                                                |
| --------- | ---------------------------------------------------------------------------------------------------------- |
| Classic   | The simple colors light blue, dark blue, light purple, purple, light yellow, and yellow (colors repeat).   |
| Cool      | A gradient color scheme made from green and blue.                                                          |
| Warm      | A gradient color scheme made from yellow and orange.                                                       |
| Purple    | A gradient color scheme made from purple.                                                                  |
| Orange    | A gradient color scheme made from orange.                                                                  |
| Gray      | A gradient color scheme made from gray.                                                                    |

For line graphs, different metrics can be assigned specific palettes by separating the queries in JSON.

#### Metric aliasing

Each query or formula can be aliased. The alias overrides the display on the graph and legend, which is useful for long metric names. At the end of the query/formula click on **as...**, then enter your metric alias:

{{< img src="graphing/index/metric_alias.png" alt="metric alias" responsive="true" style="width:75%;" >}}

##### Event Overlay

Add events from related systems to add more context to your graph. For example, you can add GitHub commits, Jenkins deploys, or Docker creation events. Expand the **Event Overlays** section and enter a query to display those events. Use the same query format as for the [Event Stream][6], for example:

| Query                         | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| `sources:jenkins`             | Shows all events from the Jenkins source.                    |
| `tag:role:web`                | Shows all events with the tag `role:web`.                    |
| `tags:$<TEMPLATE_VARIABLE>`   | Shows all events from the selected [Template Variable][7].   |

##### Y-axis controls

Y-axis controls are available via the UI and the JSON editor. They allow you to:

* Clip the y-axis to specific ranges.
* Remove outliers either by specifying a percentage or an absolute value to remove outliers.
* Change the y-axis scale from linear to log, pow, or sqrt.

Change the Y-axis scale by expanding the *Y-Axis Controls* button.

The following configuration options are available:

| Option                  | Required   | Description                                                                                                                                                                                                         |
| ----------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Min`/`Max`             | No         | Specify the minimum and/or maximum value to show on y-axis. It takes a number or `Auto` as the default value.                                                                                                       |
| `Scale`                 | No         | Specifies the scale type. Possible values:<br>- *linear*: A linear scale (default)<br>- *log*: A logarithmic scale<br>- *pow*: A Power of 2 scale (2 is default, modify in JSON)<br>- *sqrt*: A square root scale   |
| `Always include zero`   | No         | Always include zero or fit the axis to the data range. The default is to always include zero.                                                                                                                       |

**Note**: Because the mathematical log function doesn't accept negative values, the Datadog log scale only works if values are of the same sign (everything > 0 or everything < 0). Otherwise an empty graph is returned.

## API

The dedicated [widget JSON schema definition][8] for the change widget is: 

```
  "definition": {
    "type": "timeseries",
    "requests": ["<REQUEST_SCHEMA>"],
    "yaxis":  <AXIS_SCHEMA>,
    "markers": <MARKERS_SCHEMA>,
    "events": <EVENTS_SCHEMA>,
    "title": "<WIDGET_TITLE>"
  }
```

| Parameter  | Type             | Description                                                                                                                                      |
| ------     | -----            | --------                                                                                                                                         |
| `type`     | string           | Type of the widget, for the group widget use `timeseries`                                                                                        |
| `requests` | array of strings | List of request to display in the widget. See the dedicated [Request JSON schema documentation][9] to learn how to build the `<REQUEST_SCHEMA>`. |
| `yaxis`    | object           | Y-axis control options. See the dedicated [Y-axis JSON schema documentation][10] to learn how to build the `<AXIS_SCHEMA>`.                      |
| `events`   | object           | Event overlay control options. See the dedicated [Events JSON schema documentation][11] to learn how to build the `<EVENTS_SCHEMA>`              |
| `title`    | string           | Title of your widget.                                                                                                                            |

Additional properties allowed in a request:

```
{
    "style": {
        "palette": "<PALETTE_STYLE>"
        "line_type":  "<LINE_TYPE>"
        "line_width": "<LINE_WIDTH>"
    },
    "metadata": [
        {
            "expression": "<EXPRESSION>",
            "alias_name": "<ALIAS_NAME>"
        }
    ],
    "display_type": "<DISPLAY_TYPE>"
}
```

| Parameter          | Type   | Description                                                                             |
| ------             | -----  | --------                                                                                |
| `style.palette`    | string | Color palette to apply to the widget.                                                   |
| `style.line_type`  | string | Type of lines displayed. Available values are: `dashed`, `dotted`, or `solid`.          |
| `style.line_width` | string | Width of line displayed. Available values are: `normal`, `thick`, or `thin`.            |
| `metadata`         | object | Used to define expression aliases.                                                      |
| `display_type`     | enum   | Type of display to use for the request, available value are: `area`, `bars`, or `line`. |


## Further Reading

{{< partial name="whats-next/whats-next.html" >}}


[1]: /graphing/dashboards/timeboard
[2]: /graphing/dashboards/screenboard
[3]: /graphing
[4]: /tracing/visualization/search/#search-bar
[5]: https://docs.datadoghq.com/logs/explorer/search/#search-syntax
[6]: /graphing/event_stream
[7]: /graphing/dashboards/template_variables
[8]: /graphing/graphing_json/widgets_json
[9]: /graphing/graphing_json/request_json
[10]: /graphing/graphing_json/widget_json/#y-axis-schema
[11]: /grpahing/graphing_json/widget_json/#events-schema