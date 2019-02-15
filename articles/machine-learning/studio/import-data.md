---
title: Importera data från olika datakällor
titleSuffix: Azure Machine Learning Studio
description: Hur du importerar dina data till Azure Machine Learning Studio från olika datakällor. Lär dig vilka datatyper och dataformat stöds.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: bbae6d4b727f3e3dc51bd57e8badbc6e87814a51
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267894"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importera dina utbildningsdata till Azure Machine Learning Studio från olika datakällor

Om du vill använda dina egna data i Machine Learning Studio för att utveckla och träna en lösning för förutsägelseanalys, kan du använda data från: 

* **Lokal fil** – Läs in lokala data i tid från hårddisken för att skapa en datauppsättning-modulen i din arbetsyta
* **Online-datakällor** – Använd den [importera Data] [ import-data] modul för att komma åt data från en av flera onlinekällor när experimentet körs
* **Machine Learning Studio-experiment** -använder data som har sparats som en datauppsättning i Machine Learning Studio
* [**En lokal SQL Server-databas** ](use-data-from-an-on-premises-sql-server.md) -använda data från en lokal SQL Server-databas utan att behöva kopiera data manuellt

> [!NOTE]
> Det finns ett antal provdatauppsättningar i Machine Learning Studio som du kan använda för träningsdata. Information om dessa finns i [Använd exemplen på datauppsättningar i Azure Machine Learning Studio](use-sample-datasets.md).

## <a name="prepare-data"></a>Förbereda data

Machine Learning Studio är utformad för att arbeta med rektangulär eller tabular data, till exempel textdata som har avgränsade eller strukturerade data från en databas, men i vissa fall icke-rektangulär data kan användas.

Det är bäst om dina data är relativt ren innan du importerar den till Studio. Exempelvis bör du ta hand om problem som till exempel ociterade strängar.

Det finns dock moduler som är tillgängliga i Studio som gör att vissa manipulation av data i experimentet när du har importerat dina data. Beroende på de machine learning-algoritmer som du ska använda, kanske måste du bestämma hur du ska hantera strukturella problem med till exempel värden som saknas och null-optimerade data och det finns moduler som kan hjälpa dig med som. Titta i den **Dataomvandling** avsnittet på modulpaletten för moduler som utför dessa funktioner.

Du kan visa eller ladda ned de data som produceras av en modul genom att klicka på utdataporten när som helst i experimentet. Det kan finnas olika hämtningsalternativ beroende på modulen eller du kan visualisera data i din webbläsare i Studio.

## <a name="supported-data-formats-and-data-types"></a>Stöds dataformat och datatyper

Du kan importera en rad olika datatyper till ditt experiment, beroende på vilken metod du använder för att importera data och där det kommer från:

