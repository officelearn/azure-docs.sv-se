---
title: 'Snabbstart: Skapa ett sökindex i Java med REST-API:er'
titleSuffix: Azure Cognitive Search
description: I den här Snabbstarten för Java får du lära dig hur du skapar ett index, läser in data och kör frågor med azure Cognitive Search REST-API:er.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: 0b07e934625b09e7f6249dc00865465147f6f0ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77624025"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java-using-rest-apis"></a>Snabbstart: Skapa ett Azure Cognitive Search-index i Java med REST-API:er
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Skapa ett Java-konsolprogram som skapar, läser in och frågar efter ett sökindex med [IntelliJ,](https://www.jetbrains.com/idea/) [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)och [AZURE Cognitive Search REST API](/rest/api/searchservice/). Den här artikeln innehåller steg-för-steg-instruktioner för att skapa programmet. Alternativt kan du [ladda ner och köra hela programmet](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Vi använde följande programvara och tjänster för att bygga och testa denna snabbstart:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Hämta en nyckel och webbadress

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomstnyckel på varje begäran. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

   Skapa också en frågenyckel. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

![Hämta tjänstens namn och administratörs- och frågenycklar](media/search-get-started-nodejs/service-name-and-keys.png)

Varje begäran som skickas till din tjänst kräver en api-nyckel. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja med att öppna IntelliJ IDEA och starta ett nytt projekt.

### <a name="create-the-project"></a>Skapa projektet

1. Öppna IntelliJ IDEA och välj **Skapa nytt projekt**.
1. Välj **Maven**.
1. Välj Java 11 SDK i listan **Projekt SDK.**

    ![Skapa ett maven-projekt](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. För **GroupId** och **ArtifactId**anger du `AzureSearchQuickstart`.
1. Acceptera de återstående standardinställningarna för att öppna projektet.

### <a name="specify-maven-dependencies"></a>Ange Maven-beroenden

1. Välj **Filinställningar** > **Settings**.
1. I fönstret **Inställningar** väljer du **Bygg, Utförande, Distribution** > **Byggverktyg** > **Maven** > **Importera**.
1. Markera kryssrutan **Importera Maven-projekt automatiskt** och klicka på **OK** för att stänga fönstret. Maven plugins och andra beroenden synkroniseras nu automatiskt när du uppdaterar pom.xml-filen i nästa steg.

    ![Maven importera alternativ i IntelliJ-inställningar](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Öppna filen pom.xml och ersätt innehållet med följande Konfigurationsinformation för Maven. Dessa inkluderar referenser till [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) och en [JSON gränssnitt API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>Ställ in projektstrukturen

1. Välj > **Filprojektstruktur**. **File**
1. Välj **Moduler**och expandera källträdet för att `src`  >   `main` komma åt innehållet i mappen.
1. Lägg `src`  >   `main`  >  `java` till `app` och `service` mappar i mappen. Det gör du `java` genom att markera mappen, trycka på Alt + Infoga och sedan ange mappnamnet.
1. Lägg `src`  >   `main`  > `resources` till `app` och `service` mappar i mappen.

    När du är klar ska projektträdet se ut så här.

    ![Projektkatalogstruktur](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Stäng fönstret genom att klicka på **OK**.

### <a name="add-azure-cognitive-search-service-information"></a>Lägga till Azure Cognitive Search-tjänstinformation

1. Expandera källträdet i **projektfönstret** för `src`  >   `main`  > `resources`  >  `app` att komma `config.properties` åt mappen och lägga till en fil. Det gör du `app` genom att markera mappen, tryck på Alt + Infoga, välja **Arkiv**och ange sedan filnamnet.

1. Kopiera följande inställningar till den `<YOUR-SEARCH-SERVICE-NAME>`nya `<YOUR-ADMIN-KEY>`filen `<YOUR-QUERY-KEY>` och ersätt , och med tjänstnamnet och nycklarna. Om tjänstslutpunkten `https://mydemo.search.windows.net`är, skulle tjänstnamnet vara "mydemo".

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>Lägg till huvudmetoden

1. Lägg `src`  >   `main`  >  `java`  >  `app` till en `App` klass i mappen. Det gör du `app` genom att markera mappen, tryck på Alt + Infoga, välja **Java-klass**och ange sedan klassnamnet.
1. Öppna `App` klassen och ersätt innehållet med följande kod. Den här koden `main` innehåller metoden. 

    Den okommenterade koden läser söktjänstparametrarna och använder dem för att skapa en instans av söktjänstklienten. Klientkoden för söktjänsten läggs till i nästa avsnitt.

    Den kommenterade koden i den här klassen kommer att avkommenteras i ett senare avsnitt i den här snabbstarten.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>Lägga till HTTP-åtgärder

1. Lägg `src`  >   `main`  >  `java`  >  `service` till en`SearchServiceClient` klass i mappen. Det gör du `service` genom att markera mappen, tryck på Alt + Infoga, välja **Java-klass**och ange sedan klassnamnet.
1. Öppna `SearchServiceClient` klassen och ersätt innehållet med följande kod. Den här koden innehåller de HTTP-åtgärder som krävs för att använda AZURE Cognitive Search REST API. Ytterligare metoder för att skapa ett index, ladda upp dokument och fråga om indexet läggs till i ett senare avsnitt.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Bygga projektet

1. Kontrollera att projektet har följande struktur.

    ![Projektkatalogstruktur](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Öppna **maven** verktygsfönstret och kör detta `verify exec:java` 
 ![maven mål: kör maven mål: kontrollera exec:java](media/search-get-started-java/java-quickstart-execute-maven-goal.png)

När bearbetningen är klar letar du efter ett BUILD SUCCESS-meddelande följt av en nollkod (0).

## <a name="1---create-index"></a>1 - Skapa index

Hotellindexdefinitionen innehåller enkla fält och ett komplext fält. Exempel på ett enkelt fält är "HotelName" eller "Beskrivning". Fältet "Adress" är ett komplext fält eftersom det har underfält, till exempel "Gatuadress" och "Ort". I den här snabbstarten anges indexdefinitionen med JSON.

1. Expandera källträdet i **projektfönstret** för `src`  >   `main`  > `resources`  >  `service` att komma `index.json` åt mappen och lägga till en fil. Det gör du `app` genom att markera mappen, tryck på Alt + Infoga, välja **Arkiv**och ange sedan filnamnet.

1. Öppna `index.json` filen och infoga följande indexdefinition.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Indexnamnet kommer att vara "hotels-quickstart". Attributen i indexfälten avgör hur indexerade data kan sökas i ett program. `IsSearchable` Attributet måste till exempel tilldelas varje fält som ska inkluderas i en fulltextsökning. Mer information om attribut finns i [Fältsamling och fältattribut](search-what-is-an-index.md#fields-collection).
    
    Fältet `Description` i det här `analyzer` indexet använder den valfria egenskapen för att åsidosätta standardspråkanalysatorn för Lucene. Fältet `Description_fr` använder den franska Lucene-analysatorn `fr.lucene` eftersom den lagrar fransk text. Den `Description` använder den valfria Microsoft språkanalysator en.lucene. Mer information om analysatorer finns i [Analysatorer för textbearbetning i Azure Cognitive Search](search-analyzers.md).

1. Lägg till följande `SearchServiceClient` kod i klassen. Dessa metoder bygger Azure Cognitive Search REST-tjänst webbadresser som skapar och tar bort ett index och som avgör om det finns ett index. Metoderna gör också HTTP-begäran.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Avkommenta följande kod `App` i klassen. Den här koden tar bort indexet "hotels-quickstart" om det finns, och skapar ett nytt index baserat på indexdefinitionen i filen "index.json". 

    En paus på en sekund infogas efter begäran om att skapa index. Den här pausen säkerställer att indexet skapas innan du överför dokument.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Öppna **verktygsfönstret i Maven** och kör det här maven-målet:`verify exec:java`

    När koden körs letar du efter meddelandet "Skapa index" följt av en 201-svarskod. Den här svarskoden bekräftar att indexet har skapats. Körningen ska avslutas med ett BUILD SUCCESS-meddelande och en noll (0) slutkod.
    
## <a name="2---load-documents"></a>2 - Ladda dokument

1. Expandera källträdet i **projektfönstret** för `src`  >   `main`  > `resources`  >  `service` att komma `hotels.json` åt mappen och lägga till en fil. Det gör du `app` genom att markera mappen, tryck på Alt + Infoga, välja **Arkiv**och ange sedan filnamnet.
1. Infoga följande hotelldokument i filen.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Infoga följande kod `SearchServiceClient` i klassen. Den här koden skapar REST-tjänstens URL för att ladda upp hotelldokumenten till indexet och gör sedan HTTP POST-begäran.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Avkommenta följande kod `App` i klassen. Den här koden överför dokumenten i "hotels.json" till indexet.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    En paus på två sekunder infogas efter begäran om överföring för att säkerställa att dokumentinläsningsprocessen slutförs innan du frågar index.

1. Öppna **verktygsfönstret i Maven** och kör det här maven-målet:`verify exec:java`

    Eftersom du skapade ett "hotels-quickstart"-index i föregående steg tas koden nu bort det och återskapas igen innan du läser in hotelldokumenten.

    När koden körs letar du efter meddelandet "Ladda upp dokument" följt av en 200-svarskod. Den här svarskoden bekräftar att dokumenten har överförts till indexet. Körningen ska avslutas med ett BUILD SUCCESS-meddelande och en noll (0) slutkod.

## <a name="3---search-an-index"></a>3 – Söka i ett index

Nu när du har läst in hotelldokumenten kan du skapa sökfrågor för att komma åt hotelldata.

1. Lägg till följande `SearchServiceClient` kod i klassen. Den här koden skapar AZURE Cognitive Search REST-tjänst webbadresser för att söka efter indexerade data och skriver ut sökresultaten.

    Med `SearchOptions` klassen `createSearchOptions` och metoden kan du ange en delmängd av de tillgängliga azure cognitive search REST API-frågealternativen. Mer information om rest API-frågealternativen finns i [Sökdokument (AZURE Cognitive Search REST API)](/rest/api/searchservice/search-documents).

    Metoden `SearchPlus` skapar sökfråge-URL:en, gör sökbegäran och skriver sedan ut resultaten till konsolen. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. Avmarkera `App` följande kod i klassen. Den här koden ställer in fem olika frågor, inklusive söktext, frågeparametrar och datafält som ska returneras. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Det finns två [sätt att matcha termer i en fråga:](search-query-overview.md#types-of-queries)fulltextsökning och filter. En fulltextsökfråga söker efter ett `IsSearchable` eller flera termer i fälten i indexet. Ett filter är ett booleskt `IsFilterable` uttryck som utvärderas över fält i ett index. Du kan använda fulltextsökning och filter tillsammans eller separat.

1. Öppna **verktygsfönstret i Maven** och kör det här maven-målet:`verify exec:java`

    Leta efter en sammanfattning av varje fråga och dess resultat. Körningen ska slutföras med BUILD SUCCESS-meddelandet och en noll (0) exit-kod.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här Java-snabbstarten har du arbetat igenom en rad uppgifter för att skapa ett index, läsa in det med dokument och köra frågor. Om du är bekväm med de grundläggande begreppen rekommenderar vi följande artikel som listar indexeringsåtgärder i REST.

> [!div class="nextstepaction"]
> [Indexeringsverksamhet](/rest/api/searchservice/indexer-operations)
