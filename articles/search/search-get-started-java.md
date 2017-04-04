---
title: "Komma igång med Azure Search i Java| Microsoft Docs"
description: "Här lär du dig hur du skapar ett värdbaserat sökprogram i molnet med Azure och Java som programmeringsspråk."
services: search
documentationcenter: 
author: EvanBoyle
manager: pablocas
editor: v-lincan
ms.assetid: 8b4df3c9-3ae5-4e3a-b4bb-74b516a91c8e
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 07/14/2016
ms.author: evboyle
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f6ca06a0349def97b38a1bf6d0d8f36236077e92
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-search-in-java"></a>Komma igång med Azure Search i Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [NET](search-howto-dotnet-sdk.md)
> 
> 

Lär dig hur du skapar ett anpassat Java-sökprogram som använder Azure Search som sökmiljö. I den här självstudiekursen används [REST-API:et för tjänsten Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) för att skapa de objekt och åtgärder som används i den här övningen.

Om du vill köra det här exemplet måste du ha en Azure Search-tjänst, som du kan registrera dig för på [Azure Portal](https://portal.azure.com). Stegvisa instruktioner finns i [Skapa en Azure Search-tjänst på portalen](search-create-service-portal.md).

Vi använde följande programvara när vi skapade och testade det här exemplet:

* [Eclipse IDE för Java EE-utvecklare](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Var noga med att ladda ned EE-versionen. Ett av verifieringsstegen kräver en funktion som bara finns i den här versionen.
* [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Om de data som används
Det här exempelprogrammet använder data från [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), som har filtrerats på delstaten Rhode Island för att minska datauppsättningens storlek. Vi ska använda dessa data för att skapa ett sökprogram som returnerar viktiga byggnader som sjukhus och skolor, samt geologiska element som vattendrag, sjöar och bergstoppar.

I det här programmet bygger och läser programmet **SearchServlet.java** in indexet med hjälp av en [indexeringskonstruktion](https://msdn.microsoft.com/library/azure/dn798918.aspx) och hämtar den filtrerade USGS-datauppsättningen från en offentlig Azure SQL-databas. Fördefinierade autentiseringsuppgifter och anslutningsinformation för onlinedatakällan finns i programkoden. Ingen ytterligare konfiguration krävs vad gäller dataåtkomsten.

> [!NOTE]
> Vi har använt ett filter för den här datauppsättningen för att hålla oss under gränsen på 10 000 dokument för den kostnadsfria prisnivån. Om du använder standardnivån så gäller inte den här gränsen och du kan ändra koden om du vill använda en större datauppsättning. Mer information om kapaciteten för varje prisnivå finns i [Gränser och begränsningar](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Om programfilerna
Följande lista beskriver de filer som är relevanta för det här exemplet.

* Search.JSP: Tillhandahåller användargränssnittet
* SearchServlet.java: Tillhandahåller metoder (påminner om en kontrollant i MVC)
* SearchServiceClient.java: Hanterar HTTP-begäranden
* SearchServiceHelper.java: En hjälparklass som tillhandahåller statiska metoder
* Document.Java: Tillhandahåller datamodellen
* Config.Properties: Anger URL:en och API-nyckeln för Search-tjänsten
* Pom.XML: Ett Maven-beroende

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Leta upp tjänstnamnet och API-nyckeln för Azure Search-tjänsten
Alla REST API-anrop till Azure Search kräver att du anger tjänstens URL och en API-nyckel. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I snabbåtkomstfältet klickar du på **Söktjänst** för att visa en lista över Azure Search-tjänsterna som har etablerats för din prenumeration.
3. Markera den tjänst som du vill använda.
4. På instrumentpanelen för tjänsten ser du paneler för viktig information samt nyckelikonen för att komma åt administatörsnycklarna.
   
      ![][3]
5. Kopiera tjänstens URL och en administratörsnyckel. Du behöver dem senare när du lägger till dem i filen **config.properties**.

## <a name="download-the-sample-files"></a>Ladda ned exempelfilerna
1. Gå till [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) på GitHub.
2. Klicka på **Ladda ned ZIP**, spara ZIP-filen på disk och extrahera sedan alla filer som den innehåller. Om du vill kan du extrahera filerna till Java-arbetsytan så att det blir lättare att hitta projektet senare.
3. Exempelfilerna är skrivskyddade. Högerklicka på Mappegenskaper och ta bort skrivskyddet.

Alla efterföljande filändringar och körningsinstruktioner görs mot filer i den här mappen.  

## <a name="import-project"></a>Importera projekt
1. I Eclipse väljer du **File** > **Import** > **General** > **Existing Projects into Workspace**.
   
    ![][4]
2. I **Select root directory** bläddrar du till mappen som innehåller exempelfilerna. Välj mappen som innehåller mappen .project. Projektet bör visas i listan **Projects** som ett markerat objekt.
   
    ![][12]
3. Klicka på **Finish**.
4. Använd **Project Explorer** för att visa och redigera filerna. Om den inte redan är öppen klickar du på **Window** > **Show view** > **Project Explorer** eller använder genvägen för att öppna den.

## <a name="configure-the-service-url-and-api-key"></a>Konfigurera tjänstens URL och API-nyckel
1. I **Project Explorer** dubbelklickar du på **config.properties** för att redigera konfigurationsinställningarna som innehåller servernamnet och API-nyckeln.
2. Följ stegen ovan i den här artikeln, där du letade upp tjänstens URL och API-nyckeln på [Azure Portal](https://portal.azure.com), för att hämta de värden som du nu ska ange i **config.properties**.
3. I **config.properties** ersätter du ”Api Key” med API-nyckeln för tjänsten. Därefter ska tjänstnamnet (den första delen av URL:en http://servicename.search.windows.net) ersätta ”service name” i samma fil.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Konfigurera projektet, versionen och runtime-miljöerna
1. I Eclipse högerklickar du på projektet i Project Explorer > **Properties** > **Project Facets**.
2. Välj **Dynamic Web Module**, **Java** och **JavaScript**.
   
    ![][6]
3. Klicka på **Apply**.
4. Välj **Window** > **Preferences** > **Server** > **Runtime Environments** > **Add**.
5. Expandera Apache och välj den version av Apache Tomcat-servern som du installerade tidigare. I vårt system installerade vi version 8.
   
    ![][7]
6. Ange installationskatalogen för Tomcat på nästa sida. På en Windows-dator är detta antagligen C:\Program\Apache Software Foundation\Tomcat *version*.
7. Klicka på **Finish**.
8. Välj **Window** > **Preferences** > **Java** > **Installed JREs** > **Add**.
9. Välj **Standard VM**i **Add JRE**.
10. Klicka på **Next**.
11. Klicka på **Directory** i JRE Definition på JRE-startsidan.
12. Gå till **Program Files** > **Java** och välj den JDK som du installerade tidigare. Det är viktigt att du väljer JDK som JRE.
13. Välj **JDK** i Installed JREs. Inställningarna bör se ut som i följande skärmbild.
    
    ![][9]
14. Du kan också välja **Window** > **Web Browser** > **Internet Explorer** om du vill öppna programmet i ett externt webbläsarfönster. En extern webbläsare ger dig en bättre webbupplevelse.
    
    ![][8]

Nu har du slutfört konfigurationsåtgärderna. Nu är det dags att bygga och köra projektet.

## <a name="build-the-project"></a>Bygga projektet
1. Högerklicka på projektets namn i Project Explorer och välj **Run as** > **Maven build** för att konfigurera projektet.
   
    ![][10]
2. I Goals i Edit Configuration skriver du ”clean install” och klickar på **Run**.

Statusmeddelanden visas i konsolfönstret. Meddelandet BUILD SUCCESS bör visas som anger att projektet har skapats utan fel.

## <a name="run-the-app"></a>Kör appen
I det sista steget ska du köra programmet i körningsmiljön för en lokal server.

Om du inte har angett serverkörningsmiljön i Eclipse än så måste du göra det först.

1. Expandera **WebContent** i Project Explorer.
2. Högerklicka på **Search.jsp** > **Run As** > **Run on Server**. Välj Apache Tomcat-servern och klicka på **Run**.

> [!TIP]
> Om du använder en annan arbetsyta än en standardarbetsyta för att lagra projektet måste du ändra **Run Configuration** så att det pekar på projektets plats för att undvika fel när servern startar. I Project Explorer högerklickar du på **Search.jsp** > **Run As** > **Run Configurations**. Välj Apache Tomcat-servern. Klicka på **Arguments**. Klicka på **Workspace** eller **File System** för att ange mappen som innehåller projektet.
> 
> 

När du kör programmet bör du se ett webbläsarfönster med en sökruta där du kan ange söktermer.

Vänta ungefär en minut innan du klickar på **Search** så att tjänsten får tid på sig att skapa och läsa in indexet. Om ett HTTP 404-fel returneras väntar du bara lite längre innan du försöker igen.

## <a name="search-on-usgs-data"></a>Söka i USGS-data
USGS-datauppsättningen innehåller poster som är relevanta för delstaten Rhode Island. Om du klickar på **Search** i en tom sökrutan returneras 50 poster, vilket är standard.

Om du skriver en sökterm ger du sökmotorn något att gå på. Prova att skriva namnet på någon från regionen. ”Roger Williams” var Rhode Islands första guvernör. Många parker, byggnader och skolor bär hans namn.

![][11]

Du kan också prova någon av dessa söktermer:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Nästa steg
Det här är den första Azure Search-självstudiekursen som baseras på Java och USGS-datauppsättningen. Med tiden kommer vi att utöka den här självstudiekursen och demonstrera ytterligare sökfunktioner som du kanske vill använda i dina anpassade lösningar.

Om du redan har viss erfarenhet av Azure Search kan du använda det här exemplet som en utgångspunkt för ytterligare experiment och kanske utöka [söksidan](search-pagination-page-layout.md) eller implementera [aspektbaserad navigering](search-faceted-navigation.md). Du kan även förbättra sidan med sökresultat genom att lägga till antal och batchbearbeta dokument så att användarna kan bläddra igenom resultaten.

Har du inte provat Azure Search än? Vi rekommenderar att du går andra självstudiekurser så att du ser vad du kan skapa. Vår [dokumentationssida](https://azure.microsoft.com/documentation/services/search/) innehåller fler resurser. Mer information finns också på länkarna i [listan med videoklipp och självstudiekurser](search-video-demo-tutorial-list.md).

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

