---
title: Importera utbildningsdata
titleSuffix: ML Studio (classic) - Azure
description: Så här importerar du data till Azure Machine Learning Studio (klassiskt) från olika datakällor. Läs om vilka datatyper och dataformat som stöds.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: cee49124a7547399889e425008a8580b9b25945a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217988"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importera dina träningsdata till Azure Machine Learning Studio (klassisk) från olika datakällor

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Om du vill använda dina egna data i Machine Learning Studio (klassiskt) för att utveckla och träna en lösning för prediktiv analys kan du använda data från: 

* **Lokal fil** – Ladda lokala data i förväg från hårddisken för att skapa en datauppsättningsmodul på arbetsytan
* **Onlinedatakällor** – Använd modulen [Importera data][import-data] för att komma åt data från en av flera onlinekällor medan experimentet körs
* **Machine Learning Studio (klassiskt) experiment** – Använd data som sparades som en datauppsättning i Machine Learning Studio (klassiskt)
* [**Lokal SQL Server-databas**](use-data-from-an-on-premises-sql-server.md) - Använda data från en lokal SQL Server-databas utan att behöva kopiera data manuellt

> [!NOTE]
> Det finns ett antal exempeldatauppsättningar som är tillgängliga i Machine Learning Studio (klassiskt) som du kan använda för träningsdata. Information om dessa finns [i Använda exempeldatauppsättningarna i Azure Machine Learning Studio (klassiskt).](use-sample-datasets.md)

## <a name="prepare-data"></a>Förbereda data

Machine Learning Studio (klassisk) är utformad för att fungera med rektangulära eller tabellformiga data, till exempel textdata som avgränsas eller strukturerade data från en databas, men i vissa fall kan icke-rektangulära data användas.

Det är bäst om dina data är relativt rena innan du importerar dem till Studio (klassisk). Du bör till exempel ta hand om problem som onoterade strängar.

Det finns dock moduler i Studio (klassisk) som möjliggör viss manipulering av data i experimentet när du har importerat dina data. Beroende på vilka maskininlärningsalgoritmer du ska använda kan du behöva bestämma hur du ska hantera strukturella dataproblem som saknade värden och glesa data, och det finns moduler som kan hjälpa till med det. Titta i avsnittet **Dataomvandling** i modulpaletten efter moduler som utför dessa funktioner.

När som helst i experimentet kan du visa eller hämta data som produceras av en modul genom att klicka på utdataporten. Beroende på modulen kan det finnas olika nedladdningsalternativ tillgängliga, eller så kanske du kan visualisera data i din webbläsare i Studio (klassisk).

## <a name="supported-data-formats-and-data-types"></a>Dataformat och datatyper som stöds

Du kan importera ett antal datatyper till experimentet, beroende på vilken mekanism du använder för att importera data och varifrån de kommer:

