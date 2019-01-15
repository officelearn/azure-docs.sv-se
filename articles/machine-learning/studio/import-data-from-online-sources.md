---
Rubrik: Importera data till Machine Learning Studio från online-datakällor titleSuffix: Beskrivning av Azure Machine Learning Studio: Den här artikeln beskrivs stödet för import av online-data från olika källor och den information som krävs för att flytta data från dessa källor till en Azure Machine Learning Studio experimentera.
tjänster: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 11/29/2017
---
# <a name="import-data-into-azure-machine-learning-studio-from-online-data-sources"></a>Importera data till Azure Machine Learning Studio från online-datakällor 
Den här artikeln beskrivs stödet för import av online-data från olika källor och den information som krävs för att flytta data från dessa källor till en Azure Machine Learning Studio experimentera.

> [!NOTE]
> Den här artikeln innehåller allmän information om den [importdata] [ import-data] modulen. Mer detaljerad information om vilka typer av data som du kan komma åt format, parametrar och svar på vanliga frågor finns i avsnittet med modulen referens för den [importdata] [ import-data] modulen.
> 
> 

## <a name="introduction"></a>Introduktion
Med hjälp av den [importdata] [ import-data] modulen, du kan komma åt data från en av flera datakällor som online när experimentet körs i [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* En URL med HTTP
* Hadoop med HiveQL
* Azure blobblagring
* Azure-tabell
* Azure SQL database eller SQL Server på Azure VM
* En lokal SQL Server-databas
* En provider, för närvarande OData-datafeed
* Azure Cosmos DB

Du kommer åt online-datakällor i Studio-experiment genom att lägga till den [importera Data] [ import-data] modul till din, Välj den **datakälla**, och sedan ange de parametrar som behövs för att få åtkomst till data. Online-datakällor som stöds är uppdelat i tabellen nedan. Den här tabellen sammanfattar också filformat som stöds och parametrar som används för att komma åt data.

Observera att eftersom den här träningsdata hämtas när experimentet körs, det är bara tillgängliga i försöket. Jämförelsevis har är data som lagrats i en datauppsättning till modulen tillgängliga för alla experiment på arbetsytan.

> [!IMPORTANT]
> För närvarande den [importdata] [ import-data] och [exportera Data] [ export-data] moduler kan läsa och skriva data endast från Azure storage som skapats med klassiskt distributionsmodell. Med andra ord stöds den nya Azure Blob Storage-kontotyp som erbjuder en frekvent åtkomstnivå eller lågfrekvent åtkomstnivå inte ännu. 
> 
> I allmänhet bör alla Azure storage-konton som du kan ha skapat innan det här alternativet om tjänsten blev tillgängliga bör inte påverkas. 
> Om du vill skapa ett nytt konto kan du välja **klassiska** för distributionen modell, eller använda Resource manager och välj **generella** snarare än **Blob-lagring** för  **Typ av konto**. 
> 
> Mer information finns i [Azure Blob Storage: Frekvent och lågfrekvent lagringsnivå](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Online-datakällor som stöds
Azure Machine Learning **importdata** modulen stöder följande datakällor:

| Datakälla | Beskrivning | Parametrar |
| --- | --- | --- |
| Webbadress till via HTTP |Läser data i fil med kommaavgränsade värden (CSV), tabbavgränsade värden (TVS), Attributrelation filformatet (ARFF) och Support Vector datorer (SVM lätt)-format, från valfri webbtjänst-URL som använder HTTP |<b>URL</b>: Anger det fullständiga namnet på filen, inklusive webbplatsens URL och filnamn, med alla tillägg. <br/><br/><b>Dataformatet</b>: Anger en av format som stöds: CSV, TVS, ARFF eller SVM ljus. Om data har en rubrikrad, används den tilldelas kolumnnamn. |
| Hadoop/HDFS |Läser data från distribuerad lagring i Hadoop. Du anger de data du vill genom att använda HiveQL, ett SQL-liknande frågespråk. HiveQL kan också användas för att sammanställa data och utföra filtrering innan du lägger till data till Machine Learning Studio. |<b>Hive databasfråga</b>: Anger Hive-fråga som används för att generera data.<br/><br/><b>HCatalog server URI </b> : Ange namnet på klustret i formatet  *&lt;klusternamnet&gt;. azurehdinsight.net.*<br/><br/><b>Hadoop-användarkontonamnet</b>: Anger Hadoop-användarkontonamnet används för att etablera klustret.<br/><br/><b>Hadoop lösenord</b> : Anger de autentiseringsuppgifter som används vid etableringen av klustret. Mer information finns i [skapa Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Platsen för utdata</b>: Anger om data lagras i ett Hadoop distributed file system (HDFS) eller i Azure. <br/><ul>Om du lagrar utdata för i HDFS kan du ange URI för HDFS-servern. (Glöm inte att använda HDInsight-klustrets namn utan prefixet HTTPS://). <br/><br/>Om du lagrar dina utdata i Azure måste du ange Azure storage-kontonamn, åtkomstnyckel för lagring och namnet på lagringsbehållaren.</ul> |
| SQL-databas |Läser data som lagras i en Azure SQL-databas eller i en SQL Server-databas som körs på virtuella Azure-datorer. |<b>Namn på databasserver</b>: Anger namnet på den server där databasen körs.<br/><ul>Ange servernamnet som genereras när det gäller Azure SQL Database. Har normalt formatet  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Vid en SQL-servern i en Azure virtuell dator anger *tcp:&lt;DNS-namn på virtuell dator&gt;, 1433*</ul><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Server användarkontonamn</b>: Anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Server lösenord</b>: Anger lösenordet för användarkontot.<br/><br/><b>Databasfråga</b>: Ange ett SQL-uttryck som beskriver de data som du vill läsa. |
| Lokal SQL-databas |Läser data som lagras i en lokal SQL-databas. |<b>Datagateway</b>: Anger namnet på Data Management Gateway installerad på en dator där den kan komma åt SQL Server-databasen. Information om hur du konfigurerar gatewayen finns i [utför avancerad analys med Azure Machine Learning med hjälp av data från en lokal SQLServer](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Namn på databasserver</b>: Anger namnet på den server där databasen körs.<br/><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Server användarkontonamn</b>: Anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Användarnamn och lösenord</b>: Klicka på <b>ange värden</b> att ange dina Databasautentiseringsuppgifter. Du kan använda Windows-integrerad autentisering eller SQL Server-autentisering beroende på hur din lokala SQL Server är konfigurerat.<br/><br/><b>Databasfråga</b>: Ange ett SQL-uttryck som beskriver de data som du vill läsa. |
| Azure-tabell |Läser data från Table service i Azure Storage.<br/><br/>Om du har läst stora mängder data används sällan använda Azure Table Service. Det ger en flexibel, icke-relationella (NoSQL), mycket skalbar, prisvärd och mycket tillgängliga lagringslösning. |Alternativen i den **importdata** ändras beroende på om du får åtkomst till offentlig information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha värdet för ”PublicOrSAS” eller ”konto”, som har en egen uppsättning parametrar. <br/><br/><b>Offentlig eller delad Åtkomstsignatur (SAS) URI</b>: Parametrarna är:<br/><br/><ul><b>Tabellen URI</b>: Anger offentlig eller SAS-Webbadressen för tabellen.<br/><br/><b>Anger vilka rader som ska söka efter egenskapsnamn</b>: Värdena som är <i>TopN</i> söker igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen. <br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett tal för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/></ul><b>Privat Lagringskonto</b>: Parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller tabellen att läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringskontots åtkomstnyckel som är associerade med kontot.<br/><br/><b>Tabellnamnet</b> : Anger namnet på den tabell som innehåller data att läsa.<br/><br/><b>Rader som ska söka efter egenskapsnamn</b>: Värdena som är <i>TopN</i> söker igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen.<br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett tal för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/> |
| Azure Blob Storage |Läser data som lagras i Blob service i Azure Storage, inklusive bilder, ostrukturerad text eller binära data.<br/><br/>Du kan använda Blob-tjänsten att exponera data offentligt eller lagra programdata privat. Du kan komma åt dina data var som helst genom att använda HTTP eller HTTPS-anslutningar. |Alternativen i den **importdata** modul ändras beroende på om du får åtkomst till offentlig information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha ett värde för ”PublicOrSAS” eller ”konto”.<br/><br/><b>Offentlig eller delad Åtkomstsignatur (SAS) URI</b>: Parametrarna är:<br/><br/><ul><b>URI: N</b>: Anger offentlig eller SAS-Webbadress för blob storage.<br/><br/><b>Filformat för</b>: Anger formatet för data i Blob-tjänsten. Format som stöds är CSV, TVS och ARFF.<br/><br/></ul><b>Privat Lagringskonto</b>: Parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller den blob som du vill läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringskontots åtkomstnyckel som är associerade med kontot.<br/><br/><b>Sökvägen till behållaren, katalogen eller blob </b> : Anger namnet på bloben som innehåller data att läsa.<br/><br/><b>BLOB-filformatet</b>: Anger formatet för data i blob-tjänsten. Format som stöds är CSV, TVS, ARFF, CSV med en angiven kodning och Excel. <br/><br/><ul>Om formatet är CSV- eller TVS, måste du ange om filen innehåller en rubrikrad.<br/><br/>Du kan använda Excel-alternativet för att läsa data från Excel-arbetsböcker. I den <i>Excel dataformat</i> måste du ange om data är i ett Excel-kalkylbladsområde eller i en Excel-tabell. I den <i>Excel-blad eller inbäddade tabell </i>, anger du namnet på kalkylbladet eller tabellen som du vill läsa från.</ul><br/> |
| -Datafeedprovider |Läser data från en leverantör som stöds feed. För närvarande endast formatet Open Data Protocol (OData) stöds. |<b>Innehållstyp som data</b>: Anger OData-format.<br/><br/><b>Käll-URL</b>: Anger den fullständiga URL för datafeeden. <br/>Till exempel läser följande URL från exempeldatabasen: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure ML-webbtjänster som använder moduler för dataimport och dataexport](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
