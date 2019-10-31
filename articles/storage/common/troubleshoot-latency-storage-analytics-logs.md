---
title: Felsök svars tider med hjälp av Lagringsanalys loggar
description: Identifiera och Felsök latens problem med Azure Storage analytiska loggar och optimera klient programmet.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f8ec8e3f27e74f442f314d4c812908888598244c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180633"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Felsök svars tider med hjälp av Lagringsanalys loggar

Diagnostisering och fel sökning är en viktig kunskap för att skapa och stödja klient program med Azure Storage.

På grund av den distribuerade typen av ett Azure-program kan det vara mer komplicerat än i traditionella miljöer att diagnostisera och felsöka både fel och prestanda problem.

Följande steg visar hur du kan identifiera och felsöka latens problem med hjälp av Azure Storage analytiska loggar och optimera klient programmet.

## <a name="recommended-steps"></a>Rekommenderade åtgärder

1. Ladda ned [Lagringsanalys loggar](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Använd följande PowerShell-skript för att konvertera RAW-formatet loggar till tabell format:

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

3. Skriptet startar ett GUI-fönster där du kan filtrera informationen efter kolumner, som du ser nedan.

   ![Fönstret lagrings analys logg tolkare](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Begränsa logg posterna baserat på "åtgärds typ" och leta efter logg posten som skapades under utfärdarens tidsram.

   ![Logg poster för åtgärds typ](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Under den tid då problemet uppstod är följande värden viktiga:

   * Åtgärds typ = GetBlob
   * Request-status = SASNetworkError
   * Slut punkt till slut punkt – i-MS = 8453
   * Server-svars tid – in-MS = 391

   Svars tid från slut punkt till slut punkt beräknas med hjälp av följande ekvation:

   * Svars tid från slut punkt till slut punkt = Server svars tid + klient svars tid

   Beräkna klient fördröjningen med hjälp av logg posten:

   * Klient svars tid = svars tid från slut punkt till slut punkt – Server-svars tid

          * Example: 8453 – 391 = 8062ms

   Följande tabell innehåller information om OperationType-och RequestStatus-resultaten med hög latens:

   |   |RequestStatus =<br>Lyckades|RequestStatus =<br>SÄKERHETS NetworkError|Rekommendation|
   |---|---|---|---|
   |GetBlob|Ja|Nej|[**GetBlob-åtgärd:** RequestStatus = lyckades](#getblob-operation-requeststatus--success)|
   |GetBlob|Nej|Ja|[**GetBlob-åtgärd:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Ja|Nej|[**Placerings åtgärd:** RequestStatus = lyckades](#put-operation-requeststatus--success)|
   |PutBlob|Nej|Ja|[**Placerings åtgärd:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Status resultat

### <a name="getblob-operation-requeststatus--success"></a>GetBlob-åtgärd: RequestStatus = lyckades

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-svars tid
* Klient svars tid

I en **GetBlob-åtgärd** med **RequestStatus = lyckades**, om den **maximala tiden** används i **klient svars**tiden, betyder det att Azure Storage kostar en stor mängd tid när data skrivs till klienten. Den här fördröjningen indikerar ett problem på klient sidan.

**Rekommenderade**

* Undersök koden i klienten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob-åtgärd: RequestStatus = (SAS) NetworkError

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-svars tid
* Klient svars tid

I en **GetBlob-åtgärd** med **REQUESTSTATUS = (SAS) NetworkError**, om den **maximala tiden** används i **klient svars**tiden, är det vanligaste problemet att klienten kopplas från innan en tids gräns går ut i lagrings tjänsten.

**Rekommenderade**

* Undersök koden i klienten för att förstå varför och när klienten kopplas från lagrings tjänsten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten. 

### <a name="put-operation-requeststatus--success"></a>Placerings åtgärd: RequestStatus = lyckades

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-svars tid
* Klient svars tid

I en **Placera-åtgärd** med **RequestStatus = lyckades**, om den **maximala tiden** används i **klient svars**tiden, indikerar detta att klienten tar längre tid att skicka data till Azure Storage. Den här fördröjningen indikerar ett problem på klient sidan.

**Rekommenderade**

* Undersök koden i klienten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Placerings åtgärd: RequestStatus = (SAS) NetworkError

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-svars tid
* Klient svars tid

I en **PutBlob-åtgärd** med **REQUESTSTATUS = (SAS) NetworkError**, om den **maximala tiden** används i **klient svars**tiden, är det vanligaste problemet att klienten kopplas från innan en tids gräns går ut i lagrings tjänsten.

**Rekommenderade**

* Undersök koden i klienten för att förstå varför och när klienten kopplas från lagrings tjänsten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten.

