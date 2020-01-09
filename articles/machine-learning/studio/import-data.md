---
title: Importera utbildningsdata
titleSuffix: ML Studio (classic) - Azure
description: Så här importerar du data till Azure Machine Learning Studio (klassisk) från olika data källor. Lär dig mer om vilka data typer och data format som stöds.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 95938b979a90766c7e50f2560cf72266e287bfb5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454696"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importera dina utbildnings data till Azure Machine Learning Studio (klassisk) från olika data källor

Om du vill använda dina egna data i Machine Learning Studio (klassisk) för att utveckla och träna en förutsägelse analys lösning kan du använda data från: 

* **Lokal fil** – Läs in lokala data i förväg från hård disken för att skapa en data uppsättnings modul i din arbets yta
* **Online-datakällor** – Använd modulen [Importera data][import-data] för att få åtkomst till data från en av flera onlinekällor medan experimentet körs
* **Machine Learning Studio (klassisk) experiment** -Använd data som sparats som en data uppsättning i Machine Learning Studio (klassisk)
* [**Lokal SQL Server databas**](use-data-from-an-on-premises-sql-server.md) – Använd data från en lokal SQL Server databas utan att behöva kopiera data manuellt

> [!NOTE]
> Det finns ett antal exempel data uppsättningar som är tillgängliga i Machine Learning Studio (klassisk) som du kan använda för tränings data. Information om dessa finns i [använda exempel data uppsättningar i Azure Machine Learning Studio (klassisk)](use-sample-datasets.md).

## <a name="prepare-data"></a>Förbered data

Machine Learning Studio (klassisk) är utformad för att fungera med rektangulära eller tabell data, till exempel text data som är avgränsade eller strukturerade data från en databas, men i vissa fall kan icke-rektangulära data användas.

Det är bäst om dina data är relativt rena innan du importerar dem till Studio (klassisk). Till exempel vill du kunna ta hand om problem som strängar som inte är citerade.

Det finns dock moduler som är tillgängliga i Studio (klassisk) som möjliggör viss manipulering av data i experimentet när du har importerat dina data. Beroende på vilka Machine Learning-algoritmer du kommer att använda kan du behöva bestämma hur du ska hantera problem med data struktur, till exempel saknade värden och sparse-data, och det finns moduler som kan hjälpa dig med det. Titta i avsnittet **datatransformering** i modulen modul för moduler som utför dessa funktioner.

Du kan när som helst i experimentet Visa eller hämta de data som skapas av en modul genom att klicka på utdataporten. Beroende på modulen kan det finnas olika tillgängliga nedladdnings alternativ, eller så kan du visualisera data i webbläsaren i Studio (klassisk).

## <a name="supported-data-formats-and-data-types"></a>Data format och data typer som stöds

Du kan importera ett antal data typer till experimentet, beroende på vilken mekanism du använder för att importera data och var de kommer från:

