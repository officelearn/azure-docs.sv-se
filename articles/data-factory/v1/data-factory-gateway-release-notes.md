---
title: Viktig information för Data Management Gateway | Microsoft Docs
description: Data Management Gateway tory viktig information
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ac0e1945e75ee7aea346c103a671b4a47b9e5994
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048622"
---
# <a name="release-notes-for-data-management-gateway"></a>Viktig information för gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [egenvärdbaserat integrering körning i V2](../create-self-hosted-integration-runtime.md).

En av utmaningarna för moderna dataintegrering är att flytta data till och från lokalt till molnet. Data Factory gör den här integreringen med Data Management Gateway, vilket är en agent som att du kan installera lokalt för att aktivera hybrid dataflyttning.

Se följande artiklar för detaljerad information om Data Management Gateway och hur du använder den:

*  [Gateway för datahantering](data-factory-data-management-gateway.md)
*  [Flytta data mellan lokala och molnet med Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUELL VERSION 
Inga fler upprätthåller vi i Viktigt.txt. Hämta senaste viktig information [här](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Tidigare versioner
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Förbättringar-
- Du kan lägga till DNS-poster godkända service bus i stället för vitlistning av alla Azure-IP-adresser från din brandvägg (vid behov). Du hittar motsvarande DNS-post på Azure-portalen (Data Factory -> 'Författare och distribution' -> 'Gateways' -> ”serviceUrls” (i JSON)
- HDFS connector stöder nu självsignerat certifikat med offentlig genom att du kan hoppa över validering av SSL.
- Fast: Problem med gateway offline under uppdateringen (på grund av klockavvikelser)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Förbättringar-
-   Du kan lägga till DNS-poster godkända Service Bus i stället för vitlistning av alla Azure-IP-adresser från din brandvägg (vid behov). Mer information här.
-   Nu kan du kopieringsdata till och från ett enda block blob upp till 4,75 TB, vilket är den största storleken som stöds av blockblob. (tidigare gränsen var 195 GB).
-   Fast: Slut på minne problemet när man öppnar flera mindre filer under kopieringsaktiviteten.
-   Fast: Indexet ligger utanför intervallet problem vid kopiering från dokumentet DB till en lokal SQL Server med idempotens funktionen.
-   Fast: SQL-skriptet för rensning av fungerar inte med lokala SQL Server från guiden Kopiera.
-   Fast: Kolumnnamnet med utrymme i slutet fungerar inte i en Kopieringsaktivitet.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Förbättringar-
- Fast: Problem med saknas autentiseringsuppgifter för gateway-datorn startas om.
- Fast: Problem med registreringen under gateway återställa en säkerhetskopia.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Förbättringar-
- Fast: Felaktig läsning av null decimalvärde från Oracle som källa.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Nyheter
- Kunder kan ge feedback på gateway registrerar upplevelse.
- Stöd för ett nytt komprimeringsformat för: ZIP (Deflate)

### <a name="enhancements-"></a>Förbättringar-
- Förbättring av prestanda för Oracle Sink HDFS källa.
- Buggfix för gateway automatiskt uppdatera gateway parallell bearbetning av kapacitet.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Förbättringar
- Bättre och mer robust Gateway registrering experience – nu kan du spåra förloppsstatus under registreringen Gateway, vilket gör registreringen uppleva snabbare.
- Förbättring i Gateway återställa Process - du kan fortfarande återställa gateway, även om du inte har den säkerhetskopiera filen gateway med den här uppdateringen. Detta kräver du återställa länkade tjänsten autentiseringsuppgifter i portalen.
- Buggfix.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Nyheter

- Nu kan du lagra autentiseringsuppgifterna för datakällan lokalt. Autentiseringsuppgifterna krypteras. Autentiseringsuppgifterna för datakällan kan återställas och återställas med hjälp av säkerhetskopian som kan exporteras från den befintliga gatewayen, alla lokala.

### <a name="enhancements-"></a>Förbättringar-

- Bättre och mer robust Gateway registrering upplevelse.
- Stöd för automatisk identifiering av QuoteChar konfiguration för textformat i guiden Kopiera och förbättra övergripande format identifiering.

## <a name="2361002"></a>2.3.6100.2

- Stöd för firstRowAsHeader och automatisk identifiering av SkipLineCount i guiden Kopiera textfiler i lokala filsystemet och HDFS.
- Förbättra stabiliteten för nätverksanslutningen mellan gateway och Service Bus
- Några buggkorrigeringar


## <a name="2260721"></a>2.2.6072.1

*  Har stöd för inställningen HTTP-proxy för gatewayen med Konfigurationshanteraren för Gateway. Om konfigurerad, kan Azure Blob, Azure Table, Azure Data Lake och dokumentet DB nås via HTTP-proxy.
*  Har stöd för huvudet hantering för TextFormat vid kopiering av data från/till Azure Blob Azure Data Lake Store lokalt filsystem, och lokala HDFS.
*  Kopiera data från Lägg till Blob och Sidblob tillsammans med redan stöds Blockblob stöder.
*  Introducerar en ny gateway status **Online (begränsat)**, vilket tyder på att de flesta funktioner för gatewayen fungerar utom interaktiva åtgärden stöd för guiden Kopiera.
*  Förbättrar gateway-registrering med registreringsnyckel stabilitet.

## <a name="216040"></a>2.1.6040.

*  DB2 drivrutin ingår nu i gateway-installationspaketet. Du behöver inte installera det separat.
*  DB2-drivrutinen stöder nu z/OS och DB2 för jag (AS / 400) tillsammans med de plattformar som redan stöds (Linux, Unix- och Windows).
*  Stöder Azure Cosmos DB som källa eller mål för lokala datalager
*  Har stöd för kopiering av data från/till kall/frekvent blob-lagring tillsammans med redan stöds Allmänt lagringskonto.
*  Kan du ansluta till lokal SQL Server via en gateway med behörighet för fjärrinloggning.  

## <a name="2060131"></a>2.0.6013.1

*  Du kan välja språkkulturen som ska användas av en gateway vid manuell installation.

*  När gatewayen inte fungerar som förväntat, kan du välja att skicka gateway-loggarna för senaste sju dagarna till Microsoft för att underlätta felsökning av problemet. Om gateway inte är ansluten till Molntjänsten kan du välja att spara och arkivera gateway-loggarna.  

*  Användargränssnittet för gateway configuration manager:

    *  Se gatewayens status tydligare på fliken Start.

    *  Organiseras och förenklad kontroller.

    *  Du kan kopiera data från en lagring med hjälp av den [kod utan Kopiera verktyget](data-factory-copy-data-wizard-tutorial.md). Se [mellanlagrad kopiera](data-factory-copy-activity-performance.md#staged-copy) mer information om den här funktionen i allmänhet.
*  Du kan använda Data Management Gateway till ingång data direkt från en lokal SQL Server-databas i Azure Machine Learning.

*  Prestandaförbättringar

    * Förbättra prestanda om hur du visar Schema/Förhandsgranska mot SQL Server i koden utan kopiera verktyg.

## <a name="11259531"></a>1.12.5953.1

*  Felkorrigeringar

## <a name="11159181"></a>1.11.5918.1

*  Maximal storlek för gateway-händelseloggen har ökat från 1 MB till 40 MB.

*  Ett varningsmeddelande visas om en omstart krävs under gateway automatisk uppdatering. Du kan välja att starta om normalt sedan eller senare.

*  Om automatisk uppdatering misslyckas, försöker installationsprogrammet för gateway automatisk uppdatering tre gånger på maximalt.

*  Prestandaförbättringar

    * Förbättra prestanda för inläsning av stora tabeller från lokala servern i kod-fri kopiering scenario.

*  Felkorrigeringar

## <a name="11058921"></a>1.10.5892.1

*  Prestandaförbättringar

*  Felkorrigeringar

## <a name="1958652"></a>1.9.5865.2

*  Funktionen noll touch automatisk uppdatering
*  Ny ikon med indikatorer för gateway-status
*  Möjligheten att ”uppdatera nu” från klienten
*  Möjlighet att ställa in schemat Uppdateringstid
*  PowerShell-skript för att använda automatisk uppdatering av/på
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

*  Stöd för lokala HDFS källor/mottagare
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656961"></a>1.6.5696.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656761"></a>1.6.5676.1

*  Diagnostiska supportverktyg på Configuration Manager
*  Stöd för tabellkolumnerna tabelldata källor för Azure Data Factory
*  SQL DW-stöd för Azure Data Factory
*  Stöd för Reclusive i BlobSource och FileSource för Azure Data Factory
*  Stöd för CopyBehavior – MergeFiles, PreserveHierarchy och FlattenHierarchy i BlobSink och FileSink med binära kopia för Azure Data Factory
*  Stöd för Kopieringsaktiviteten statusrapportering för Azure Data Factory
*  Stöd för källa anslutningen dataverifiering för Azure Data Factory
*  Felkorrigeringar

### <a name="1656721"></a>1.6.5672.1

*  Tabellnamnet för ODBC-datakällan har stöd för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656581"></a>1.6.5658.1

*  Stöd för filen Sink för Azure Data Factory
*  Stöd för bevara hierarki i binär kopia för Azure Data Factory
*  Kopiera aktivitet idempotens har stöd för Azure Data Factory
*  Felkorrigeringar

### <a name="1656401"></a>1.6.5640.1

*  Stöd för 3 fler datakällor för Azure Data Factory (ODBC, OData, HDFS)
*  Citattecken i csv-parser stöd för Azure Data Factory
*  Komprimering support (BZip2)
*  Felkorrigeringar

### <a name="1556121"></a>1.5.5612.1

*  Stöd för fem relationsdatabaser för Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata och Sybase)
*  Komprimering support (Gzip och Deflate)
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1455491"></a>1.4.5549.1

*  Lägga till stöd för Oracle datakällan för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1454921"></a>1.4.5492.1

*  Enhetlig binary som har stöd för både Microsoft Azure Data Factory och Power BI för Office 365-tjänster
*  Förfina processen användargränssnitt och registrering
*  Azure Data Factory – Azure ingående och utgående stöd för SQL Server-datakälla

### <a name="1253031"></a>1.2.5303.1

*  Åtgärda problemet tidsgränsen för att stödja mer tidskrävande datakällanslutningar.

### <a name="1155268"></a>1.1.5526.8

*  Kräver .NET Framework 4.5.1 som en förutsättning under installationen.

### <a name="1051442"></a>1.0.5144.2

*  Inga ändringar som påverkar Azure Data Factory-scenarier.
