---
title: Felsöka långa svarstider med hjälp av loggar i Lagringsanalys
description: Identifiera och felsöka svarstidsproblem med Azure Storage-analytiska loggar och optimera klientprogrammet.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196507"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Felsöka långa svarstider med hjälp av loggar i Lagringsanalys

Diagnostisera och felsöka är en viktig färdighet för att skapa och stödja klientprogram med Azure Storage.

På grund av den distribuerade karaktären av ett Azure-program kan det vara mer komplicerat att diagnostisera och felsöka både fel och prestandaproblem än i traditionella miljöer.

Följande steg visar hur du identifierar och felsöker svarstidsproblem med Azure Storage Analytic-loggar och optimerar klientprogrammet.

## <a name="recommended-steps"></a>Rekommenderade åtgärder

1. Ladda ned [lagringsanalysloggarna](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Använd följande PowerShell-skript för att konvertera råformatsloggarna till tabellformat:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Skriptet startar ett GUI-fönster där du kan filtrera informationen efter kolumner, som visas nedan.

   ![Fönstret Lagring analytisk loggtolkare](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Begränsa loggposterna baserat på "operationstyp" och leta efter den loggpost som skapades under problemets tidsram.

   ![Loggposter av åtgärdstyp](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Under den tid då problemet uppstod är följande värden viktiga:

   * Operation-typ = GetBlob
   * begäran-status = SASNetworkError
   * på slutet till slut-latens -In-Ms = 8453
   * Server-svarstid -In-Ms = 391

   Svarstiden från slutpunkt till slutpunkt beräknas med hjälp av följande ekvation:

   * Svarstid från till sluttid = Server-svarstid + klientsvarstid

   Beräkna klientsvarstiden med hjälp av loggposten:

   * Klientsvarstid = Svarstid från till – Server-svarstid

          * Example: 8453 – 391 = 8062ms

   I följande tabell finns information om resultaten för den höga svarstiden OperationType och RequestStatus:

   |   |RequestStatus=<br>Lyckades|RequestStatus=<br>Jag är mycket väl tillbaka i den här lyddorna. Nätverkare|Rekommendation|
   |---|---|---|---|
   |GetBlob (på andra et)|Ja|Inga|[**GetBlob Operation:** RequestStatus = Lyckad](#getblob-operation-requeststatus--success)|
   |GetBlob (på andra et)|Inga|Ja|[**GetBlob Operation:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob (1990)|Ja|Inga|[**Sätt Operation:** RequestStatus = Lyckad](#put-operation-requeststatus--success)|
   |PutBlob (1990)|Inga|Ja|[**Sätt Operation:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Statusresultat

### <a name="getblob-operation-requeststatus--success"></a>GetBlob-åtgärd: RequestStatus = Lyckad

Kontrollera följande värden som nämns i steg 5 i avsnittet "Rekommenderade steg":

* Svarstid från till på sluten tid
* Server-svarstid
* Svarstid för klient

I en **GetBlob-åtgärd** med **RequestStatus = Framgång**, om **maxtid** spenderas i **klient-svarstid**, indikerar detta att Azure Storage spenderar en stor mängd tidsskrivningsdata till klienten. Den här fördröjningen indikerar ett problem på klientsidan.

**Rekommendation:**

* Undersök koden i din klient.
* Använd Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob-åtgärd: RequestStatus = (SAS)NetworkError

Kontrollera följande värden som nämns i steg 5 i avsnittet "Rekommenderade steg":

* Svarstid från till på sluten tid
* Server-svarstid
* Svarstid för klient

I en **GetBlob-åtgärd** med **RequestStatus =(SAS)NetworkError**, om **maxtid** spenderas i **klient-svarstid**, är det vanligaste problemet att klienten kopplar från innan en timeout upphör att gälla i lagringstjänsten.

**Rekommendation:**

* Undersök koden i klienten för att förstå varför och när klienten kopplar från lagringstjänsten.
* Använd Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten. 

### <a name="put-operation-requeststatus--success"></a>Placera åtgärd: RequestStatus = Lyckad

Kontrollera följande värden som nämns i steg 5 i avsnittet "Rekommenderade steg":

* Svarstid från till på sluten tid
* Server-svarstid
* Svarstid för klient

I en **Put-åtgärd** med **RequestStatus = lyckades**, om **maxtid** spenderas i **klient-svarstid**, indikerar detta att klienten tar längre tid att skicka data till Azure Storage. Den här fördröjningen indikerar ett problem på klientsidan.

**Rekommendation:**

* Undersök koden i din klient.
* Använd Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Placera åtgärd: RequestStatus = (SAS)NetworkError

Kontrollera följande värden som nämns i steg 5 i avsnittet "Rekommenderade steg":

* Svarstid från till på sluten tid
* Server-svarstid
* Svarstid för klient

I en **PutBlob-åtgärd** med **RequestStatus =(SAS)NetworkError**, om **maxtid** spenderas i **klient-svarstid**, är det vanligaste problemet att klienten kopplar från innan en timeout upphör att gälla i lagringstjänsten.

**Rekommendation:**

* Undersök koden i klienten för att förstå varför och när klienten kopplar från lagringstjänsten.
* Använd Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten.