* Oformaterad text (. txt)
* Kommaavgränsade värden (CSV) med ett sidhuvud (. csv) eller utan (. NH. csv)
* Tabbavgränsade värden (TSV) med ett sidhuvud (. tsv) eller utan (. NH. tsv)
* Excel-fil
* Azure-tabell
* Hive-tabell
* SQL Database-tabell
* OData-värden
* SVMLight-data (. SVMLight) (se [SVMLight-definitionen](http://svmlight.joachims.org/) för format information)
* ARFF-data (Attribute Relations fil format) (. arff) (se [arff-definitionen](https://weka.wikispaces.com/ARFF) för format information)
* Zip-fil (. zip)
* R-objekt eller arbetsytefil (. RData

Om du importerar data i ett format, till exempel ARFF som innehåller metadata, använder Studio (klassisk) dessa metadata för att definiera rubriken och data typen för varje kolumn.

Om du importerar data, till exempel TSV-eller CSV-format som inte innehåller dessa metadata, härleds data typen för varje kolumn genom att data samplas. Om data inte heller har kolumn rubriker, tillhandahåller Studio (klassisk) standard namn.

Du kan uttryckligen ange eller ändra rubriker och data typer för kolumner med hjälp av modulen [Redigera metadata][edit-metadata] .

Följande data typer kan identifieras av Studio (klassisk):

* String
* Integer
* Double
* Boolean
* DateTime
* TimeSpan

Studio använder en intern datatyp som kallas ***data tabell*** för att skicka data mellan moduler. Du kan uttryckligen konvertera dina data till data tabell format med hjälp av modulen [konvertera till data uppsättning][convert-to-dataset] .

Alla moduler som accepterar andra format än data tabeller kommer att konvertera data till data tabellen tyst innan de skickas till nästa modul.

Vid behov kan du konvertera data tabell format tillbaka till CSV-, TSV-, ARFF-eller SVMLight-format med andra konverterings moduler.
Titta i avsnittet **data format konverteringar** i modulen modul för moduler som utför dessa funktioner.

## <a name="data-capacities"></a>Data kapaciteter

Moduler i Machine Learning Studio (klassisk) har stöd för data uppsättningar på upp till 10 GB kompakta numeriska data för vanliga användnings fall. Om en modul hämtar indata från mer än ett ställe är värdet 10 GB summan av alla indata. Du kan prova större data uppsättningar genom att använda frågor från Hive eller Azure SQL Database, eller så kan du använda för bearbetning av antal inlärnings data innan du importerar data.  

Följande typer av data kan expanderas till större datauppsättningar under funktionsnormalisering och är begränsade till mindre än 10 GB:

* Utspridda
* Kategoriska
* Strängar
* Binära data

Följande moduler är begränsade till datauppsättningar som är mindre än 10 GB:

* Moduler för rekommenderare
* Modulen SMOTE (Synthetic Minority Oversampling Technique)
* Skriptmoduler: R, Python, SQL
* Moduler där den utgående datastorleken kan vara större än den inkommande datastorleken, till exempel kopplings- eller funktions-hashning
* Korsvalidering, hyperparametrar för justeringsmodeller, ordningstalsregression och ”en eller alla”-multiklasser, om antalet iterationer är mycket stort

För data uppsättningar som är större än ett par GB laddar du upp data till Azure Storage eller Azure SQL Database eller använder Azure HDInsight, i stället för att ladda upp direkt från en lokal fil.

Du kan hitta information om bilddata i modulen [Importera avbildningar](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importera från en lokal fil

Du kan ladda upp en datafil från hård disken och använda den som utbildnings data i Studio (klassisk). När du importerar en datafil skapar du en data uppsättnings modul som är redo att användas i experiment i din arbets yta.

Om du vill importera data från en lokal hårddisk, gör du följande:

1. Klicka på **+ ny** längst ned i Studio-fönstret (klassisk).
2. Välj **DATAUPPSÄTTNING** och **från lokal fil**.
3. I dialog rutan **Ladda upp en ny data uppsättning** bläddrar du till den fil som du vill ladda upp.
4. Ange ett namn, identifiera datatypen och även ange en beskrivning. En beskrivning rekommenderas – du kan registrera alla egenskaper om de data som du vill komma ihåg när du använder data i framtiden.
5. Kryssrutan **det här är den nya versionen av en befintlig datamängd** gör att du kan uppdatera en befintlig datamängd med nya data. Det gör du genom att klicka på den här kryss rutan och sedan ange namnet på en befintlig data uppsättning.

![Ladda upp en ny datauppsättning](./media/import-data/upload-dataset-from-local-file.png)

Ladda upp tiden beror på storleken på dina data och hastigheten på din anslutning till tjänsten. Om du vet att filen tar lång tid kan du göra andra saker i Studio (klassisk) medan du väntar. Men om du stänger webbläsaren innan data överföringen är klar orsakar överföringen att Miss lyckas.

När dina data har överförts, lagras i en datauppsättning modul och är tillgänglig för alla experiment på arbetsytan.

När du redigerar ett experiment kan du hitta de data uppsättningar som du har laddat upp i listan **mina data uppsättningar** under listan **sparade data uppsättningar** i modulen modul. Du kan dra och släpp datauppsättningen till experimentets arbetsyta när du vill använda datauppsättningen för ytterligare analys och maskininlärning.

## <a name="import-from-online-data-sources"></a>Importera från data källor online

Med hjälp av modulen [Importera data][import-data] kan experimentet importera data från olika data källor online medan experimentet körs.

> [!NOTE]
> Den här artikeln innehåller allmän information om modulen [Importera data][import-data] . Mer detaljerad information om vilka typer av data som du kan komma åt, format, parametrar och svar på vanliga frågor finns i avsnittet modulreferens i modulen [Importera data][import-data] .

Med hjälp av modulen [Importera data][import-data] kan du komma åt data från en av flera data källor online medan experimentet körs:

* En URL med HTTP
* Hadoop med HiveQL
* Azure Blob Storage
* Azure-tabell
* Azure SQL database eller SQL Server på Azure VM
* En lokal SQL Server-databas
* En provider, för närvarande OData-datafeed
* Azure Cosmos DB

Eftersom dessa inlärnings data används medan experimentet körs, är det bara tillgängligt i det experimentet. I jämförelse är data som har lagrats i en data uppsättnings modul tillgänglig för alla experiment i din arbets yta.

Om du vill komma åt online-datakällor i ditt Studio (klassiska) experiment lägger du till modulen [Importera data][import-data] i experimentet. Välj sedan **guiden för att starta import data** under **Egenskaper** för steg-för-steg-instruktioner för att välja och konfigurera data källan. Alternativt kan du manuellt välja **data källa** under **Egenskaper** och ange de parametrar som krävs för att få åtkomst till data.

Online-datakällor som stöds är uppdelat i tabellen nedan. Den här tabellen sammanfattar också filformat som stöds och parametrar som används för att komma åt data.

> [!IMPORTANT]
> För närvarande kan modulen [Importera data][import-data] och [Exportera data][export-data] bara läsa och skriva data från Azure Storage som skapats med den klassiska distributions modellen. Med andra ord stöds den nya Azure Blob Storage-kontotyp som erbjuder en frekvent åtkomstnivå eller lågfrekvent åtkomstnivå inte ännu.
>
> I allmänhet bör alla Azure storage-konton som du kan ha skapat innan det här alternativet om tjänsten blev tillgängliga bör inte påverkas.
> Om du vill skapa ett nytt konto kan du välja **klassiska** för distributionen modell, eller använda Resource manager och välj **generella** snarare än **Blob-lagring** för  **Typ av konto**.
>
> Mer information finns i [Azure Blob Storage: frekvent och lågfrekvent lagringsnivå](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Online-datakällor som stöds
Modulen Azure Machine Learning Studio (klassisk) **Importera data** stöder följande data Källor:

| Datakälla | Beskrivning | Parametrar |
| --- | --- | --- |
| Webbadress till via HTTP |Läser data i fil med kommaavgränsade värden (CSV), tabbavgränsade värden (TVS), Attributrelation filformatet (ARFF) och Support Vector datorer (SVM lätt)-format, från valfri webbtjänst-URL som använder HTTP |<b>URL: en</b>: Anger det fullständiga namnet på filen, inklusive webbplatsens URL och filnamn, med alla tillägg. <br/><br/><b>Dataformatet</b>: Anger en av data som stöds formaterar: CSV, TVS, ARFF eller SVM ljus. Om data har en rubrikrad, används den tilldelas kolumnnamn. |
| Hadoop/HDFS |Läser data från distribuerad lagring i Hadoop. Du anger de data du vill genom att använda HiveQL, ett SQL-liknande frågespråk. HiveQL kan också användas för att aggregera data och utföra data filtrering innan du lägger till data i Studio (klassisk). |<b>Hive databasfråga</b>: Anger Hive-fråga som används för att generera data.<br/><br/><b>HCatalog server URI </b> : anges namnet på klustret i formatet  *&lt;klusternamnet&gt;. azurehdinsight.net.*<br/><br/><b>Hadoop-användarkontonamnet</b>: Anger Hadoop-användarkontonamnet används för att etablera klustret.<br/><br/><b>Hadoop lösenord</b> : Anger de autentiseringsuppgifter som används vid etableringen av klustret. Mer information finns i [skapa Hadoop-kluster i HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Platsen för utdata</b>: Anger om data lagras i ett Hadoop distributed file system (HDFS) eller i Azure. <br/><ul>Om du lagrar utdata för i HDFS kan du ange URI för HDFS-servern. (Glöm inte att använda HDInsight-klustrets namn utan prefixet HTTPS://). <br/><br/>Om du lagrar dina utdata i Azure måste du ange Azure storage-kontonamn, åtkomstnyckel för lagring och namnet på lagringsbehållaren.</ul> |
| SQL-databas |Läser data som lagras i en Azure SQL-databas eller i en SQL Server-databas som körs på virtuella Azure-datorer. |<b>Namn på databasserver</b>: Anger namnet på den server där databasen körs.<br/><ul>Ange servernamnet som genereras när det gäller Azure SQL Database. Har normalt formatet  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Om det finns en SQL Server på en virtuell Azure-dator anger du *TCP:&lt;DNS-namn för virtuell dator&gt;1433*</ul><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Server användarkontonamn</b>: anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Server lösenord</b>: Anger lösenordet för användarkontot.<br/><br/><b>Databasfråga</b>: Ange ett SQL-uttryck som beskriver de data som du vill läsa. |
| Lokal SQL-databas |Läser data som lagras i en lokal SQL-databas. |<b>Datagateway</b>: Anger namnet på Data Management Gateway installerad på en dator där den kan komma åt SQL Server-databasen. Information om hur du konfigurerar gatewayen finns i [utföra avancerad analys med Azure Machine Learning Studio (klassisk) med hjälp av data från en lokal SQL Server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Namn på databasserver</b>: Anger namnet på den server där databasen körs.<br/><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Server användarkontonamn</b>: anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Användarnamn och lösenord</b>: Klicka på <b>ange värden</b> att ange dina Databasautentiseringsuppgifter. Du kan använda Windows-integrerad autentisering eller SQL Server-autentisering beroende på hur din lokala SQL Server är konfigurerat.<br/><br/><b>Databasfråga</b>: Ange ett SQL-uttryck som beskriver de data som du vill läsa. |
| Azure-tabell |Läser data från Table service i Azure Storage.<br/><br/>Om du har läst stora mängder data används sällan använda Azure Table Service. Det ger en flexibel, icke-relationella (NoSQL), mycket skalbar, prisvärd och mycket tillgängliga lagringslösning. |Alternativen i den **importdata** ändras beroende på om du får åtkomst till offentlig information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha värdet för ”PublicOrSAS” eller ”konto”, som har en egen uppsättning parametrar. <br/><br/><b>Offentlig eller signatur för delad åtkomst (SAS) URI</b>: parametrarna är:<br/><br/><ul><b>Tabellen URI</b>: Anger offentlig eller SAS-Webbadressen för tabellen.<br/><br/><b>Anger vilka rader som ska söka efter egenskapsnamn</b>: värdena är <i>TopN</i> söker igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen. <br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett tal för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/></ul><b>Privat Lagringskonto</b>: parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller tabellen att läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringskontots åtkomstnyckel som är associerade med kontot.<br/><br/><b>Tabellnamnet</b> : Anger namnet på den tabell som innehåller data att läsa.<br/><br/><b>Rader som ska söka efter egenskapsnamn</b>: värdena är <i>TopN</i> söker igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen.<br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett tal för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/> |
| Azure Blob Storage |Läser data som lagras i Blob service i Azure Storage, inklusive bilder, ostrukturerad text eller binära data.<br/><br/>Du kan använda Blob-tjänsten att exponera data offentligt eller lagra programdata privat. Du kan komma åt dina data var som helst genom att använda HTTP eller HTTPS-anslutningar. |Alternativen i den **importdata** modul ändras beroende på om du får åtkomst till offentlig information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha ett värde för ”PublicOrSAS” eller ”konto”.<br/><br/><b>Offentlig eller signatur för delad åtkomst (SAS) URI</b>: parametrarna är:<br/><br/><ul><b>URI: N</b>: Anger offentlig eller SAS-Webbadress för blob storage.<br/><br/><b>Filformat för</b>: Anger formatet för data i Blob-tjänsten. Format som stöds är CSV, TVS och ARFF.<br/><br/></ul><b>Privat Lagringskonto</b>: parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller den blob som du vill läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringskontots åtkomstnyckel som är associerade med kontot.<br/><br/><b>Sökvägen till behållaren, katalogen eller blob </b> : Anger namnet på bloben som innehåller data att läsa.<br/><br/><b>BLOB-filformatet</b>: Anger formatet för data i blobtjänsten. Format som stöds är CSV, TVS, ARFF, CSV med en angiven kodning och Excel. <br/><br/><ul>Om formatet är CSV- eller TVS, måste du ange om filen innehåller en rubrikrad.<br/><br/>Du kan använda Excel-alternativet för att läsa data från Excel-arbetsböcker. I den <i>Excel dataformat</i> måste du ange om data är i ett Excel-kalkylbladsområde eller i en Excel-tabell. I den <i>Excel-blad eller inbäddade tabell </i>, anger du namnet på kalkylbladet eller tabellen som du vill läsa från.</ul><br/> |
| -Datafeedprovider |Läser data från en leverantör som stöds feed. För närvarande endast formatet Open Data Protocol (OData) stöds. |<b>Innehållstyp som data</b>: Anger OData-formatet.<br/><br/><b>Käll-URL</b>: Anger en fullständig URL för datafeeden. <br/>Till exempel läser följande URL från exempeldatabasen: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importera från ett annat experiment

Det kommer att finnas tillfällen när du vill göra ett mellanliggande resultat från ett experiment och använda det som en del av ett annat experiment. Det gör du genom att spara modulen som en data uppsättning:

1. Klicka på utdata från den modul som du vill spara som en data uppsättning.
2. Klicka på **Spara som data uppsättning**.
3. När du uppmanas till det anger du ett namn och en beskrivning som gör det enkelt att identifiera data uppsättningen.
4. Klicka på **bock markeringen** .

När Spara-funktionen är klar är data uppsättningen tillgänglig för användning i alla experiment på arbets ytan. Du hittar den i listan med **sparade data uppsättningar** i modulen modul.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure Machine Learning Studio webb tjänster som använder moduler för data import och data export](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
