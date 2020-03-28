---
title: 'Självstudiekurs: Skapa en anteckningsbok i Azure Cosmos DB för att analysera och visualisera data'
description: 'Självstudiekurs: Lär dig hur du använder inbyggda Jupyter-anteckningsböcker för att importera data till Azure Cosmos DB, analysera data och visualisera utdata.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73720924"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Självstudiekurs: Skapa en anteckningsbok i Azure Cosmos DB för att analysera och visualisera data

I den här artikeln beskrivs hur du använder inbyggda Jupyter-anteckningsböcker för att importera exempelbeädlardata till Azure Cosmos DB. Du kommer att se hur du använder SQL och Azure Cosmos DB magiska kommandon för att köra frågor, analysera data och visualisera resultaten.

## <a name="prerequisites"></a>Krav

* [Aktivera stöd för anteckningsböcker när du skapar Azure Cosmos-kontot](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Skapa resurser och importera data
 
I det här avsnittet skapar du Azure Cosmos-databasen, behållaren och importerar återförsäljardata till behållaren.

1. Navigera till ditt Azure Cosmos-konto och öppna **Data Explorer.**

1. Gå till fliken **Anteckningsböcker,** välj `…` bredvid **Mina anteckningsböcker** och skapa en **ny anteckningsbok**. Välj **Python 3** som standardkärna.

   ![Skapa en ny notebook](./media/create-notebook-visualize-data/create-new-notebook.png)

1. När en ny anteckningsbok har skapats kan du byta namn på den till något som **VisualizeRetailData.ipynb.**

1. Därefter kommer du att skapa en databas med namnet "RetailDemo" och en behållare med namnet "WebsiteData" för att lagra butiksdata. Du kan använda /CardID som partitionsnyckel. Kopiera och klistra in följande kod i en ny cell i anteckningsboken och kör den:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Om du vill `Shift + Enter` köra en cell markerar du Eller markerar cellen och väljer **Alternativet Kör aktiv cell** i navigeringsfältet för datauterare.

   ![Köra den aktiva cellen](./media/create-notebook-visualize-data/run-active-cell.png)

   Databasen och behållaren skapas i ditt aktuella Azure Cosmos-konto. Behållaren är etablerad med 400 RU/s. Följande utdata visas när databasen och behållaren har skapats. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Du kan också uppdatera fliken **Data** och se de nyligen skapade resurserna:

   ![Uppdatera datafliken för att se den nya behållaren](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Därefter importerar du exempelbevarardata till Azure Cosmos-behållaren. Här är formatet för ett objekt från butiksdata:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   För självstudien lagras exempelbevarar data i Azure blob-lagring. Du kan importera den till Azure Cosmos-behållaren genom att klistra in följande kod i en ny cell. Du kan bekräfta att data har importerats genom att köra en fråga för att välja antalet objekt.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   När du kör föregående fråga returneras följande utdata:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Hämta dina data till en DataFrame

Innan du kör frågor för att analysera data kan du läsa data från behållaren till en [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) för analys. Använd följande sql magic-kommando för att läsa data i en DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Mer information finns i de [inbyggda kommandona och funktionerna i den bärbara datorn i Azure Cosmos DB-artikeln.](use-notebook-features-and-commands.md) Du kommer att `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`köra frågan- . Resultaten sparas i en Pandas DataFrame som heter df_cosmos. Klistra in följande kommando i en ny anteckningsbokscell och kör den:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

I en ny anteckningsbokscell kör du följande kod för att läsa de första 10 objekten från utdata:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Kör fråga för att få topp 10 objekt](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Köra frågor och analysera dina data

I det här avsnittet kör du vissa frågor om data som hämtas.

* **Fråga1:** Kör en grupp efter fråga på DataFrame för att få summan av de totala försäljningsintäkterna för varje land och visa 5 artiklar från resultaten. I en ny anteckningsbokscell kör du följande kod:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Total försäljningsintäkter](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Fråga2:** Om du vill hämta en lista över de fem inköpta objekten öppnar du en ny anteckningsbokscell och kör följande kod:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Topp fem inköpta artiklar](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Visualisera dina data  

1. Nu när vi har våra data om intäkter från Azure Cosmos-behållaren kan du visualisera dina data med ett visualiseringsbibliotek som du väljer. I den här guiden kommer vi att använda Bokeh bibliotek. Öppna en ny anteckningsbokscell och kör följande kod för att installera Bokeh-biblioteket. När alla krav är uppfyllda installeras biblioteket.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Nästa förbereda att rita data på en karta. Gå med i data i Azure Cosmos DB med landsinformation i Azure Blob-lagring och konvertera resultatet till GeoJSON-format. Kopiera följande kod till en ny anteckningsbokscell och kör den.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualisera försäljningsintäkterna för olika länder på en världskarta genom att köra följande kod i en ny anteckningsbokscell:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   Utdata visar världskartan med olika färger. Färgerna mörkare till ljusare representerar de länder som har högst intäkter till lägsta intäkter.

   ![Länder intäktskarta visualisering](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Låt oss se ett annat fall av datavisualisering. Behållaren WebsiteData har register över användare som har tittat på ett objekt, lagt till i kundvagnen och köpt varan. Nu ska vi rita omräkningskursen för köpta artiklar. Kör följande kod i en ny cell för att visualisera konverteringsfrekvensen för varje objekt:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![Visualisera konverteringsfrekvens för inköp](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om kommandon för anteckningsböcker finns [i hur du använder inbyggda kommandon och funktioner för bärbara datorer i Azure Cosmos DB-artikeln.](use-notebook-features-and-commands.md)