* Oformaterad text (.txt)
* Kommaavgränsade värden (CSV) med en rubrik (.csv) eller utan (.nh.csv)
* Tab-avgränsade värden (TSV) med en rubrik (.tsv) eller utan (.nh.tsv)
* Excel-fil
* Azure-tabell
* Tabell i Hive
* SQL-databastabell
* OData-värden
* SVMLight-data (.svmlight) (se [SVMLight-definitionen](http://svmlight.joachims.org/) för formatinformation)
* ARFF-data (Attribute Relation File Format) (.arff) (se [ARFF-definitionen](https://weka.wikispaces.com/ARFF) för formatinformation)
* Zip-fil (.zip)
* R-objekt eller arbetsyta (. RData)

Om du importerar data i ett format som ARFF som innehåller metadata använder Studio (klassisk) dessa metadata för att definiera rubriken och datatypen för varje kolumn.

Om du importerar data som TSV- eller CSV-format som inte innehåller dessa metadata, drar Studio (klassisk) in datatypen för varje kolumn genom att samplingsdata. Om data inte heller har kolumnrubriker innehåller Studio (klassisk) standardnamn.

Du kan uttryckligen ange eller ändra rubriker och datatyper för kolumner med modulen [Redigera metadata.][edit-metadata]

Följande datatyper känns igen av Studio (klassisk):

* String
* Integer
* Double
* Boolean
* DateTime
* TimeSpan

Studio använder en intern datatyp som kallas ***datatabell*** för att skicka data mellan moduler. Du kan uttryckligen konvertera dina data till datatabellformat med modulen [Konvertera till datauppsättning.][convert-to-dataset]

Alla moduler som accepterar andra format än datatabell konverterar data till datatabellen tyst innan de skickar den till nästa modul.

Om det behövs kan du konvertera datatabellformat tillbaka till CSV-, TSV-, ARFF- eller SVMLight-format med andra konverteringsmoduler.
Titta i avsnittet **Dataformatkonverteringar** i modulpaletten efter moduler som utför dessa funktioner.

## <a name="data-capacities"></a>Datakapacitet

Moduler i Machine Learning Studio (klassisk) stöder datauppsättningar på upp till 10 GB tät numeriska data för vanliga användningsfall. Om en modul hämtar indata från mer än ett ställe är värdet 10 GB summan av alla indata. Du kan prova större datauppsättningar med hjälp av frågor från Hive eller Azure SQL Database, eller så kan du använda Learning by Counts förbearbetning innan du importerar data.  

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

För datauppsättningar som är större än ett par GB laddar du upp data till Azure Storage eller Azure SQL Database eller använder Azure HDInsight i stället för att ladda upp direkt från en lokal fil.

Du hittar information om bilddata i modulereferensen [Importera bilder.](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)

## <a name="import-from-a-local-file"></a>Importera från en lokal fil

Du kan ladda upp en datafil från hårddisken för att använda som träningsdata i Studio (klassisk). När du importerar en datafil skapar du en datauppsättningsmodul som är klar att användas i experiment på arbetsytan.

Så här importerar du data från en lokal hårddisk:

1. Klicka på **+NYTT** längst ned i studiofönstret (klassiskt).
2. Välj **DATAUPPSÄTTNING** och **FRÅN LOKAL FIL**.
3. Bläddra till den fil som du vill ladda upp i dialogrutan **Ladda upp en ny datauppsättning.**
4. Ange ett namn, identifiera datatypen och ange eventuellt en beskrivning. En beskrivning rekommenderas - det låter dig registrera några egenskaper om de data som du vill komma ihåg när du använder data i framtiden.
5. Kryssrutan **Det här är den nya versionen av en befintlig datauppsättning** gör att du kan uppdatera en befintlig datauppsättning med nya data. Om du vill göra det klickar du på den här kryssrutan och anger sedan namnet på en befintlig datauppsättning.

![Ladda upp en ny datauppsättning](./media/import-data/upload-dataset-from-local-file.png)

Uppladdningstiden beror på storleken på dina data och hastigheten på din anslutning till tjänsten. Om du vet att filen kommer att ta lång tid, kan du göra andra saker i Studio (klassisk) medan du väntar. Om du stänger webbläsaren innan dataöverföringen är klar misslyckas dock överföringen.

När dina data har laddats upp lagras de i en datauppsättningsmodul och är tillgängligt för alla experiment på arbetsytan.

När du redigerar ett experiment hittar du de datauppsättningar som du har laddat upp i listan **Mina datauppsättningar** under listan **Sparade datauppsättningar** i modulpaletten. Du kan dra och släppa datauppsättningen på experimentarbetsytan när du vill använda datauppsättningen för ytterligare analyser och maskininlärning.

## <a name="import-from-online-data-sources"></a>Importera från datakällor online

Med modulen [Importera data][import-data] kan experimentet importera data från olika datakällor online medan experimentet körs.

> [!NOTE]
> Den här artikeln innehåller allmän information om modulen [Importera data.][import-data] Mer detaljerad information om vilka typer av data du kan komma åt, format, parametrar och svar på vanliga frågor finns i modulens referensavsnitt för modulen [Importera data.][import-data]

Med hjälp av modulen [Importera data][import-data] kan du komma åt data från en av flera onlinedatakällor medan experimentet körs:

* En webb-URL med HTTP
* Hadoop med HiveQL
* Azure Blob Storage
* Azure-tabell
* Azure SQL-databas eller SQL Server på Azure VM
* Lokal SQL Server-databas
* En datafeedleverantör, OData för närvarande
* Azure Cosmos DB

Eftersom de här träningsdata används medan experimentet körs är de bara tillgängliga i det experimentet. Som jämförelse är data som har lagrats i en datauppsättningsmodul tillgängliga för alla experiment på arbetsytan.

Om du vill komma åt onlinedatakällor i ditt studioexperiment (klassiskt) lägger du till modulen [Importera data][import-data] i experimentet. Välj sedan **Guiden Starta importdata** under **Egenskaper** för steg-för-steg-guidade instruktioner för att välja och konfigurera datakällan. Du kan också manuellt välja **Datakälla** under **Egenskaper** och ange de parametrar som behövs för att komma åt data.

De onlinedatakällor som stöds är specificerade i tabellen nedan. I den här tabellen sammanfattas också de filformat som stöds och parametrar som används för att komma åt data.

> [!IMPORTANT]
> För närvarande kan [modulerna Importera data][import-data] och [exportera data][export-data] endast läsa och skriva data från Azure-lagring som skapats med hjälp av den klassiska distributionsmodellen. Med andra ord stöds ännu inte den nya Azure Blob Storage-kontotypen som erbjuder en åtkomstnivå för frekvent lagring eller åtkomstnivå för cool lagring.
>
> I allmänhet bör alla Azure-lagringskonton som du kan ha skapat innan det här tjänstalternativet blev tillgängligt inte påverkas.
> Om du behöver skapa ett nytt konto väljer du **Klassisk** för distributionsmodellen eller använder Resurshanteraren och väljer **Allmänt ändamål** i stället för **Blob-lagring** för **kontoslag**.
>
> Mer information finns i [Azure Blob Storage: Hot and Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Datakällor som stöds online
Modulen Azure Machine Learning Studio (klassisk) **Import Data** stöder följande datakällor:

| Datakälla | Beskrivning | Parametrar |
| --- | --- | --- |
| Webb-URL via HTTP |Läser data i CSV-format (kommaavgränsade värden), TSV (tab-separated values), ARFF (attribute-relation file format) och Support Vector Machines (SVM-light), från valfri webb-URL som använder HTTP |<b>URL</b>: Anger det fullständiga namnet på filen, inklusive webbplatsens URL och filnamnet, med ett eventuellt tillägg. <br/><br/><b>Dataformat</b>: Anger ett av de dataformat som stöds: CSV, TSV, ARFF eller SVM-light. Om data har en rubrikrad används den för att tilldela kolumnnamn. |
| Hadoop/HDFS |Läser data från distribuerad lagring i Hadoop. Du anger vilka data du vill använda HiveQL, ett SQL-liknande frågespråk. HiveQL kan också användas för att aggregera data och utföra datafiltrering innan du lägger till data i Studio (klassisk). |<b>Hive-databasfråga</b>: Anger hive-frågan som används för att generera data.<br/><br/><b>HCatalog server URI</b> : Ange namnet på klustret med hjälp av det format * &lt;som klusternamnet&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop-användarkontonamn</b>: Anger hadoop-användarkontonamnet som används för att etablera klustret.<br/><br/><b>Hadoop-användarkontolösenord</b> : Anger de autentiseringsuppgifter som används vid etablering av klustret. Mer information finns i [Skapa Hadoop-kluster i HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Plats för utdata:</b>Anger om data lagras i ett Hadoop-distribuerat filsystem (HDFS) eller i Azure. <br/><ul>Om du lagrar utdata i HDFS anger du HDFS-serverns URI. (Var noga med att använda HDInsight-klusternamnet utan HTTPS:// prefixet). <br/><br/>Om du lagrar dina utdata i Azure måste du ange Azure-lagringskontonamn, Lagringsåtkomstnyckel och lagringsbehållarenamn.</ul> |
| SQL-databas |Läser data som lagras i en Azure SQL-databas eller i en SQL Server-databas som körs på en virtuell Azure-dator. |<b>Databasservernamn</b>: Anger namnet på den server där databasen körs.<br/><ul>Om Azure SQL Database anges det servernamn som genereras. Vanligtvis har formuläret * &lt;generated_identifier&gt;.database.windows.net.* <br/><br/>Om en SQL-server finns på en virtuell Azure-dator anger *du tcp:&lt;DNS-namn&gt;för virtuell dator , 1433*</ul><br/><b>Databasnamn </b>: Anger namnet på databasen på servern. <br/><br/><b>Serveranvändarkontonamn</b>: Anger ett användarnamn för ett konto som har åtkomstbehörigheter för databasen. <br/><br/><b>Lösenord för serveranvändarkonto</b>: Anger lösenordet för användarkontot.<br/><br/><b>Databasfråga</b>:Ange ett SQL-uttryck som beskriver de data du vill läsa. |
| Lokal SQL-databas |Läser data som lagras i en lokal SQL-databas. |<b>Datagateway</b>: Anger namnet på datahanteringsgatewayen som är installerad på en dator där den kan komma åt SQL Server-databasen. Information om hur du konfigurerar gatewayen finns i [Utföra avancerad analys med Azure Machine Learning Studio (klassisk) med hjälp av data från en lokal SQL-server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Databasservernamn</b>: Anger namnet på den server där databasen körs.<br/><br/><b>Databasnamn </b>: Anger namnet på databasen på servern. <br/><br/><b>Serveranvändarkontonamn</b>: Anger ett användarnamn för ett konto som har åtkomstbehörigheter för databasen. <br/><br/><b>Användarnamn och lösenord</b>: Klicka på <b>Ange värden</b> för att ange databasuppgifterna. Du kan använda Windows Integrated Authentication eller SQL Server Authentication beroende på hur din lokala SQL Server är konfigurerad.<br/><br/><b>Databasfråga</b>:Ange ett SQL-uttryck som beskriver de data du vill läsa. |
| Azure-tabell |Läser data från tabelltjänsten i Azure Storage.<br/><br/>Om du läser stora mängder data sällan använder du Azure Table Service. Det ger en flexibel, icke-relationell (NoSQL), massivt skalbar, billig och högtillgänglig lagringslösning. |Alternativen i **importdata** ändras beroende på om du använder offentlig information eller ett privat lagringskonto som kräver inloggningsuppgifter. Detta bestäms av <b>autentiseringstypen</b> som kan ha värdet "PublicOrSAS" eller "Account", som var och en har sin egen uppsättning parametrar. <br/><br/><b>SAS-signatur (Public eller Shared Access Signature) URI</b>: Parametrarna är:<br/><br/><ul><b>Tabell-URI:</b>Anger den offentliga eller SAS-URL:en för tabellen.<br/><br/><b>Anger vilka rader som ska sökas efter egenskapsnamn</b>: Värdena är <i>TopN</i> för att söka igenom det angivna antalet rader eller <i>ScanAll</i> för att hämta alla rader i tabellen. <br/><br/>Om data är homogena och förutsägbara rekommenderar vi att du väljer *TopN* och anger ett nummer för N. För stora tabeller kan detta resultera i snabbare lästider.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som varierar beroende på tabellens djup och placering väljer du alternativet *ScanAll* för att skanna alla rader. Detta säkerställer integriteten för din resulterande egenskap och metadatakonvertering.<br/><br/></ul><b>Private Storage-konto:</b>Parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på kontot som innehåller tabellen som ska läsas.<br/><br/><b>Kontonyckel:</b>Anger den lagringsnyckel som är kopplad till kontot.<br/><br/><b>Tabellnamn</b> : Anger namnet på den tabell som innehåller de data som ska läsas.<br/><br/><b>Rader som ska sökas efter egenskapsnamn</b>: Värdena är <i>TopN</i> för att skanna det angivna antalet rader eller <i>ScanAll</i> för att hämta alla rader i tabellen.<br/><br/>Om data är homogena och förutsägbara rekommenderar vi att du väljer *TopN* och anger ett nummer för N. För stora tabeller kan detta resultera i snabbare lästider.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som varierar beroende på tabellens djup och placering väljer du alternativet *ScanAll* för att skanna alla rader. Detta säkerställer integriteten för din resulterande egenskap och metadatakonvertering.<br/><br/> |
| Azure Blob Storage |Läser data som lagras i Blob-tjänsten i Azure Storage, inklusive avbildningar, ostrukturerad text eller binära data.<br/><br/>Du kan använda Blob-tjänsten för att offentligt exponera data eller för att lagra programdata privat. Du kan komma åt dina data var du än är med hjälp av HTTP- eller HTTPS-anslutningar. |Alternativen i modulen **Importera data** ändras beroende på om du använder offentlig information eller ett privat lagringskonto som kräver inloggningsuppgifter. Detta bestäms av <b>autentiseringstypen</b> som kan ha ett värde antingen av "PublicOrSAS" eller "Konto".<br/><br/><b>SAS-signatur (Public eller Shared Access Signature) URI</b>: Parametrarna är:<br/><br/><ul><b>URI</b>: Anger den offentliga eller SAS-URL:en för lagringsblobben.<br/><br/><b>Filformat</b>: Anger formatet på data i Blob-tjänsten. Formaten som stöds är CSV, TSV och ARFF.<br/><br/></ul><b>Private Storage-konto:</b>Parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på kontot som innehåller den blob som du vill läsa.<br/><br/><b>Kontonyckel:</b>Anger den lagringsnyckel som är kopplad till kontot.<br/><br/><b>Sökväg till behållare, katalog eller blob</b> : Anger namnet på blob som innehåller de data som ska läsas.<br/><br/><b>Blob-filformat</b>: Anger formatet på data i blob-tjänsten. Dataformat som stöds är CSV, TSV, ARFF, CSV med en angiven kodning och Excel. <br/><br/><ul>Om formatet är CSV eller TSV måste du ange om filen innehåller en rubrikrad.<br/><br/>Du kan använda excel-alternativet för att läsa data från Excel-arbetsböcker. Ange om data finns i ett Excel-kalkylbladsområde eller i en Excel-tabell i <i>excelformatalternativet.</i> I alternativet <i>Excel-blad eller inbäddad tabell </i>anger du namnet på det blad eller den tabell som du vill läsa från.</ul><br/> |
| Leverantör av datafeed |Läser data från en feed-provider som stöds. För närvarande stöds endast OData-formatet (Open Data Protocol). |<b>Datainnehållstyp</b>: Anger OData-formatet.<br/><br/><b>Käll-URL:</b>Anger den fullständiga URL:en för datafeeden. <br/>Följande URL-adress läser till exempel från exempeldatabasen Northwind:https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importera från ett annat experiment

Det kommer att finnas tillfällen när du vill ta ett mellanliggande resultat från ett experiment och använda det som en del av ett annat experiment. För att göra detta sparar du modulen som en datauppsättning:

1. Klicka på utdata för den modul som du vill spara som en datauppsättning.
2. Klicka på **Spara som datauppsättning**.
3. När du uppmanas till det anger du ett namn och en beskrivning som gör att du enkelt kan identifiera datauppsättningen.
4. Klicka **OK** på OK-bocken.

När sparat är klart är datauppsättningen tillgänglig för användning i alla experiment på arbetsytan. Du hittar den i listan **Sparade datauppsättningar** i modulpaletten.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure Machine Learning Studio-webbtjänster som använder moduler för dataimport och dataexport](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
