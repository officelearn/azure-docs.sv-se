---
title: Viktig information för Gateway för datahantering | Microsoft Docs
description: Data Management Gateway tory viktig information
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 865bfdae199bca7ebee888be527db239d34511d1
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017624"
---
# <a name="release-notes-for-data-management-gateway"></a>Viktig information för gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [integration runtime i V2 med egen värd](../create-self-hosted-integration-runtime.md).

En av utmaningarna med integrering av moderna är att flytta data till och från lokal plats till molnet. Data Factory gör den här integreringen med Data Management Gateway, vilket är en agent som du installerar lokalt för att aktivera hybriddataförflyttning.

Se följande artiklar för detaljerad information om Gateway för datahantering och hur du använder det:

*  [Gateway för datahantering](data-factory-data-management-gateway.md)
*  [Flytta data mellan lokala och molnbaserade med Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUELL VERSION 
Vi behåller inga fler viktig information här. Hämta senaste versionsanteckningarna [här](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Tidigare versioner
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Förbättringar av –
- Du kan lägga till DNS-poster till listan över godkända service bus i stället för lista över tillåtna alla Azure-IP-adresser från din brandvägg (vid behov). Du hittar motsvarande DNS-post på Azure-portalen (Data Factory -> ”författare och distribution” -> 'Gatewayer ”->” serviceurl: er ”(i JSON)
- HDFS-anslutningsappen stöder nu självsignerat offentligt certifikat kan du hoppa över SSL-verifiering.
- Åtgärdat: Problem med gateway offline under uppdateringen (på grund av klockan skeva)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Förbättringar av –
-   Du kan lägga till DNS-poster du godkänna Service Bus i stället för lista över tillåtna alla Azure-IP-adresser från din brandvägg (vid behov). Mer information här.
-   Du kan kopieringsdata till/från ett enda block blob upp till 4,75 TB, vilket är den maximala storleken som stöds för blockblob. (tidigare begränsningen var 195 GB).
-   Åtgärdat: Utanför minne problem vid uppackning flera små filer under kopieringsaktiviteten.
-   Åtgärdat: Indexet är utanför intervallet problem när du kopierar från Document DB till en lokal SQL Server med idempotens funktionen.
-   Åtgärdat: SQL-skriptet för rensning fungerar inte med en lokal SQL Server från guiden Kopiera.
-   Åtgärdat: Kolumnnamn med blanksteg i slutet fungerar inte i kopieringsaktiviteten.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Förbättringar av –
- Åtgärdat: Utfärda med saknade autentiseringsuppgifter för omstart av gateway-datorn.
- Åtgärdat: Problem med registrering under gateway återställa med hjälp av en säkerhetskopia.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Förbättringar av –
- Åtgärdat: Felaktig läsning av Decimal null-värde från Oracle som källa.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Nyheter
- Kunder kan ge feedback på gateway registrerar upplevelse.
- Stöd för ett nytt komprimeringsformat: ZIP (Deflate)

### <a name="enhancements-"></a>Förbättringar av –
- Prestandaförbättringar för Oracle mottagare, HDFS källa.
- Buggfix för gateway automatiskt uppdatera gateway parallell bearbetning av kapacitet.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Förbättringar
- Förbättrad och stabilare Gateway registrering upplevelse – nu kan du spåra förloppsstatus under registreringsprocessen Gateway, vilket gör registreringen Upplev mer responsiv.
- Förbättring av Gateway återställa processen – du kan fortfarande återställa gateway, även om du inte har den säkerhetskopiera filen gateway med den här uppdateringen. Det här kräver du återställa länkade autentiseringsuppgifter på portalen.
- Felkorrigering.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Nyheter

- Nu kan du lagra autentiseringsuppgifter för datakälla lokalt. Autentiseringsuppgifterna krypteras. Datakällans autentiseringsuppgifter kan återställas och återställas med hjälp av den säkerhetskopia som kan exporteras från den befintliga gatewayen, alla lokala.

### <a name="enhancements-"></a>Förbättringar av –

- Bättre och mer robust Gateway registrering upplevelse.
- Stöd för automatisk identifiering av QuoteChar konfiguration för textformat i Kopieringsguiden och förbättra övergripande format identifieringen.

## <a name="2361002"></a>2.3.6100.2

- Stöd för firstRowAsHeader och SkipLineCount Autoidentifiering i Kopieringsguiden för textfiler i lokala filsystem och HDFS.
- Förbättra stabiliteten för nätverksanslutningen mellan gateway och Service Bus
- Några felkorrigeringar


## <a name="2260721"></a>2.2.6072.1

*  Har stöd för inställningen HTTP-proxy för gatewayen med hjälp av Konfigurationshanteraren för Gateway. Om konfigurerat, kan Azure Blob, Azure Table, Azure Data Lake och Document DB nås via HTTP-proxy.
*  Har stöd för rubriken hantering för TextFormat när du kopierar data från/till Azure Blob, Azure Data Lake Store lokala filsystem och lokala HDFS.
*  Har stöd för kopiering av data från Lägg till Blob- och Page Blob tillsammans med redan stöds Blockblob.
*  Introducerar en ny gatewaystatus **Online (begränsat)**, vilket betyder att de flesta funktioner för gatewayen fungerar förutom interaktiva åtgärden stöd för guiden Kopiera.
*  Ger ökad stabilitet av gateway-registrering med hjälp av Registreringsnyckeln.

## <a name="216040"></a>2.1.6040.

*  DB2-drivrutinen ingår nu i gateway-installationspaketet. Du behöver inte installera det separat.
*  DB2-drivrutinen stöder nu z/OS och DB2 för jag (AS / 400) tillsammans med de plattformar som stöds redan (Linux, Unix- och Windows).
*  Stöder användning av Azure Cosmos DB som källa eller mål för den lokala datalager
*  Har stöd för kopiering av data från/till kall/hot blob-lagring tillsammans med det allmänna lagringskontot som redan stöds.
*  Kan du ansluta till en lokal SQL Server via en gateway med behörighet för fjärrinloggning.  

## <a name="2060131"></a>2.0.6013.1

*  Du kan välja det språk/den språkmiljö som ska användas av en gateway under manuell installation.

*  När gatewayen inte fungerar som förväntat, kan du skicka gatewayloggarna för senaste sju dagarna till Microsoft för att underlätta felsökning av problemet. Om gatewayen inte är ansluten till Molntjänsten kan välja du att spara och arkivera gateway-loggar.  

*  Användargränssnittet för gateway configuration manager:

    *  Kontrollera status för gateway tydligare på fliken Start.

    *  Organiseras och förenklad kontroller.

    *  Du kan kopiera data från en storage med hjälp av den [kodfria kopieringsverktyget](data-factory-copy-data-wizard-tutorial.md). Se [mellanlagrad kopiering](data-factory-copy-activity-performance.md#staged-copy) mer information om den här funktionen i allmänhet.
*  Du kan använda Data Management Gateway att inkommande data direkt från en lokal SQL Server-databas till Azure Machine Learning.

*  Prestandaförbättringar

    * Förbättra prestanda om hur du visar schemaversionen/Preview mot SQL Server i kodfria kopieringsverktyget.

## <a name="11259531"></a>1.12.5953.1

*  Felkorrigeringar

## <a name="11159181"></a>1.11.5918.1

*  Maximal storlek på gateway-händelseloggen har ökat från 1 MB till 40 MB.

*  Ett varningsmeddelande visas om en omstart krävs under gateway automatisk uppdatering. Du kan välja att starta om just sedan eller senare.

*  Om automatisk uppdatering misslyckas, försöker installationsprogrammet för gateway automatiskt uppdaterar tre gånger på maximalt.

*  Prestandaförbättringar

    * Förbättra prestanda för stora tabeller läses in från den lokala servern i kodfria kopia scenario.

*  Felkorrigeringar

## <a name="11058921"></a>1.10.5892.1

*  Prestandaförbättringar

*  Felkorrigeringar

## <a name="1958652"></a>1.9.5865.2

*  Noll touch automatisk uppdateringsfunktion
*  Ny ikon med indikatorer för gateway-status
*  Möjlighet att ”uppdatera nu” från klienten
*  Möjlighet att ange schema Uppdateringstid
*  PowerShell-skript för att växla mellan automatisk uppdatering på/av
*  Stöd för JSON-format  
*  Prestandaförbättringar
*  Felkorrigeringar

## <a name="1858221"></a>1.8.5822.1

*  Förbättra felsökning
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1757951"></a>1.7.5795.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1757641"></a>1.7.5764.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1657351"></a>1.6.5735.1

*  Stöd för lokala HDFS källa/mottagare
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656961"></a>1.6.5696.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656761"></a>1.6.5676.1

*  Diagnostiska verktyg för support på Configuration Manager
*  Stöd tabellkolumner för datakällor i tabellform för Azure Data Factory
*  Stöd för SQL DW för Azure Data Factory
*  Stöd för Reclusive i BlobSource och filkällan för Azure Data Factory
*  Stöd för CopyBehavior – MergeFiles och PreserveHierarchy FlattenHierarchy i BlobSink och FileSink med binär kopia för Azure Data Factory
*  Stöd för Kopieringsaktiviteten statusrapportering för Azure Data Factory
*  Stöd för källa anslutning dataverifiering för Azure Data Factory
*  Felkorrigeringar

### <a name="1656721"></a>1.6.5672.1

*  Stöd för tabellnamn för ODBC-datakälla för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656581"></a>1.6.5658.1

*  Tilläggsfil mottagare för Azure Data Factory
*  Stöd för preserving hierarki i binär kopia för Azure Data Factory
*  Kopiera aktivitet idempotens-stöd för Azure Data Factory
*  Felkorrigeringar

### <a name="1656401"></a>1.6.5640.1

*  Stöd för 3 fler datakällor för Azure Data Factory (ODBC, OData, HDFS)
*  Citattecken i csv-parsern support för Azure Data Factory
*  Komprimeringsstöd för (BZip2)
*  Felkorrigeringar

### <a name="1556121"></a>1.5.5612.1

*  Stöd för fem relationella databaser för Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata och Sybase)
*  Komprimering support (Gzip och Deflate)
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1455491"></a>1.4.5549.1

*  Lägg till stöd för Oracle datakällor för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1454921"></a>1.4.5492.1

*  Enhetlig binary som har stöd för både Microsoft Azure Data Factory och Power BI för Office 365-tjänster
*  Förfina processen Kopplingskonfigurationens användargränssnitt och registrering
*  Azure Data Factory – Azure ingående och utgående stöd för SQL Server-datakälla

### <a name="1253031"></a>1.2.5303.1

*  Åtgärda timeout-problem för att stödja mer tidskrävande datakällanslutningar.

### <a name="1155268"></a>1.1.5526.8

*  Kräver .NET Framework 4.5.1 som ett krav under installationen.

### <a name="1051442"></a>1.0.5144.2

*  Inga ändringar som påverkar Azure Data Factory-scenarier.
