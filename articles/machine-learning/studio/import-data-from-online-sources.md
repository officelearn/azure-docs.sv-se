---
title: "Importera data till Machine Learning Studio från online datakällor | Microsoft Docs"
description: "Hur du importerar utbildningsdata Azure Machine Learning Studio från olika källor för online."
keywords: "Importera data, dataformatet, datatyper, datakällor, träningsdata"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;garye
ms.openlocfilehash: f58bd7206bb19328635b9834ca81bcdf692882c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importera data till Azure Machine Learning Studio från olika datakällor online med modulen Importera data
Den här artikeln beskrivs stöd för import av online-data från olika källor och information som behövs för att flytta data från dessa källor till ett Azure Machine Learning-experiment.

> [!NOTE]
> Den här artikeln innehåller allmän information om den [importera Data] [ import-data] modul. Mer detaljerad information om vilka typer av data som du kan komma åt format, parametrar och svar på vanliga frågor finns i referensavsnittet för modulen för den [importera Data] [ import-data] modul.
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introduktion
Med hjälp av den [importera Data] [ import-data] modulen, du kan komma åt data från flera källor för online-data när experimentet körs i [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* En URL med HTTP
* Hadoop med HiveQL
* Azure-blobblagring
* Azure-tabellen
* Azure SQL database eller SQL Server på Azure VM
* Lokal SQL Server-databas
* En datafeed för närvarande OData-providern
* Azure CosmosDB (tidigare kallade DocumentDB)

För att komma åt online datakällor i experimentet Studio, lägger du till den [importera Data] [ import-data] modulen, Välj den **datakällan**, och sedan ange parametrar som behövs för att få åtkomst till data. Online datakällor som stöds är specificerade i tabellen nedan. Den här tabellen sammanfattas också filformat som stöds och parametrar som används för att komma åt data.

Observera att eftersom den här utbildningsdata används när experimentet är igång, det är bara tillgängliga i försöket. Jämförelse kan är data som har lagrats i en dataset-modul tillgängliga för alla experiment på arbetsytan.

> [!IMPORTANT]
> För närvarande den [importera Data] [ import-data] och [exportera Data] [ export-data] moduler kan läsa och skriva data endast från skapas med klassiskt Azure-lagring distributionsmodell. Med andra ord stöds den nya typen av Azure Blob Storage-konto som erbjuder en frekvent åtkomstnivå eller lågfrekvent åtkomstnivå inte ännu. 
> 
> I allmänhet alla Azure storage-konton som du har skapat innan det här alternativet om tjänsten blev tillgängliga påverkas inte. 
> Om du behöver skapa ett nytt konto, Välj **klassiska** för distributionen modell, eller använda Resource manager och välj **generella** snarare än **Blob storage** för  **Kontot kind**. 
> 
> Mer information finns i [Azure Blob Storage: frekvent och lågfrekvent lagringsnivåer](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Online datakällor som stöds
Azure Machine Learning **importera Data** modulen stöder följande datakällor:

| Datakälla | Beskrivning | Parametrar |
| --- | --- | --- |
| URL för Web via HTTP |Läser data i fil med kommaavgränsade värden (CSV), tabbavgränsade värden (TVS), Attributrelation filformatet (ARFF) och Support Vector datorer (SVM lätt)-format från varje webbtjänst-URL som använder HTTP |<b>URL: en</b>: Anger det fullständiga namnet på filen, inklusive URL: en plats och filnamn, med alla tillägg. <br/><br/><b>Dataformatet</b>: Anger en av data som stöds formaterar: CSV TVS, ARFF eller SVM ljus. Om data har en rubrikrad, används den tilldelas kolumnnamn. |
| Hadoop/HDFS |Läser data från distribuerade lagringsutrymmen i Hadoop. Anger du de data som du vill använda HiveQL, ett SQL-liknande frågespråk. HiveQL kan också användas för att sammanställa data och utföra filtrering innan du lägger till data i Machine Learning Studio. |<b>Hive databasfrågan</b>: Anger Hive-fråga som används för att generera data.<br/><br/><b>HCatalog server URI </b> : Ange namnet på klustret i formatet  *&lt;klusternamnet&gt;. azurehdinsight.net.*<br/><br/><b>Hadoop användarkontonamnet</b>: Anger Hadoop användarkontonamnet används för att etablera klustret.<br/><br/><b>Hadoop lösenord</b> : Anger de autentiseringsuppgifter som används vid etablering av klustret. Mer information finns i [skapa Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Platsen för utdata</b>: Anger om data lagras i en Hadoop distributed file system (HDFS) eller i Azure. <br/><ul>Om du lagrar utdata i HDFS ange HDFS-serverns URI. (Måste du använda HDInsight-klustrets namn utan prefixet HTTPS://). <br/><br/>Om du lagrar dina utdata i Azure måste du ange kontonamn för Azure storage, lagringsåtkomstnyckel och namnet på lagringsbehållaren.</ul> |
| SQL-databas |Läser data som lagras i en Azure SQL database eller i en SQL Server-databas som körs på en virtuell Azure-dator. |<b>Databasservernamnet</b>: Anger namnet på den server där databasen körs.<br/><ul>Ange namnet på servern som genereras vid Azure SQL Database. Har vanligtvis formuläret  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Vid en SQLServer finns på en virtuell Azure-dator anger *tcp:&lt;virtuella DNS-namnet&gt;, 1433*</ul><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Servern användarkontonamnet</b>: anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Serverlösenord</b>: Anger lösenordet för användarkontot.<br/><br/><b>Acceptera alla servercertifikat</b>: Använd det här alternativet (mindre säkert) om du vill hoppa över granska platscertifikatet innan du läser dina data.<br/><br/><b>Databasfrågan</b>: Ange en SQL-instruktion som beskriver de data som du vill läsa. |
| Lokal SQL-databas |Läser data som lagras i en lokal SQL-databas. |<b>Datagatewayen</b>: Anger namnet på Data Management Gateway installeras på en dator där den kan komma åt SQL Server-databasen. Information om hur du konfigurerar gatewayen finns [utför avancerade analyser med Azure Machine Learning med hjälp av data från en lokal SQLServer](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Databasservernamnet</b>: Anger namnet på den server där databasen körs.<br/><br/><b>Databasnamnet </b>: Anger namnet på databasen på servern. <br/><br/><b>Servern användarkontonamnet</b>: anger ett användarnamn för ett konto som har åtkomstbehörighet för databasen. <br/><br/><b>Användarnamn och lösenord</b>: Klicka på <b>ange värden</b> att ange dina Databasautentiseringsuppgifter. Du kan använda Windows-integrerad autentisering eller beroende på hur dina lokala SQL Server är konfigurerat för SQL Server-autentisering.<br/><br/><b>Databasfrågan</b>: Ange en SQL-instruktion som beskriver de data som du vill läsa. |
| Azure-tabellen |Läser data från tabelltjänsten i Azure Storage.<br/><br/>Om du läsa stora mängder data mer sällan använda tjänsten Azure-tabellen. Det ger en flexibel och icke-relationella (NoSQL), mycket skalbar, prisvärda och hög tillgänglighet lagringslösning. |Alternativen i den **importera Data** ändras beroende på om du ansluter till offentliga information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha värdet ”PublicOrSAS” eller ”konto”, som har en egen uppsättning parametrar. <br/><br/><b>Offentlig eller delad signatur åtkomst (SAS) URI</b>: parametrarna är:<br/><br/><ul><b>Tabell URI</b>: Anger den offentliga eller SAS-URL för tabellen.<br/><br/><b>Anger raderna för att söka efter egenskapsnamn</b>: värdena är <i>TopN</i> att söka igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen. <br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett nummer för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera, beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/></ul><b>Privata Lagringskonto</b>: parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller tabellen om du vill läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringsnyckeln som är associerat med kontot.<br/><br/><b>Tabellnamnet</b> : Anger namnet på den tabell som innehåller data som kan läsas.<br/><br/><b>Rader som ska genomsökas för egenskapsnamn</b>: värdena är <i>TopN</i> att söka igenom det angivna antalet rader, eller <i>ScanAll</i> att hämta alla rader i tabellen.<br/><br/>Om data är homogena och förutsägbara, rekommenderar vi att du väljer *TopN* och ange ett nummer för N. För stora tabeller kan detta resultera i snabbare läsning gånger.<br/><br/>Om data är strukturerade med uppsättningar med egenskaper som kan variera, beroende på djupet och position i tabellen, väljer du den *ScanAll* alternativet att genomsöka alla rader. Detta säkerställer integriteten hos dina resulterande egenskapen och metadata för konvertering.<br/><br/> |
| Azure Blob Storage |Läser data som lagras i Blob-tjänsten i Azure Storage, inklusive bilder, Ostrukturerade text eller binära data.<br/><br/>Du kan använda Blob-tjänsten att exponera data offentligt eller privat lagra programdata. Du kan komma åt dina data från var som helst genom att använda HTTP eller HTTPS-anslutningar. |Alternativen i den **importera Data** modul ändras beroende på om du ansluter till offentliga information eller en privat lagringskontot som kräver autentiseringsuppgifter för inloggning. Detta bestäms av den <b>autentiseringstyp</b> som kan ha ett värde för ”PublicOrSAS” eller ”konto”.<br/><br/><b>Offentlig eller delad signatur åtkomst (SAS) URI</b>: parametrarna är:<br/><br/><ul><b>URI</b>: Anger den offentliga eller SAS-URL för blob storage.<br/><br/><b>Filformat</b>: Anger formatet för data i Blob-tjänsten. Format som stöds är CSV, TVS och ARFF.<br/><br/></ul><b>Privata Lagringskonto</b>: parametrarna är: <br/><br/><ul><b>Kontonamn</b>: Anger namnet på det konto som innehåller blob som du vill läsa.<br/><br/><b>Kontonyckel</b>: Anger lagringsnyckeln som är associerat med kontot.<br/><br/><b>Sökvägen till behållaren, katalogen eller blob </b> : Anger namnet på blob som innehåller data att läsa.<br/><br/><b>BLOB-filformatet</b>: Anger formatet för data i blob-tjänsten. Data som stöds format är CSV, TVS, ARFF, CSV med angiven kodning och Excel. <br/><br/><ul>Om formatet är CSV- eller TVS, måste du ange om filen innehåller en rubrikrad.<br/><br/>Du kan använda Excel-alternativet för att läsa data från Excel-arbetsböcker. I den <i>Excel dataformat</i> alternativ, ange om data är i ett intervall för Excel-kalkylblad eller i en Excel-tabell. I den <i>Excel-kalkylblad eller inbäddad tabell </i>, anger du namnet på kalkylbladet eller tabellen som du vill läsa från.</ul><br/> |
| Data Feed Provider |Läser data från en feed provider som stöds. För närvarande stöds endast Open Data Protocol (OData)-formatet. |<b>Data innehållstyp</b>: Anger OData-format.<br/><br/><b>Käll-URL</b>: Anger den fullständiga URL för datafeeden. <br/>Till exempel följande URL läser från exempeldatabasen Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure ML-webbtjänster som använder Data importera och exportera Data moduler](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
