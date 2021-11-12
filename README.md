# Interactive Visualization in Science:
## Resources and advice for Python + JavaScript
*by Adam Willats*

## Choose your own adventure!
- *yeah yeah yeah, skip the lists & resources*, just tell me [which plotting library to use](#which-plotting-library-should-i-use)
- [just show me cool-looking plots!](#useful-plotting-techniques)

## Table of Contents 
- [Comparing plotting libraries](#visualization-library-options)
  - [Which plotting library should I use?](#which-plotting-library-should-i-use)
  - [Handling large datasets](#handling-large-datasets)
  - [Interactivity](#interactivity)
  - [Embedding results](#embedding-results)
- Advice for data visualization 
  - [useful plotting techniques](#useful-plotting-techniques)
  - [more resources](#sources-inspiration-more-resources)
  - [further musings](#further-musings)
-------
## Context / background

### What do I want from visualization libraries ?
I'm interested in using python and javascript to interactively explore problems in computational neuroscience and neuroengineering.
I'm also interested in using interactive visualizations to teach concepts to others.
  *here's some of my work:*
  - dynamics visualizer [code :fa-github:](https://github.com/awillats/dynamics-visualizer-p5), [demo](https://awillats.github.io/dynamics-visualizer-p5/) 
  - cross-correlation visualizer [code :fa-github:](https://github.com/awillats/xcorr-visualizer-p5) [demo](https://awillats.github.io/xcorr-visualizer-p5/) (🚧 work in progress 🚧)


![holoviz docs image of high level + low level](https://holoviz.org/assets/shortcuts.png)
*image from [HoloViz documentation](https://holoviz.org/background.html#shortcuts-not-dead-ends)*

**my priorities are:**
- must allow rich interactivity (zooming, filtering, linking plots)
- robust handling of multiple 10k sample timeseries
- expressive, functional-style flexible mapping of (nested) indices to plots 
  - I want to be able to explore relationships between variables in high(er) dimensional parameter sweeps
  - bonus points if it can render 3D plots
- easy to embed / host results, especially in static webpages
  - easy to share, and for others to access, preferrably without complex tools


## Visualization library options
Here I focus on a *subset* of solutions which I think are most promising for the interactive use-case.
*see also [pyviz.org](https://pyviz.org/overviews/index.html) or "Dynamic science viz.."[^dyna] for a more comprehensive evaluation.*
| library      | language for computation | [lots of data](#handling-large-datasets) | [custom js](#custom-callbacks) | [easy to embed](#embedding-results)              | [interactivity](#interactivity) | 3D plots    |
| ------------ | ------------------------ | ---------------------------------------- | ------------------------------ | ------------------------------------------------ | ------------------------------- | --- |
| Plotly /Dash | python[^plotly_multi]    | yes, webgl + datashader                  | sort of - via Dash [^dash]     | yes, dash is more flexible, but more complicated | high                            | yes    |
| Bokeh        | python (+ js)            | yes, column-data & server solutions      | yes                            | yes                                              | high                            | yes-ish    |
| Altair       | python                   | no [^alt_dat]️                           | difficult                      | yes, through vega-lite                           | medium                          | no    |
| HoloViz      | python                   | via datashader                           | yes                            | yes                                              | high                            | yes, through plotly    |
| Observable   | javascript[^why_obs]     | yes[^obs_data], stream from server       | yes [^obs]                     | yes, can also embed single cells                 | high                            | yes, through js libraries    |
<!-- add 3D column? -->
<!-- Altair doesn't render well in Atom  -->
[^plotly_multi]: Plotly also has interfaces in R, MATLAB, Julia, JavaScript
[^alt_dat]: there are some workarounds in progress for [Altair w/ large datasets](#large-datasets-in-Altair)
[^obs]: [Observable's not JavaScript](https://observablehq.com/@observablehq/observables-not-javascript) 
[^obs_data]: data capacity / capabilities differ between public and private notebooks
[^why_obs]: While Observable notebooks can't currently execute Python, I've included it here because I do think it's a promising solution for interactive data-science notebooks. At the moment my workflow would look something like performing the primary analysis in Python, exporting the results to `.csv` then importing that to Observable for visualization.

[^dash]: Dash tries to provide a pure-Python interface to mimic the roles of HTML, JS, CSS in traditional websites. > "Dash abstracts away all of the technologies and protocols that are required to build a full-stack web app with interactive data visualization." [dash callbacks](https://dash.plotly.com/basic-callbacks)
[^dyna]:  [Dynamic scientific visualizations in the browser for Python users](https://xcorr.net/2021/06/02/dynamic-scientific-visualizations-in-the-browser-for-python-users/) by Patrick Mineault 

#### other libraries / ecosystems **not** investigated / compared here:
- [matplotlib](https://matplotlib.org/) - the *de facto* standard for (non-interactive, 2D) plotting in Python
  - [Seaborn](https://seaborn.pydata.org/index.html) - high-level "opinionated defaults" library built on matplotlib
  - [mpld3](https://mpld3.github.io/) - "The mpld3 project brings together Matplotlib, the popular Python-based graphing library, and D3js, the popular JavaScript library for creating interactive data visualizations for the web."
- [plotnine](https://plotnine.readthedocs.io/en/stable/) - like Altair, this is a Python library which implements a grammar of graphics approach to plotting
- [Streamlit](https://streamlit.io/gallery) - python dashboarding library becoming increasingly popular


- [weights and biases](https://wandb.ai/site) - visualization & logging for machine learning 
  - has great table view [page](https://wandb.ai/stacey/mnist-viz/reports/Guide-to-W-B-Tables--Vmlldzo2NTAzOTk), [live demo](https://wandb.ai/stacey/mnist-viz/artifacts/predictions/baseline/d888bc05719667811b23/files/predictions.table.json#2bb3b1d40aa777496b5d$2x_layers_2x_lr)
  - [incremental logging](https://docs.wandb.ai/guides/track/log) of diverse data-types
    - flexible enough to be used outside ML
  - could fit with use-case of having to stop mid-simulation to offload data
  - wrapper functions for managing [hyperparameter sweeps](https://docs.wandb.ai/guides/sweeps)
  - tools for [visualizing results in progress](https://docs.wandb.ai/guides/data-vis)
- see also: 
  - The Python Visualization Landscape (2017) [video of talk](https://youtu.be/FytuB8nFHPQ) by Jake VanderPlas
  - lots more comparisons of python viz tools at [pyviz.org](https://pyviz.org/overviews/index.html)
![Adaptation of Jake VanderPlas graphic about the Python visualization landscape, by Nicolas P. Rougier](https://rougier.github.io/python-visualization-landscape/landscape-colors.png)
*Adaptation of Jake VanderPlas graphic about the Python visualization landscape, by Nicolas P. Rougier, via [PyViz.org](https://pyviz.org/overviews/index.html)*

### Lineage / taxonomy of libraries
<details><summary> breakdown of where libraries came from / were inspired by</summary> 

- [D3.js](https://d3js.org/)
  - best as remapping data to visual features 
  - comparatively "low-level" specification of plots
  - many libraries built from, or inspired by D3
    - [D3 -> Vega](https://vega.github.io/vega/about/vega-and-d3/) 
      - provides a higher-level, more concise interface to plots
      - [Vega -> Vega-lite](https://vega.github.io/vega-lite/)
        - making Vega simpler, provides an even higher-level interface
        - [Vega-lite -> Altair](https://altair-viz.github.io/)
          - bringing vega-lite's declarative visualization to python 
          - [Altair discussion group](https://groups.google.com/g/altair-viz)
        - see "Exploratory Data Visualization with Vega, Vega-Lite, and Altair" - PyCon 2018 [video of talk](https://youtu.be/ms29ZPUKxbU) by Jake VanderPlas for examples and context
    
    - [D3 -> Plotly](https://plotly.com/javascript/)
      - is a javascript library with interfaces to several languages:
        - [Python](https://plotly.com/python/)
        - [R](https://plotly.com/R/) 
        - [Julia](https://plotly.com/julia/)
        - [MATLAB](https://plotly.com/matlab/)
      - Plotly is somewhat unique in the Python landscape for having rich, interactable 3D plots

- Bokeh is inspired by, but [not built on D3.js](https://docs.bokeh.org/en/0.10.0/docs/faq.html#does-bokeh-use-d3-js)
- HoloViz is a very high-level tool 
  - which allows combinations of many of the above libaries 
    - matplotlib, Seaborn
    - Bokeh 
    - Plotly
    - Altair / Vega 
    - ggplot2
      
- Grammar of Graphics *(see [sources](#sources-inspiration-more-resources) for more info)*
  - explicit framework for:
    - Vega -> Vega-lite -> Altair 
    - ggplot / ggplot2 -> plotnine
    - Observable:Plot
  - influenced 
    - Plotly 
    - Seaborn 
    - Bokeh
    
- Pandas DataFrames
  - several of the above plotting libraries depend on (or work best with) data being in pandas dataframes
  - they also work most smoothly if the data is organized in "tidy" form *(see [resources here](#tidying-data))*
  - [10 minutes to pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/10min.html)
  - [Python for Financial Data Analysis with pandas](https://www.slideshare.net/wesm/python-for-financial-data-analysis-with-pandas) by Wes McKinney
    - <details><summary>Design philosophy</summary>
      - Clean axis indexing design to support fast data alignment, loops, hierarchical indexing and more 
      - Think outside the matrix: stop thinking about shape and start thinking about indices 
      - Fault tolerance: save you from common blunders caused by coding errors (misaligned data)
      - Hierarchical indexing + `group_by` database operations
      </details>

</details>        

### Which plotting library should I use? 
It depends on your goals and use case. Select the goals that line up most closely with what you want and I'll recommend a library to you.

- <details><summary>🛠️ I just want to publish a paper (or other non-interactive publication), and I want to be able to tweak the details of the figure</summary>
  
  **matplotlib**
</details>

- <details><summary>⚔️ I'm not interested in learning the intricacies of how to draw ellipses on a computer, just give me nice looking plots with minimal code</summary>
  
  **Seaborn**
  - you can always fine-tune details with **matplotlib** later if needed.
  </details>

- <details><summary>🎻 I love the elegance of the grammar of graphics approach, and work mostly with small to medium-sized datasets. Easily specified interactivity would be a a bonus</summary>
  
  **Altair**

  </details>
  
- <details><summary>🧙 I want interactivity, to be able to switch between python and javascript, and from small to large datasets (even if it takes more code)</summary>

    **Bokeh**
  </details>
  
- <details><summary>🏹 I want the maximum amount of plotting features (3D plots, interactivity, dashboards) without having to write too much code</summary>
    
    **Plotly**
  </details>

- <details><summary>🧰 I have no loyalties to a particular plotting library, let me mix-and-match the best tools for the job</summary>
  
  **HoloViz**
  </details>

### Design philosophies & features
**Ploty** 🚧
One double-edged feature of Plotly is the gradient of multiple approaches to achieve the same plot:
- Good-looking plots can be put together very quickly with **Plotly express** 
- If you need more customization, you often end up switching to Plotly's **graph objects**. This has a similar level of customization to matplotlib 
- If you want to compose multiple plots together, you use Plotly's **figure factor**
While this flexible allows you to pick the right tool for the job, it makes looking through the documentation much more confusing.

  <!-- unfortunately multiple ways to accomplish a single task means looking through documentation can be difficult -->

**Altair** 🚧
  The biggest part of the Altair learning curve for me was getting my data into the correct form. It was also disheartening to fall in love with the grammar of graphics approach, only to run into a wall when trying to plot many timeseries[^alt_dat]. 
  
**Bokeh** [technical vision](https://docs.bokeh.org/en/0.10.0/index.html#technicalvision)
  - attempts to address "How do we look at all the data?" and "How can scientists and data analysts be empowered to use visualization fluidly, not merely as an output facility or one stage of a pipeline, but as an entire mode of engagement with data and models?"

**HoloViz** 🚧
- [**HoloViz Goals:** talk](https://holoviz.org/talks/index.html) 
  - Full functionality in browsers (not desktop)
  - Full interactivity (inside and out of plots)
  - Focus on Python users, not web programmers
  - Start with data, not coding
  - Work with data of any size
  - Exploit general-purpose SciPy/PyData tools
  - Focus on 2D primarily, with some 3D
  - Avoid entangling your data, code, and viz:
    - Same viz/analysis code in Jupyter, Python, HPC, ...
    - Widgets/apps in Jupyter, standalone servers, web pages
    - Jupyter as a tool, not part of the results
    
**Observable** 🚧
  - created by designer of D3.js Mike Bostock
  - reactive javascript notebooks 
  - often compared to excel, in that cells of code automatically rerun when their predecessors change value (unlike jupyter notebooks)
    - [Reactive Dataflow](https://observablehq.com/@observablehq/reactive-dataflow?collection=@observablehq/learning-observable) in Observable




------------  

### Handling large datasets

- [big data analytics with pandas and sqlite](https://plotly.com/python/v3/ipython-notebooks/big-data-analytics-with-pandas-and-sqlite/)
- Options for nesting data: take a look at [Dask](https://docs.dask.org/en/latest/why.html)

#### large datasets in Bokeh 
  - Arguably, Bokeh has the best suite of tools for visualizing large datasets
  - Bokeh's [`ColumnDataSource` [docs]](https://docs.bokeh.org/en/latest/docs/user_guide/data.html) allows more flexible partial loading of data leading to better performance with large datasets
    - slices of data can be extracted with [`CDSView` [docs]](https://docs.bokeh.org/en/latest/docs/user_guide/data.html#filtering-data)
  - [Running a Bokeh server](https://docs.bokeh.org/en/latest/docs/user_guide/server.html)
  - [Accelerating with WebGL](https://docs.bokeh.org/en/latest/docs/user_guide/webgl.html)
    
#### large datasets in Plotly
- [Proper way to plot large datasets](https://community.plotly.com/t/proper-way-to-plot-large-datasets/9793)
- [WebGL vs SVG in Python](https://plotly.com/python/webgl-vs-svg/)
  - millions of points! 
  - restricted to scatter plots only?
- datashader for aggregation / density estimation
    [Mapping over 1 Million points with Plotly Datashader](https://youtu.be/6cGlBFz71Oc)
  
#### large datasets in Altair 
- [Slow rendering. Lazy loading possible?](https://github.com/altair-viz/altair/issues/1511)
- [State of altair/ vega-lite and large datasets?](https://groups.google.com/g/altair-viz/c/ScHfqzMpLw4)
- [Interactive chart slow with large number of data points](https://github.com/altair-viz/altair/issues/688)
    - fastdom = big performance improvements
    
- [scaleable-vega](https://github.com/vega/scalable-vega) for lots of data-points 
    
- [also, could instead host and grab data via url](https://altair-viz.github.io/user_guide/faq.html#why-does-altair-lead-to-such-extremely-large-notebooks)

- [Streaming data into Vega](https://vega.github.io/vega-lite/tutorials/streaming.html)
  - see also [observable section](#large-datasets-in-Observable)

- just adding data to a database seems to dramatically slow down altair, even if the data isn't actively being rendered 
  - some confirmation of that here:
    - [Vega-Lite with Streaming Data Updates](https://observablehq.com/@vega/vega-lite-with-streaming-data-updates)
- [++] [Streaming Data](https://vega.github.io/vega-lite/tutorials/streaming.html)
- also discussion here: [altair discussion](https://stackoverflow.com/questions/61298647/how-to-add-modify-patch-altair-chart-data-dynamically)

-   for local exploration, can use [altair-data-server](https://github.com/altair-viz/altair_data_server/blob/master/AltairDataServer.ipynb) 
    > This notebook shows an example of using the Altair data server, a lightweight plugin for Altair that lets you efficiently and transparently work with larger datasets.

#### large datasets in Observable 
- Getting Data into a Notebook [video](https://youtu.be/DqrQ20Hc3mk), [notebook](https://observablehq.com/@anjana/getting-data-into-a-notebook?collection=%40anjana%2Flearning-observable-video-companions)
- Connecting to databases *(for private notebooks only)* [notebook](https://observablehq.com/@observablehq/databases)
- "Vega-Lite with Streaming Data Updates" [notebook](https://observablehq.com/@vega/vega-lite-with-streaming-data-updates)
  - [another notebook](https://observablehq.com/@davidbudac/streaming-data-into-vega)


### Misc. considerations a good storage system
Should you be saving data from Python with [pickle](https://docs.python.org/3/library/pickle.html), [numpy binary](https://numpy.org/doc/stable/reference/generated/numpy.savez.html), [writing to csv](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_csv.html), or something else?

- plain-text “human-readable” formats v.s. binary
  - "human-readable" formats like `.csv` give the ability to visually inspect the integrity of the data which is very useful
  - This choice also impact git’s ability to diff files. 
    - Binary files seem very slow to add to git because of this. 
    - The standard solution to this seems to be simple to avoid version-controlling binary data.
  - but generally binary data storage is going to be faster and more memory-efficient
- ability to load partial chunks of data
  - one of the primary selling points of hdf5
  - also seems to be one of the use cases for `ColumnDataSource` as in Bokeh, although I haven't tried this yet
- ease of integration with summary visualization tools
  - integration with data-analysis tools like numpy / pandas
- tight connection between metadata / summary data and primary timeseries

#### Additional considerations 
features which are good to have, but don’t strongly impact my user experience at the moment
- memory efficiency of storage
  - - "best way to preserve numpy arrays on disk" - [stack overflow discussion with benchmarks](https://stackoverflow.com/questions/9619199/best-way-to-preserve-numpy-arrays-on-disk)
- speed of saving/loading to/from files
  - see also [fastest save and load options for a numpy array](https://stackoverflow.com/questions/30329726/fastest-save-and-load-options-for-a-numpy-array)
- portability across platforms (i.e. python versions)
  
#### [:fa-table: go back to comparison table :fa-table:](#visualization-library-options)
------------  
  
### Embedding results
Being able to share results and code with others, especially without them having to install a complex ecosystem of tools is useful, and good for open, reproducible science.
- **Observable**
  - Observable: Sharing, Publishing & Embedding [video](https://youtu.be/PAslRgpiTmw)
  - [Introduction to Embedding](https://observablehq.com/@observablehq/introduction-to-embedding) by Toph Tucker
  - [Advanced Embedding and Downloading](https://observablehq.com/@observablehq/downloading-and-embedding-notebooks) by Jeremy Ashkenas
  
- **Plotly**
  - [interactive HTML export](https://plotly.com/python/interactive-html-export/)
    - see also `get_embed()`[docs](https://plotly.com/python/embedding-plotly-graphs-in-HTML/) , [an example](https://kitchingroup.cheme.cmu.edu/blog/2016/02/06/Interactive-plots-in-HTML-with-Plotly/)
  - [Dash](https://dash.plotly.com/introduction) provides a way to build and embed more complex "dashboards" built from plotly components
    - can be [deployed with Heroku](https://dash.plotly.com/deployment)


- **Altair**
  - [convert to JSON](https://altair-viz.github.io/user_guide/saving_charts.html#json-format), then embed with [vegaEmbed](https://github.com/vega/vega-embed)
  - to save altair to html: https://github.com/altair-viz/altair/issues/1511
    ```
    shell_html = io.StringIO()  
    chart.save(shell_html,'html')  
    return shell_html  
    ```
  
- **Bokeh**
  - Embedding Bokeh content [docs](https://docs.bokeh.org/en/latest/docs/user_guide/embed.html) 
    - can use `file_html()` or `json_item()`

#### [:fa-table: go back to comparison table :fa-table:](#visualization-library-options)
--------------------------------------------
## Interactivity 
### Shortlist - my favorite examples
- Seattle weather interactive - [demo](https://altair-viz.github.io/gallery/seattle_weather_interactive.html) ⭐
- Using facets to identify patterns - Correlation over Time - [observable](https://observablehq.com/@observablehq/correlation-over-time?collection=@observablehq/analyzing-time-series-data)

### Examples of useful interactivity

- **Filtering (aka brushing)** - dynamic queries
  - Altair 
    - Seattle weather interactive - [demo](https://altair-viz.github.io/gallery/seattle_weather_interactive.html) ⭐
    - linked range selection - [demo](https://altair-viz.github.io/user_guide/interactions.html#conditions-making-the-chart-respond)
  - very useful in parallel coordinates 
    - Plotly - [parallel coordinates with brushing](https://plotly.com/python/parallel-coordinates-plot/)
  - Observable 
    - [intro to brushing](https://observablehq.com/@uwdata/interaction?collection=@uwdata/visualization-curriculum), as well as [several rich examples](https://observablehq.com/@uwdata/interaction?collection=@uwdata/visualization-curriculum#cell-427)

- **Linking / cross-filtering** - connecting behavior across subplots
  - **faceting / small multiples** - prerequisite for cross-filtering
     - Altair 
        - scatter plot with marginal distributions - [histogram demo](https://altair-viz.github.io/gallery/scatter_marginal_hist.html), [dot-dash plot demo](https://altair-viz.github.io/gallery/dot_dash_plot.html)
     - Plotly - [several facet plot examples](https://plotly.com/python/facet-plots/)
     - Observable
      - Multi-View Composition [tutorial](https://observablehq.com/@uwdata/multi-view-composition) by UW Data Lab
 
  - Linked filtering - [Plotly R demo](https://plotly-r.com/client-side-linking.html)
  - Altair 
    - subselecting distribution by category - [demo](https://altair-viz.github.io/gallery/interactive_cross_highlight.html)
    - visualizing the correlation structure of weather data - [linked histogram example](https://towardsdatascience.com/altair-plot-deconstruction-visualizing-the-correlation-structure-of-weather-data-38fb5668c5b1)
    - subselecting timeseries - [demo](https://altair-viz.github.io/gallery/select_detail.html)
  - Bokeh 
    - [linking - docs](https://docs.bokeh.org/en/latest/docs/user_guide/interaction/linking.html)
    - Bokeh via HoloViz - [crossfiltering demo](http://holoviews.org/gallery/apps/bokeh/crossfilter.html)
  
  
- **Highlights / tooltips** - responsive annotation ties different representations together
  - Altair: [multi-line highlight](https://altair-viz.github.io/gallery/multiline_highlight.html)
  - Altair: [simple scatter plot with tooltips](https://altair-viz.github.io/gallery/scatter_tooltips.html?highlight=tooltip) (limited customization)
  - Bokeh hover tool [docs](https://docs.bokeh.org/en/latest/docs/user_guide/tools.html#userguide-tools-hover-tool)
  - Plotly: 
    - [hover text and formatting in python](https://plotly.com/python/hover-text-and-formatting/)
    - [car exploration with hover events in Python/v3](https://plotly.com/python/v3/cars-exploration/)
      - uses Figure Widgets 




- **Plotly / Dash**
  -  [Interactive Visualizations - docs](https://dash.plotly.com/interactive-graphing)

- **Bokeh**
  - Color slider example [demo](https://docs.bokeh.org/en/latest/docs/gallery/color_sliders.html)
    - includes custom JavaScript callbacks
  - [Making Interactions - docs](https://docs.bokeh.org/en/latest/docs/user_guide/interaction.html)
  - [Linking - docs](https://docs.bokeh.org/en/latest/docs/user_guide/interaction/linking.html)


- **Altair**
  - [Interactive plot gallery](https://larremorelab.github.io/)
  - [Bindings, Selections, Conditions: Making Charts Interactive](https://altair-viz.github.io/user_guide/interactions.html)
    > One of the unique features of Altair, inherited from Vega-Lite, is a declarative grammar of not just visualization, but interaction. 
    

### Custom callbacks:
In order to implement rich interactivity beyond preconstructed templates, it is useful to have control over the `callbacks` or functions which execute after another event.

- **Dash** has it's own pseudo-javascript interface to callbacks:
  - [basic callbacks](https://dash.plotly.com/basic-callbacks)
  - [advanced callbacks](https://dash.plotly.com/advanced-callbacks)
- **Bokeh** has very straightforward integration with custom JS callbacks! 
  - js callbacks [docs](https://docs.bokeh.org/en/latest/docs/user_guide/interaction/callbacks.html#userguide-interaction-jscallbacks)

- **Altair / vega-lite**
  > Altair does not offer any way to register event handlers, beyond what's available in the Vega-Lite spec. That would have to be done in Javascript via the Vega view API

  - export to json, write callbacks as js: [github](https://github.com/altair-viz/altair/issues/1816)
  - [run js on click](https://stackoverflow.com/questions/57879846/in-altair-vega-is-there-a-way-to-run-arbitrary-javascript-code-on-click) discussion
  
- **ObservableHQ**:
  - Since the entire notebook is reactive, and built on javascript, callbacks should be straightforward
  - but specifically from vega-lite:
    - custom callback example in observable [vega-lite, custom events](https://observablehq.com/@maliky/listening-to-vega-lite-charts-events-with-a-generator)
    - more info: [Observing Vega Signals](https://observablehq.com/@mbostock/observing-vega-signals)

- **HoloViz**
  - [Linking using custom JS code](https://panel.holoviz.org/user_guide/Links.html#linking-using-custom-js-code)
    > Linking objects in Python is often very convenient, because it allows writing code entirely in Python. However, it also requires a live Python kernel. If instead we want a static example (e.g. on a simple website or in an email) to have custom interactivity, or we simply want to avoid the overhead of having to call back into Python, we can define links in JavaScript.


*for Altair-specific implementation notes see [building blocks of interactivity](#building-blocks-of-interactivity)*

#### [:fa-table: go back to comparison table :fa-table:](#visualization-library-options)
--------------------------------------------

### Useful plotting techniques
*(see the [New Python Data Visualization Tools repo :fa-github:](https://github.com/skirmer/new-py-dataviz) by Stephanie Kirmer to compare plot-type implementations across Altair, Plotly, Bokeh)*
🚧 to-do: embed examples for each of these 🚧
1. think about **explanatory versus exploratory** data-viz
    - exploratory vs explanatory analysis [blogpost](https://www.storytellingwithdata.com/blog/2014/04/exploratory-vs-explanatory-analysis) by Cole Nussbaumer Knaflic
    - see also "Analyzing Time Series Data" [video via Observable](https://youtu.be/bTQfm6gwngY) for a practical discussion of balancing these goals 
    - As scientists we often need to start with exploration, but transition to explanation by time of publication. Interactive, executable articles allow for doing both well
       - see "[Neuronal timescales are functionally dynamic and shaped by cortical microarchitecture](https://elifesciences.org/articles/61277/executable)" by Richard Gao et al. as an example
     - see [resources by Daisy Chung](#sources-inspiration-more-resources) for excellent *explanatory* data-viz
   
2. **faceting / small multiples:**
    -  scatter-plot matrix  (aka SPLOM) - 💡*this is always my starting point for visualizing complex data*
        - [observable article](https://observablehq.com/@d3/brushable-scatterplot-matrix) by Mike Bostock
          <details><summary>🌟 interactive demo 🌟 </summary>
          
          <iframe width="100%" height="600" frameborder="0"
          src="https://observablehq.com/embed/@d3/brushable-scatterplot-matrix?cells=viewof+selection"></iframe>
          </details>
          
        <details>
          <summary>implementations</summary>
          
            - [comparisons :fa-github:](https://github.com/skirmer/new-py-dataviz/blob/main/facets.ipynb) by Stephanie Kirmer 
            - [seaborn](https://seaborn.pydata.org/tutorial/axis_grids.html)
               - I think the added value of marginal distributions visualized with [kernel-density estimates](https://seaborn.pydata.org/examples/joint_kde.html) is great.
               - Seaborn's [PairGrid implementation](https://seaborn.pydata.org/tutorial/axis_grids.html) is the best one I've seen for this in Python
               - although [R's `pairs.panels`](http://www.sthda.com/english/wiki/scatter-plot-matrices-r-base-graphs) seems to do something similar
               
            - [altair implementation](https://altair-viz.github.io/gallery/scatter_matrix.html) with linked behavior between panels
            - [plotly](https://plotly.com/python/splom/) [w/ customization using figure factory](https://plotly.com/python/v3/legacy/scatterplot-matrix/)
        </details>
      
    - case study: correlation over time , [article](https://observablehq.com/@observablehq/correlation-over-time) by Mike Freeman
      <details><summary>🌟 live, embedded demo 🌟</summary>
      <iframe width="100%" height="600" frameborder="0"
        src="https://observablehq.com/embed/@observablehq/correlation-over-time?cells=facet_wrap"></iframe>
        </details>
      
3. add **tooltips on hover with useful detail**
    - plotly implementation [docs](https://plotly.com/python/hover-text-and-formatting/)
    - customizing tooltips in altair [github](https://github.com/altair-viz/altair/issues/2058)

4. use **interactive heatmaps** 
    - can nest / hierarchically organize a lot of dimensions 
    - Clustergrammer [demo](https://maayanlab.github.io/clustergrammer/scrolling_tour), [talk video](https://youtu.be/82epZkmfkrE)
      - highly interactive heatmap for clustering genes associated with phenotypes
    - [Plotly examples](https://www.r-graph-gallery.com/215-interactive-heatmap-with-plotly.html), [docs](https://plotly.com/python/heatmaps/#heatmap-with-datetime-axis)

5. **parallel coordinates** - ⚠️ primarily for *exploratory* data-viz ⚠️
    - <details><summary>🌟 live observable demo by @sophigri 🌟</summary>
    
      <iframe width="100%" height="584" frameborder="0"
      src="https://observablehq.com/embed/@sophiegri/exercise-3-parallel-coordinates?cells=paracoords"></iframe>
    </details>
    
    - [5 minute intro](https://youtu.be/X8rNDvPNg30?t=1415) by Amit Kapoor
    - [longer showcase of parallel coordinates](https://youtu.be/ypc7Ul9LkxA) by Kai Chang
      - associated project page [syntagmatic - parllel coordinates - d3.js implementation](https://syntagmatic.github.io/parallel-coordinates/)

    - <details><summary>practical, implementation tips:</summary>
      
      - [plotly implementation](https://plotly.com/python/parallel-coordinates-plot/)
        - while there are parallel coordinates implementations in many python plotting packages, this is the only one I've found with the very useful feature of filtering each dimension into ranges as well as being able to reorder axes
      - order of dimensions matters a lot! use a tool where you can rearrange order
      - scaling / normalization matters a lot! 
      - coloring by a key attribute can help dissect structure
      - interactivity is crucial to sort through the "hairball"
        - high bandwidth, but hard to parse
      - can be used to pick out clusters in high-dimensional parameter space
      </details>
    
  
6. replacing legends with direct **text-annotation**
    - "Six Decades of Carbon Dioxide Concentration in the Atmosphere" [example](https://www.datawrapper.de/_/OHgEm/)
        - [Altair implementation](https://altair-viz.github.io/gallery/co2_concentration.html?highlight=mark_text)
    - Text annotation with [Observable plot](https://observablehq.com/@observablehq/plot-text)


7. **"banking to 45 degrees"** i.e. choosing aspect ratios for plots that maximize discriminability
    - original paper: Cleveland, W. S., M. E. McGill, and R. McGill. [The Shape Parameter of a Two-Variable Graph.](https://www.jstor.org/stable/2288843) Journal of the American Statistical Association, 83:289-300, 1988
    - Eager Eyes blog [examples](https://eagereyes.org/basics/banking-45-degrees)
    - my old, messy screenshot examples
      <details><summary>example here</summary>image</details>

8. **meaningful color-scales**
    - example: red-yellow-green for "bad to good" 
      - [example](https://twitter.com/vlandham/status/1445406386968571904) from @vlandham
    - provides an empathetic shortcut to visual digestion
    - Consider "perceptually uniform" colormaps
      - [A colour scheme for the display of astronomical intensity images](https://arxiv.org/pdf/1108.5083.pdf) - Dave Green
        - see also [Cubehelix, or How I Learned to Love Black & White Printers](https://ifweassume.blogspot.com/2013/05/cubehelix-or-how-i-learned-to-love.html) - JamesDavenport [repo](https://github.com/jradavenport/cubehelix)
      - [ColorBrewer: Color Advice for Maps](https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3)
        - [matplotlib colorbrewer scales](https://matplotlib.org/stable/api/pyplot_summary.html)
      - [Color Map Advice for Scientific Visualization](https://www.kennethmoreland.com/color-advice/) - Kenneth Moreland      
      - [Turbo, An Improved Rainbow Colormap for Visualization](https://ai.googleblog.com/2019/08/turbo-improved-rainbow-colormap-for.html)
      - [How Bad Is Your Colormap?](https://jakevdp.github.io/blog/2014/10/16/how-bad-is-your-colormap/)
      - [Good Colour Maps: How to Design Them](https://arxiv.org/pdf/1509.03700.pdf) -  Peter Kovesi
  
---------
### Sources, inspiration, more resources
  - many of these ideas I've fumbled my way to by trying to plot my own work 
  - but many of these ideas I picked up from the work of [Edward Tufte](https://www.edwardtufte.com/tufte/)
  
  - I've found learning about **"the Grammar of Graphics"** quite compelling
    - both as a philsophical standpoint for how to think about data-visualization
    - and as an attractive framework for prototyping and implementing visualizations
    - this idea underlies several plotting frameworks including ggplot, vega (vega-lite, altair)
      - but may help put together creative solutions for libraries which aren't explicitly built around the grammar of graphics
    - [The Grammar of Graphics](https://books.google.com/books/about/The_Grammar_of_Graphics.html?id=ZiwLCAAAQBAJ&source=kp_book_description) - Leland Wilkinson
    - [A Layered Grammar of Graphics](https://byrneslab.net/classes/biol607/readings/wickham_layered-grammar.pdf) - Hadley Wickham
    - [A Comprehensive Guide to the Grammar of Graphics](https://towardsdatascience.com/a-comprehensive-guide-to-the-grammar-of-graphics-for-effective-visualization-of-multi-dimensional-1f92b4ed4149) for the effective visualization of multi-dimensional data - by Dipanjan Sarkar
      - [slides](https://github.com/dipanjanS/art_of_data_visualization/blob/master/PyCon18/PyCon%202018%20-%20Dipanjan.pdf), [repo](https://github.com/dipanjanS/art_of_data_visualization)
    
  - **Broad data-viz advice**
    - [Daisy Chung](https://daisychung.com/) has a great list of resources for [visual storytelling with data](https://docs.google.com/document/d/1dneLTgp75WnVmU4VkaOArnfJakn1n2_X4Xzpey-VO-w/edit)
    - [[data visualization workshop slides]](https://drive.google.com/file/d/1LouVvISCRlWkItZgzoHcgoU5Q1VyHT4U/view) great practical advice from [Sam Way](http://samfway.com/) & [Dan Larremore](https://larremorelab.github.io/): 
    - "Analyzing Time Series Data" [video](https://www.youtube.com/watch?v=bTQfm6gwngY&ab_channel=Observable), by Zan Armstrong, Ian Johnson, and Mike Freeman 
      - also does a great job of walking through several practical case-studies
      - [video via Observable](https://youtu.be/bTQfm6gwngY)
      - [code workshop materials](https://observablehq.com/@observablehq/timeseries-workshop-materials)
    - I enjoyed Amit Kapoor's taxonomy of different strategies for plotting high-dimensional data [video](https://youtu.be/X8rNDvPNg30)
      - discusses the tradeoffs between simplicity and need for interactivity
    
  - **Other great overviews of python, science, data-viz**
    - [Dynamic scientific visualizations in the browser for Python users](https://xcorr.net/2021/06/02/dynamic-scientific-visualizations-in-the-browser-for-python-users/) by Patrick Mineault 
      - covers much more of the pipeline from scientific computing to web-based notebooks
    - [Nicolas Rougier](https://twitter.com/NPRougier) is currently working on [Scientific Visualization – Python & Matplotlib](https://github.com/rougier/scientific-visualization-book)
      - see also [Python & OpenGL for Scientific Visualization](https://www.labri.fr/perso/nrougier/python-opengl/) 
    - Going Beyond Matplotlib and Seaborn (2021) [video](https://youtu.be/ms1v9TqpBP0), and [repo :fa-github:](https://github.com/skirmer/new-py-dataviz) by Stephanie Kirmer
      - has implementations for several common plot types across Bokeh, plotnine, Altair and Plotly

  - **Learning JavaScript-based visualization tools**
    - UW Data has an excellent series of [tutorial notebooks](https://observablehq.com/collection/@uwdata/visualization-curriculum)
      - the [Interaction notebook](https://observablehq.com/@uwdata/interaction?collection=@uwdata/visualization-curriculum) and [Introduction to Vega-Lite](https://observablehq.com/@uwdata/introduction-to-vega-lite?collection=@uwdata/visualization-curriculum) are especially good
      - see also [Observable: Vega-Lite: A Crash Course - video](https://youtu.be/ZV_Yjcs5WtM) by Visnu Pitiyanuvath
    - [Introduction to D3 - video](https://youtu.be/8jvoTV54nXw) by Curran Kelleher
      - pracitcal step-by-step guide to building up useful plots in D3.js from scratch
    - React + D3 [video by Shirley Wu](https://youtu.be/zXBdNDnqV2Q)
      - excellent breakdown of how D3 can interface with other javascript libraries to get the best use out of each 
        - use D3 for remapping and drawing axes 
        - use something like React / Vue to handle rendering to DOM, user input
  
  - 🚧 monotonic splines 🚧

--------------------------------------------    
# Appendix
--------------------------------------------
### Further musings
*The following are my personal opinions and not necessarily general recommendations:*

- successful [faceting](#useful-plotting-techniques) might be even more useful than interactivity 
  - with interactivity like sweeping through parameter space, we need something like a faded version of previous parameters to show context for where we explored from  
  - this tradeoff changes based on the density, continuity and monotonicity of 
  
- fully flattened [tidy csv](#tidying-data) for everything means loading far too much information (especially in Altair/Vega-lite)
  - but the [tidy data paper](https://vita.had.co.nz/papers/tidy-data.pdf) helped me understand a lot of the philosophy of both Pandas DataFrames and vega-lite / altair
  - need some virtual-link / lazy-loading solution to continue this paradigm to large datasets
  
- straying too far outside python limits iteration 
  - there's value to communicating to scientists the python you're using to compute things 
  - oftentimes the `numpy` syntax is much nicer than the equivalent javascript for matrix stuff 
  - this is why jupyter is *the* current horse to bet on 
  - this is also a factor making me hesitant to jump to Observable, despite all its nice features 
  - this also means I'm very interested in watching the [Pyodide project](https://pyodide.org/en/stable/)

- aesthetically I don't like jupyter notebooks 
  - I want plain-text notebooks
    - no excessive meta-data in file
    - easy to inspect, version-control the important stuff 
    - easy to convert between script and notebook 
  - I want to be able to use my multi-purpose editor (VSCode / Atom) for research as well
    - see [Hydrogen for Atom](https://nteract.io/atom) or [docs on Jupyter in VSCode](https://code.visualstudio.com/docs/datascience/jupyter-notebooks)
    - see also [JupyText](https://github.com/mwouts/jupytext) for plain-text markdown-like notebooks
  - I want embedding not to require binder. (ideally "just go to a link!") 
  - they also look very clunky (compared to [observablehq](https://observablehq.com/explore) for instance)
  
- being able to host via github pages is a big advantage 
  - but is this always doable via services like heroku / netlify ? 
    - even if the library itself doesn't natively support it?
    - ( beyond my current expertise )
    
--------------------------------------------

### Tidying data 
Structuring data for visualization tools 
- **short version:** keep one instance to one row 
- [Tidy Data](https://vita.had.co.nz/papers/tidy-data.pdf) by Wickham, [python version](https://www.jeannicholashould.com/tidy-data-in-python.html)
  - well worth reading 
  - practical examples 
  - ellucidated the philosophy behind R, grammar of graphics, pandas
  - discusses value of alternative "wide-format" representations also
  - idea has ties to dimensional stacking for viz 
  
- data wrangling in [observable](https://observablehq.com/@observablehq/data-wrangler)

## Footnotes: