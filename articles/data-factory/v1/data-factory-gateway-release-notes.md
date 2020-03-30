---
title: Viktig information för gateway för datahantering
description: Viktig information om datahanteringsgateway
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 838e523f74a21c44958ddb6dc88e4dab3526d81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065000"
---
# <a name="release-notes-for-data-management-gateway"></a>Viktig information för gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [självvärderade integrationskörning i V2](../create-self-hosted-integration-runtime.md).

En av utmaningarna för modern dataintegrering är att flytta data till och från lokalt till moln. Data Factory gör den här integreringen med Data Management Gateway, som är en agent som du kan installera lokalt för att möjliggöra hybriddataförflyttning.

Mer information om Data Management Gateway finns i följande artiklar:

*  [Gateway för datahantering](data-factory-data-management-gateway.md)
*  [Flytta data mellan lokalt och molnet med Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUELL VERSION 
Vi inte mer upprätthålla Release anteckningar här. Få senaste versionsanteckningarna [här](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Tidigare versioner
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Förbättringar-
- Du kan lägga till DNS-poster för att vitlista servicebuss i stället för att vitlista alla Azure IP-adresser från brandväggen (om det behövs). Du hittar respektive DNS-post på Azure-portalen (Data Factory -> "Författare och distribuera" - > "Gateways" - > "serviceUrls" (i JSON)
- HDFS-anslutning stöder nu självsignerat offentligt certifikat genom att du kan hoppa över TLS-validering.
- Åtgärdat: Problem med gateway offline under uppdateringen (på grund av klocksnedställning)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Förbättringar-
-   Du kan lägga till DNS-poster för att vitlista Service Bus i stället för att vitlista alla Azure IP-adresser från brandväggen (om det behövs). Mer information finns här.
-   Du kan nu kopiera data till/från en enda blockblob upp till 4,75 TB, vilket är den maximala storleken på blockblobben. (tidigare gräns var 195 GB).
-   Åtgärdat: Problem med på minne medan du packar upp flera små filer under kopieringsaktiviteten.
-   Åtgärdat: Index utanför intervallet när du kopierar från Document DB till en lokal SQL Server med idempotency-funktion.
-   Åtgärdat: SQL-rensningsskript fungerar inte med lokal SQL Server från kopieringsguiden.
-   Åtgärdat: Kolumnnamnet med blanksteg i slutet fungerar inte i kopieringsaktivitet.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Förbättringar-
- Åtgärdat: Problem med saknade autentiseringsuppgifter vid omstart av gateway-datorn.
- Åtgärdat: Problem med registrering under återställning av gateway med hjälp av en säkerhetskopia.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Förbättringar-
- Rättad: Felaktig läsning av decimal null-värde från Oracle som källa.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Nyheter
- Kunder kan ge feedback om gateway registrering erfarenhet.
- Stöd för ett nytt komprimeringsformat: ZIP (Deflate)

### <a name="enhancements-"></a>Förbättringar-
- Prestandaförbättring för Oracle Sink, HDFS källa.
- Buggfix för automatisk uppdatering av gateway, parallell bearbetningskapacitet för gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Förbättringar 
- Förbättrad och mer robust gateway-registreringsupplevelse - Nu kan du spåra status under gatewayregistreringsprocessen, vilket gör registreringsupplevelsen mer lyhörd.
- Förbättring av gateway återställningsprocessen- Du kan fortfarande återställa gateway även om du inte har gateway backup-filen med den här uppdateringen. Detta kräver att du återställer autentiseringsuppgifter för länkade tjänster i Portalen.
- Bugg fix.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Nyheter

- Nu kan du lagra datakällautentiseringsuppgifter lokalt. Autentiseringsuppgifterna är krypterade. Autentiseringsuppgifterna för datakällan kan återställas och återställas med hjälp av säkerhetskopian som kan exporteras från den befintliga gatewayen, alla lokala.

### <a name="enhancements-"></a>Förbättringar-

- Förbättrad och mer robust gateway registrering erfarenhet.
- Stöd för automatisk identifiering av QuoteChar-konfiguration för textformat i kopieringsguiden och förbättra den övergripande formatidentifieringsnoggrannheten.

## <a name="2361002"></a>2.3.6100.2

- Stöd firstRowAsHeader och SkipLineCount automatisk identifiering i kopieringsguiden för textfiler i lokalt filsystem och HDFS.
- Förbättra stabiliteten i nätverksanslutningen mellan gateway och servicebuss
- Några buggfixar


## <a name="2260721"></a>2.2.6072.1

*  Stöder inställning av HTTP-proxy för gatewayen med hjälp av Konfigurationshanteraren för gateway. Om den är konfigurerad används Azure Blob, Azure Table, Azure Data Lake och Document DB via HTTP-proxy.
*  Stöder huvudhantering för TextFormat vid kopiering av data från/till Azure Blob, Azure Data Lake Store, lokalt filsystem och lokala HDFS.
*  Stöder kopiering av data från Lägg till Blob och Sidblob tillsammans med den redan blockerade Block Blob.
*  Introducerar en ny gatewaystatus **Online (Limited),** som anger att gatewayens huvudfunktioner fungerar förutom det interaktiva åtgärdsstödet för kopieringsguiden.
*  Förbättrar robustheten i gatewayregistrering med hjälp av registreringsnyckel.

## <a name="216040"></a>2.1.6040.

*  DB2-drivrutinen ingår i installationspaketet för gatewayen nu. Du behöver inte installera det separat.
*  DB2-drivrutinen stöder nu z/OS och DB2 för i (AS/400) tillsammans med de plattformar som redan stöds (Linux, Unix och Windows).
*  Stöder användning av Azure Cosmos DB som källa eller mål för lokala datalager
*  Stöder kopiering av data från/till kall/varm bloblagring tillsammans med det redan stödda allmänna lagringskontot.
*  Gör att du kan ansluta till lokal SQL Server via gateway med fjärrinloggningsbehörighet.  

## <a name="2060131"></a>2.0.6013.1

*  Du kan välja vilket språk/kultur som ska användas av en gateway under manuell installation.

*  När gatewayen inte fungerar som förväntat kan du välja att skicka gatewayloggar från de senaste sju dagarna till Microsoft för att underlätta felsökning av problemet. Om gatewayen inte är ansluten till molntjänsten kan du välja att spara och arkivera gatewayloggar.  

*  Förbättringar av användargränssnittet för konfigurationshanteraren för gateway:

    *  Gör gatewaystatus mer synlig på fliken Start.

    *  Omorganiserade och förenklade kontroller.

    *  Du kan kopiera data från ett lagringsutrymme med hjälp av [det kodfria kopieringsverktyget](data-factory-copy-data-wizard-tutorial.md). Se [Stegvis kopia](data-factory-copy-activity-performance.md#staged-copy) för information om den här funktionen i allmänhet.
*  Du kan använda Data Management Gateway för att skicka data direkt från en lokal SQL Server-databas till Azure Machine Learning.

*  Prestandaförbättringar

    * Förbättra prestanda när du visar Schema/Förhandsgranska mot SQL Server i kodfritt kopieringsverktyg.

## <a name="11259531"></a>1.12.5953.1

*  Felkorrigeringar

## <a name="11159181"></a>1.11.5918.1

*  Den maximala storleken på gatewayhändelseloggen har ökat från 1 MB till 40 MB.

*  En varningsdialogruta visas om en omstart behövs under automatisk uppdatering av gatewayen. Du kan välja att starta om direkt då eller senare.

*  Om automatisk uppdatering misslyckas, gateway installer försöker automatiskt uppdatera tre gånger som högst.

*  Prestandaförbättringar

    * Förbättra prestanda för inläsning av stora tabeller från lokal server i kodfritt kopieringsscenario.

*  Felkorrigeringar

## <a name="11058921"></a>1.10.5892.1

*  Prestandaförbättringar

*  Felkorrigeringar

## <a name="1958652"></a>1.9.5865.2

*  Kapacitet för automatisk uppdatering av noll tryck
*  Ikon för nytt fack med statusindikatorer för gateway
*  Möjlighet att "uppdatera nu" från klienten
*  Möjlighet att ställa in uppdateringsschematid
*  PowerShell-skript för att växla automatisk uppdatering på/av
*  Stöd för JSON-format  
*  Prestandaförbättringar
*  Felkorrigeringar

## <a name="1858221"></a>1.8.5822.1

*  Förbättra felsökningsupplevelsen
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1757951"></a>1.7.5795.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1757641"></a>1.7.5764.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1657351"></a>1.6.5735.1

*  Stöd för lokala HDFS Source/Sink
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656961"></a>1.6.5696.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656761"></a>1.6.5676.1

*  Stöd för diagnostikverktyg i Configuration Manager
*  Stödtabellkolumner för tabelldatakällor för Azure Data Factory
*  Stöd för SQL DW för Azure Data Factory
*  Stöd för tillbakadragna i BlobSource och FileSource för Azure Data Factory
*  Support CopyBehavior – MergeFiles, PreserveHierarchy, and FlattenHierarchy in BlobSink and FileSink with Binary Copy for Azure Data Factory
*  Status för rapportering av supportkopieringsaktivitet för Azure Data Factory
*  Stöd för validering av datakällanslutning för Azure Data Factory
*  Felkorrigeringar

### <a name="1656721"></a>1.6.5672.1

*  Supporttabellnamn för ODBC-datakälla för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656581"></a>1.6.5658.1

*  Stöd filmottagare för Azure Data Factory
*  Stöd för bevarande av hierarki i binär kopia för Azure Data Factory
*  Stöd för idempotens för kopieringsaktivitet för Azure Data Factory
*  Felkorrigeringar

### <a name="1656401"></a>1.6.5640.1

*  Stöd för ytterligare 3 datakällor för Azure Data Factory (ODBC, OData, HDFS)
*  Stödcitattecken i csv-parser för Azure Data Factory
*  Komprimeringsstöd (BZip2)
*  Felkorrigeringar

### <a name="1556121"></a>1.5.5612.1

*  Stöd för fem relationsdatabaser för Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata och Sybase)
*  Kompressionsstöd (Gzip och Deflate)
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1455491"></a>1.4.5549.1

*  Lägg till stöd för Oracles datakälla för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1454921"></a>1.4.5492.1

*  Enhetlig binär som stöder både Microsoft Azure Data Factory och Office 365 Power BI-tjänster
*  Förfina konfigurationsgränssnittet och registreringsprocessen
*  Azure Data Factory – Azure Ingress och Egress-stöd för SQL Server-datakälla

### <a name="1253031"></a>1.2.5303.1

*  Åtgärda timeout-problemet för att stödja mer tidskrävande datakällanslutningar.

### <a name="1155268"></a>1.1.5526.8

*  Kräver .NET Framework 4.5.1 som en förutsättning under installationen.

### <a name="1051442"></a>1.0.5144.2

*  Inga ändringar som påverkar Azure Data Factory-scenarier.
