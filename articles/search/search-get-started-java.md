---
title: 'Snabbstart: Skapa ett Azure Search-index i Java'
description: 'Förklarar hur du skapar ett index, läser in data och kör frågor med Java och Azure Search REST-API: er.'
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 7deb9d2cf16aa82de7ce4ea163652c2936819063
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533232"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>Snabbstart: Skapa ett Azure Search-index i Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [NET](search-howto-dotnet-sdk.md)
> 
> 

Lär dig hur du skapar ett anpassat Java-sökprogram som använder Azure Search som sökmiljö. I den här självstudiekursen används [REST-API:et för tjänsten Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) för att skapa de objekt och åtgärder som används i den här övningen.

Om du vill köra det här exemplet måste du ha en Azure Search-tjänst, som du kan registrera dig för på [Azure Portal](https://portal.azure.com). Stegvisa instruktioner finns i [Skapa en Azure Search-tjänst på portalen](search-create-service-portal.md).

Vi använde följande programvara när vi skapade och testade det här exemplet:

* [Eclipse IDE för Java EE-utvecklare](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Var noga med att ladda ned EE-versionen. Ett av verifieringsstegen kräver en funktion som bara finns i den här versionen.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat-8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>Om de data som används
Det här exempelprogrammet använder data från [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm), som har filtrerats på delstaten Rhode Island för att minska datauppsättningens storlek. Vi ska använda dessa data för att skapa ett sökprogram som returnerar viktiga byggnader som sjukhus och skolor, samt geologiska element som vattendrag, sjöar och bergstoppar.

I det här programmet skapar **SearchServlet. java** -programmet och läser in indexet med [](https://msdn.microsoft.com/library/azure/dn798918.aspx) hjälp av en indexerare konstruktion och hämtar den filtrerade USGS datauppsättningen-datauppsättningen från en Azure SQL Database. Fördefinierade autentiseringsuppgifter och anslutningsinformation för onlinedatakällan finns i programkoden. Ingen ytterligare konfiguration krävs vad gäller dataåtkomsten.

> [!NOTE]
> Vi har använt ett filter för den här datauppsättningen för att hålla oss under gränsen på 10 000 dokument för den kostnadsfria prisnivån. Om du använder standardnivån så gäller inte den här gränsen och du kan ändra koden om du vill använda en större datauppsättning. Mer information om kapaciteten för varje prisnivå finns i [Gränser och begränsningar](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Om programfilerna
Följande lista beskriver de filer som är relevanta för det här exemplet.

* Search. jsp: Tillhandahåller användar gränssnittet
* SearchServlet.java: Tillhandahåller metoder (liknar en kontrollant i MVC)
* SearchServiceClient.java: Hanterar HTTP-begäranden
* SearchServiceHelper.java: En hjälp klass som tillhandahåller statiska metoder
* Document. java: Tillhandahåller data modellen
* config. Properties: Anger Sök tjänstens URL och`api-key`
* pom.xml: Ett maven-beroende

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Hitta tjänst namnet och `api-key` din Azure Search-tjänst
Alla REST API anrop till Azure Search kräver att du anger tjänstens URL och en `api-key`. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I hopp fältet väljer du **Sök tjänst** för att visa alla Azure Search tjänster som har skapats för din prenumeration.
3. Markera den tjänst som du vill använda.
4. På instrumentpanelen för tjänsten ser du paneler för viktig information samt nyckelikonen för att komma åt administatörsnycklarna.
   
      ![Skärm bild som visar hur du kommer åt administratörs nycklarna från instrument panelen för tjänsten][3]
5. Kopiera tjänstens URL och en administratörsnyckel. Du behöver dem senare när du lägger till dem i filen **config.properties**.

## <a name="download-the-sample-files"></a>Ladda ned exempelfilerna
1. Gå till [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) på GitHub.
2. Välj **Hämta zip**, spara zip-filen på disk och extrahera sedan alla filer som den innehåller. Om du vill kan du extrahera filerna till Java-arbetsytan så att det blir lättare att hitta projektet senare.
3. Exempelfilerna är skrivskyddade. Högerklicka på Mappegenskaper och ta bort skrivskyddet.

Alla efterföljande filändringar och körningsinstruktioner görs mot filer i den här mappen.  

## <a name="import-project"></a>Importera projekt
1. I Sol förmörkelse väljer du **fil** > **Importera** > **allmänna** > **befintliga projekt till arbets ytan**.
   
    ![Skärm bild som visar hur du importerar ett befintligt projekt][4]
2. I **Select root directory** bläddrar du till mappen som innehåller exempelfilerna. Välj mappen som innehåller mappen .project. Projektet bör visas i listan **Projects** som ett markerat objekt.
   
    ![Skärm bild som visar listan projekt i fönstret Importera projekt][12]
3. Välj **Slutför**.
4. Använd **Project Explorer** för att visa och redigera filerna. Om den inte redan är öppen väljer du **fönster** > **Visa** > **projekt Utforskaren** eller använder genvägen för att öppna den.

## <a name="configure-the-service-url-and-api-key"></a>Konfigurera tjänstens URL och`api-key`
1. I **Project Explorer**dubbelklickar du på **config. Properties** för att redigera de konfigurations inställningar som innehåller Server `api-key`namnet och.
2. Läs de steg som beskrivs ovan i den här artikeln, där du har hittat tjänst `api-key` -URL: en och i [Azure Portal](https://portal.azure.com)för att hämta värdena som du kommer att ange i **config. Properties**.
3. I **config. Properties**ersätter du `api-key` "API Key" med för din tjänst. Sedan ersätter tjänst namnet (den första komponenten i URL: https://servicename.search.windows.net) en "tjänst namn" i samma fil.
   
    ![Skärm bild som visar hur du ersätter API-nyckeln][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Konfigurera projektet, versionen och runtime-miljöerna
1. I Eclipse högerklickar du på projektet i Project Explorer > **Properties** > **Project Facets**.
2. Välj **Dynamic Web Module**, **Java** och **JavaScript**.
   
    ![Skärm bild som visar hur du väljer projektets ansikte för ditt projekt][6]
3. Välj **Använd**.
4. Välj **Window** > **Preferences** > **Server** > **Runtime Environments** > **Add**.
5. Expandera Apache och välj den version av Apache Tomcat-servern som du installerade tidigare. I vårt system installerade vi version 8.
   
    ![Skärm bild som visar var i fönstret Runtime Environment du kan välja din version av Apache Tomcat][7]
6. Ange installationskatalogen för Tomcat på nästa sida. På en Windows-dator är detta antagligen C:\Program\Apache Software Foundation\Tomcat *version*.
7. Välj **Slutför**.
8. Välj **Window** > **Preferences** > **Java** > **Installed JREs** > **Add**.
9. Välj **Standard VM**i **Add JRE**.
10. Välj **Nästa**.
11. I JRE-definitionen, i JRE Home, väljer du **katalog**.
12. Gå till **Program Files** > **Java** och välj den JDK som du installerade tidigare. Det är viktigt att du väljer JDK som JRE.
13. I installerade JREs väljer du **JDK**. Inställningarna bör se ut som i följande skärmbild.
    
    ![Skärm bild som visar hur du väljer JDK som den installerade JRE][9]
14. Du kan också välja **Window** > **Web Browser** > **Internet Explorer** om du vill öppna programmet i ett externt webbläsarfönster. En extern webbläsare ger dig en bättre webbupplevelse.
    
    ![Skärm bild som visar hur du väljer Internet Explorer som ett externt webbläsarfönster][8]

Nu har du slutfört konfigurationsåtgärderna. Nu är det dags att bygga och köra projektet.

## <a name="build-the-project"></a>Bygga projektet
1. I Project Explorer högerklickar du på projekt namnet och väljer **Kör som** > **maven build** för att konfigurera projektet.
   
    ![Skärm bild som visar hur du väljer maven-version i fönstret projekt Utforskaren][10]
2. I redigera konfiguration, i mål, anger du "ren installation" och väljer sedan **Kör**.

Statusmeddelanden visas i konsolfönstret. Meddelandet BUILD SUCCESS bör visas som anger att projektet har skapats utan fel.

## <a name="run-the-app"></a>Kör appen
I det sista steget ska du köra programmet i körningsmiljön för en lokal server.

Om du inte har angett serverkörningsmiljön i Eclipse än så måste du göra det först.

1. Expandera **WebContent** i Project Explorer.
2. Högerklicka på **Search.jsp** > **Run As** > **Run on Server**. Välj Apache Tomcat-servern och välj sedan **Kör**.

> [!TIP]
> Om du använder en annan arbetsyta än en standardarbetsyta för att lagra projektet måste du ändra **Run Configuration** så att det pekar på projektets plats för att undvika fel när servern startar. I Project Explorer högerklickar du på **Search.jsp** > **Run As** > **Run Configurations**. Välj Apache Tomcat-servern. Välj **argument**. Välj **arbets yta** eller **fil system** för att ange mappen som innehåller projektet.
> 
> 

När du kör programmet bör du se ett webbläsarfönster med en sökruta där du kan ange söktermer.

Vänta en stund innan du väljer **Sök** för att ge tjänst tiden att skapa och läsa in indexet. Om ett HTTP 404-fel returneras väntar du bara lite längre innan du försöker igen.

## <a name="search-on-usgs-data"></a>Söka i USGS-data
USGS-datauppsättningen innehåller poster som är relevanta för delstaten Rhode Island. Om du väljer **Sök** i en tom sökruta visas de översta 50 posterna, som är standard.

Om du skriver en sökterm ger du sökmotorn något att gå på. Prova att skriva namnet på någon från regionen. ”Roger Williams” var Rhode Islands första guvernör. Många parker, byggnader och skolor bär hans namn.

![Skärm bild som visar hur du söker efter USGS DATAUPPSÄTTNINGEN-data][11]

Du kan också prova någon av dessa söktermer:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Nästa steg
Det här är den första Azure Search-självstudiekursen som baseras på Java och USGS-datauppsättningen. Med tiden kommer vi att utöka den här självstudiekursen och demonstrera ytterligare sökfunktioner som du kanske vill använda i dina anpassade lösningar.

Om du redan har viss erfarenhet av Azure Search kan du använda det här exemplet som en utgångspunkt för ytterligare experiment och kanske utöka [söksidan](search-pagination-page-layout.md) eller implementera [aspektbaserad navigering](search-faceted-navigation.md). Du kan även förbättra sidan med sökresultat genom att lägga till antal och batchbearbeta dokument så att användarna kan bläddra igenom resultaten.

Har du inte provat Azure Search än? Vi rekommenderar att du går andra självstudiekurser så att du ser vad du kan skapa. Vår [dokumentationssida](https://azure.microsoft.com/documentation/services/search/) innehåller fler resurser. 

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
