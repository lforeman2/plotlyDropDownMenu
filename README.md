# plotlyDropDownMenu
Use python plotly libraries to create custom visualizations using drop down menus to filter data and customize the visual.
The jupyter notebook example is for a grouped histogram visualization, but the code is broadly generalizable to different type of plotly express plot types.

First we load a dummy dataset from plotly:  
```plotDf = px.data.tips()```

Then we chose the column that the histogram will be calculated for:  
```metric='total_bill'``` 

Then we set the columns to group by:  
```plotColumns=['day', 'sex', 'smoker']```

Then we create a list of the number of traces (one for each unique column value) for each grouped column:  
```
numTraces=[]
for col in plotColumns:
    plotDf[col]=plotDf[col].astype(str)
    numTraces.append(plotDf[col].nunique())
```
Initialize loop variable and lists:   
```
m=0
traces = []
buttons = []
```
For each value (k) of each group variable (col), append a histogram trace grouped by the appropriate column to the "traces" list. Create the "visible" list that tells plotly which trace is active for a button selection.
```
for i, col in enumerate(plotColumns):
    visible = [False] * sum(numTraces)
    name=col
    j=numTraces[i]
    for k in range(0,j):
        traces.append(
            list(px.histogram(plotDf, x=metric, color=col).update_traces(opacity=0.75, visible=True if i==0 else False).select_traces())[k]
        )
        visible[m] = True
        m+=1
```
Define the update menus for each group variable (col):
```
    buttons.append(dict(label=name,
                        method="update",
                        args=[{"visible":visible},
                              {"title":f"{name}"}]))
```

Define the figure graph object:  
```
updatemenus = [{'active':0, "buttons":buttons}]  
fig = go.Figure(data=traces,
                 layout=dict(updatemenus=updatemenus))
```

Update the titles and style:  
```
fig.update_layout(title='Title', barmode='overlay')
fig.update_xaxes(title=str(metric))
```

Save the interactive visual offline:  
```fig.write_html("demowidget.html")```