* Oformaterad text (.txt)
* Fil med kommaavgränsade värden (CSV med en rubrik (.csv) eller utan) (. nh.csv)
* Tabbavgränsade värden (TVS med en rubrik (TSV) eller utan) (. nh.tsv)
* Excel-fil
* Azure-tabell
* Hive-tabell
* SQL-databastabell
* OData-värden
* SVMLight data (.svmlight) (se den [SVMLight definition](http://svmlight.joachims.org/) information format)
* Attributet ARFF Relation File Format ()-data (.arff) (se den [ARFF definition](http://weka.wikispaces.com/ARFF) information format)
* ZIP-filen (.zip)
* R-objekt eller arbetsyta-fil (. RData)

Om du importerar data i ett format till exempel ARFF som innehåller metadata använder Studio dessa metadata för att definiera rubrik och datatypen för varje kolumn.

Om du importerar data, till exempel TSV- eller CSV-format som inte innehåller metadata härleder Studio datatypen för varje kolumn genom att samlar data. Om data inte också har kolumnrubriker, har Studio standardnamn.

Du kan uttryckligen ange eller ändra rubriker och datatyper för kolumner med hjälp av den [redigera Metadata] [ edit-metadata] modulen.

Följande datatyper som identifieras av Studio:

* String
* Integer
* Double
* Boolesk
* DateTime
* TimeSpan

Studio använder en intern datatyp som kallas ***datatabell*** att överföra data mellan moduler. Du kan uttryckligen omvandla dina data till data tabellen format med den [konvertera till datauppsättningen] [ convert-to-dataset] modulen.

Alla moduler som format än datatabell konverterar data till datatabellen tyst vidare till nästa modul.

Om det behövs kan du konvertera data tabellformat tillbaka till CSV, TVS, ARFF eller SVMLight format med hjälp av andra moduler för konvertering.
Titta i den **Format datakonvertering** avsnittet på modulpaletten för moduler som utför dessa funktioner.

## <a name="data-capacities"></a>Datakapaciteter

Modulerna i Machine Learning Studio stöder datauppsättningar på upp till 10 GB med kompakta numeriska data för vanliga användningsfall. Om en modul hämtar indata från mer än ett ställe är värdet 10 GB summan av alla indata. Du kan ta prov på större datauppsättningar med frågor från Hive eller Azure SQL Database eller använda inlärning med antal Förbearbeta innan du importerar data.  

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

För datauppsättningar som är större än ett par GB, ladda upp data till Azure Storage eller Azure SQL Database eller använda Azure HDInsight i stället för att ladda upp direkt från en lokal fil.

Du kan hitta information om avbildningsdata i den [bildimport](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) modulreferens.

## <a name="import-from-a-local-file"></a>Importera från en lokal fil

Du kan överföra en datafil från hårddisken ska användas som träningsdata i Studio. När du importerar en fil, skapar du en datauppsättning till modulen redo för användning i experiment i din arbetsyta.

Om du vill importera data från en lokal hårddisk, gör du följande:

1. Klicka på **+ ny** längst ned i fönstret Studio.
2. Välj **DATAUPPSÄTTNING** och **från lokal fil**.
3. I den **ladda upp en ny datauppsättning** dialogrutan, bläddra till den fil som du vill ladda upp.
4. Ange ett namn, identifiera datatypen och även ange en beskrivning. En beskrivning rekommenderas – du kan registrera alla egenskaper om de data som du vill komma ihåg när du använder data i framtiden.
5. Kryssrutan **det här är den nya versionen av en befintlig datamängd** gör att du kan uppdatera en befintlig datamängd med nya data. Om du vill göra det klickar du på den här kryssrutan och ange namnet på en befintlig datamängd.

![Ladda upp en ny datauppsättning](./media/import-data/upload-dataset-from-local-file.png)

Ladda upp tiden beror på storleken på dina data och hastigheten på din anslutning till tjänsten. Om du vet att filen tar lång tid, kan du göra andra saker i Studio medan du väntar. Dock gör stängt webbläsaren innan data överföringen är klar att överföringen kommer att misslyckas.

När dina data har överförts, lagras i en datauppsättning modul och är tillgänglig för alla experiment på arbetsytan.

När du redigerar ett experiment, hittar du de datauppsättningar som du har laddat upp i den **Mina datauppsättningar** listan den **sparade datauppsättningar** lista på modulpaletten. Du kan dra och släpp datauppsättningen till experimentets arbetsyta när du vill använda datauppsättningen för ytterligare analys och maskininlärning.

## <a name="import-from-online-data-sources"></a>Importera från online-datakällor

Med hjälp av den [importera Data] [ import-data] modulen experimentet kan importera data från olika datakällor för online när experimentet körs.

> [!NOTE]
> Den här artikeln innehåller allmän information om den [importdata] [ import-data] modulen. Mer detaljerad information om vilka typer av data som du kan komma åt format, parametrar och svar på vanliga frågor finns i avsnittet med modulen referens för den [importdata] [ import-data] modulen.

Med hjälp av den [importdata] [ import-data] modulen, du kan komma åt data från en av flera datakällor som online när experimentet körs:

* En URL med HTTP
* Hadoop med HiveQL
* Azure blobblagring
* Azure-tabell
* Azure SQL database eller SQL Server på Azure VM
* En lokal SQL Server-databas
* En provider, för närvarande OData-datafeed
* Azure Cosmos DB

Eftersom den här träningsdata hämtas när experimentet körs, är det bara tillgängliga i försöket. Jämförelsevis har är data som lagrats i en datauppsättning till modulen tillgänglig för alla experiment på arbetsytan.

Du kommer åt online-datakällor i Studio-experiment genom att lägga till den [importdata] [ import-data] modulen i experimentet. Välj sedan **starta guiden Importera** under **egenskaper** stegvisa guidad instruktioner att välja och konfigurera datakällan. Du kan även manuellt välja **datakälla** under **egenskaper** och ange de parametrar som behövs för att komma åt data.

Online-datakällor som stöds är uppdelat i tabellen nedan. Den här tabellen sammanfattar också filformat som stöds och parametrar som används för att komma åt data.

> [!IMPORTANT]
> För närvarande den [importdata] [ import-data] och [exportera Data] [ export-data] moduler kan läsa och skriva data endast från Azure storage som skapats med klassiskt distributionsmodell. Med andra ord stöds den nya Azure Blob Storage-kontotyp som erbjuder en frekvent åtkomstnivå eller lågfrekvent åtkomstnivå inte ännu.
>
> I allmänhet bör alla Azure storage-konton som du kan ha skapat innan det här alternativet om tjänsten blev tillgängliga bör inte påverkas.
> Om du vill skapa ett nytt konto kan du välja **klassiska** för distributionen modell, eller använda Resource manager och välj **generella** snarare än **Blob-lagring** för  **Typ av konto**.
>
> Mer information finns i [Azure Blob Storage: Frekvent och lågfrekvent lagringsnivå](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Online-datakällor som stöds
Azure Machine Learning **importdata** modulen stöder följande datakällor:

| Datakälla | Beskrivning | Parametrar |
| --- | --- | --- |
| Webbadress till via HTTP |Läser data i fil med kommaavgränsade värden (CSV), tabbavgränsade värden (TVS), Attributrelation filformatet (ARFF) och Support Vector datorer (SVM lätt)-format, från valfri webbtjänst-URL som använder HTTP |<b>URL</b>: Anger det fullständiga namnet på filen, inklusive webbplatsens URL och filnamn, med alla tillägg. <br/><br/><b>Dataformatet</b>: Anger en av format som stöds: CSV, TVS, ARFF eller SVM ljus. Om data har en rubrikrad, används den tilldelas kolumnnamn. |
| Hadoop/HDFS |Läser data från distribuerad lagring i Hadoop. Du anger de data du vill genom att använda HiveQL, ett SQL-liknande frågespråk. HiveQL kan också användas för att sammanställa data och utföra filtrering innan du lägger till data till Studio. |<b>Hive databasfråga</b>: Anger Hive-fråga som används för att generera data.<br/><br/><b>HCatalog server URI </b> : Ange namnet på klustret i formatet  *&lt;klusternamnet&gt;. azurehdinsight.net.*<br/><br/><b>Hadoop-användarkontonamnet</b>: Anger Hadoop-användarkontonamnet används för att etablera klustret.<br/><br/><b>Hadoop lösenord</b> : Anger de autentiseringsuppgifter som används vid etableringen av klustret. Mer information finns i [skapa Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Platsen för utdata</b>: Anger om data lagras i ett Hadoop distributed file system (HDFS) eller i Azure. <br/><ul>Om du lagrar utdata för i HDFS kan du ange URI för HDFS-servern. (Glöm inte att använda HDInsight-klustrets namn utan prefixet HTTPS://). <br/><br/>Om du lagrar dina utdata i Azure måste du ange Azure storage-kontonamn, åtkomstnyckel för lagring och namnet på lagringsbehållaren.</ul> |
| SQL-databas |Läser data som lagras i en Azure SQL-databas eller i en SQL Server-databas som körs på virtuella Azure-datorer. |<b>Namn på databasserver</b>: Anger namnet på den server där databasen körs.<br/><ul>Ange servernamnet som genereras när det gäller Azure SQL Database. Har normalt formatet  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Vid en SQL-servern i en Azure virtuell dator anger *tcp:&lt;DNS-namn på virtuell dator&gt;, 1433*</ul><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Server användarkontonamn</b>: Anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Server lösenord</b>: Anger lösenordet för användarkontot.<br/><br/><b>Databasfråga</b>: Ange ett SQL-uttryck som beskriver de data som du vill läsa. |
| Lokal SQL-databas |Läser data som lagras i en lokal SQL-databas. |<b>Datagateway</b>: Anger namnet på Data Management Gateway installerad på en dator där den kan komma åt SQL Server-databasen. Information om hur du konfigurerar gatewayen finns i [utför avancerad analys med Azure Machine Learning med hjälp av data från en lokal SQLServer](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Namn på databasserver</b>: Anger namnet på den server där databasen körs.<br/><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Server användarkontonamn</b>: Anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Användarnamn och lösenord</b>: Klicka på <b>ange värden</b> att ange dina Databasautentiseringsuppgifter. Du kan använda Windows-integrerad autentisering eller SQL Server-autentisering beroende på hur din lokala SQL Server är konfigurerat.<br/><br/><b>Databasfråga</b>: Ange ett SQL-uttryck som beskriver de data som du vill läsa. |
| Azure-tabell |Läser data från Table service i Azure Storage.<br/><br/>Om du har läst stora mängder data används sällan använda Azure Table Service. Det ger en flexibel, icke-relationella (NoSQL), mycket skalbar, prisvärd och mycket tillgängliga lagringslösning. |Alternativen i den **importdata** ändras beroende på om du får åtkomst till offentlig information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha värdet för ”PublicOrSAS” eller ”konto”, som har en egen uppsättning parametrar. <br/><br/><b>Offentlig eller delad Åtkomstsignatur (SAS) URI</b>: Parametrarna är:<br/><br/><ul><b>Tabellen URI</b>: Anger offentlig eller SAS-Webbadressen för tabellen.<br/><br/><b>Anger vilka rader som ska söka efter egenskapsnamn</b>: Värdena som är <i>TopN</i> söker igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen. <br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett tal för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/></ul><b>Privat Lagringskonto</b>: Parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller tabellen att läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringskontots åtkomstnyckel som är associerade med kontot.<br/><br/><b>Tabellnamnet</b> : Anger namnet på den tabell som innehåller data att läsa.<br/><br/><b>Rader som ska söka efter egenskapsnamn</b>: Värdena som är <i>TopN</i> söker igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen.<br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett tal för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/> |
| Azure Blob Storage |Läser data som lagras i Blob service i Azure Storage, inklusive bilder, ostrukturerad text eller binära data.<br/><br/>Du kan använda Blob-tjänsten att exponera data offentligt eller lagra programdata privat. Du kan komma åt dina data var som helst genom att använda HTTP eller HTTPS-anslutningar. |Alternativen i den **importdata** modul ändras beroende på om du får åtkomst till offentlig information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha ett värde för ”PublicOrSAS” eller ”konto”.<br/><br/><b>Offentlig eller delad Åtkomstsignatur (SAS) URI</b>: Parametrarna är:<br/><br/><ul><b>URI: N</b>: Anger offentlig eller SAS-Webbadress för blob storage.<br/><br/><b>Filformat för</b>: Anger formatet för data i Blob-tjänsten. Format som stöds är CSV, TVS och ARFF.<br/><br/></ul><b>Privat Lagringskonto</b>: Parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller den blob som du vill läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringskontots åtkomstnyckel som är associerade med kontot.<br/><br/><b>Sökvägen till behållaren, katalogen eller blob </b> : Anger namnet på bloben som innehåller data att läsa.<br/><br/><b>BLOB-filformatet</b>: Anger formatet för data i blob-tjänsten. Format som stöds är CSV, TVS, ARFF, CSV med en angiven kodning och Excel. <br/><br/><ul>Om formatet är CSV- eller TVS, måste du ange om filen innehåller en rubrikrad.<br/><br/>Du kan använda Excel-alternativet för att läsa data från Excel-arbetsböcker. I den <i>Excel dataformat</i> måste du ange om data är i ett Excel-kalkylbladsområde eller i en Excel-tabell. I den <i>Excel-blad eller inbäddade tabell </i>, anger du namnet på kalkylbladet eller tabellen som du vill läsa från.</ul><br/> |
| -Datafeedprovider |Läser data från en leverantör som stöds feed. För närvarande endast formatet Open Data Protocol (OData) stöds. |<b>Innehållstyp som data</b>: Anger OData-format.<br/><br/><b>Käll-URL</b>: Anger den fullständiga URL för datafeeden. <br/>Till exempel läser följande URL från exempeldatabasen: http://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importera från ett annat experiment

Det blir när du vill ta ett mellanliggande resultat från ett experiment och använda den som en del av ett annat experiment. Om du vill göra detta måste spara du modulen som en datauppsättning:

1. Klicka på utdata från modulen som du vill spara som en datauppsättning.
2. Klicka på **Spara som datamängd**.
3. När du uppmanas, anger du ett namn och en beskrivning som du kan enkelt identifiera datauppsättningen.
4. Klicka på den **OK** bockmarkeringen.

När du är klar spara blir datauppsättningen tillgänglig för användning i alla experiment på arbetsytan. Du hittar den i den **sparade datauppsättningar** lista på modulpaletten.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure Machine Learning studio-webbtjänster som använder moduler för dataimport och dataexport](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
