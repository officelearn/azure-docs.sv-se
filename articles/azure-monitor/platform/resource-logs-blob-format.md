---
title: Förbereda för formatändring till Azure Monitor-resursloggar
description: Azure-resursloggar flyttades för att använda tilläggsblobbar den 1 november 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 001dfbc78c0027249143e933684523d47af383d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096775"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Förbereda för formatändring till Azure Monitor-plattformsloggar som arkiverats i ett lagringskonto

> [!WARNING]
> Om du skickar [Azure-resursloggar eller mått till ett lagringskonto med hjälp av diagnostikinställningar](resource-logs-collect-storage.md) eller [aktivitetsloggar till ett lagringskonto med hjälp av loggprofiler](resource-logs-collect-storage.md)ändrades formatet för data i lagringskontot till JSON-linjer den 1 november 2018. Instruktionerna nedan beskriver effekten och hur du uppdaterar verktygen för att hantera det nya formatet.
>

## <a name="what-changed"></a>Vad förändrades

Azure Monitor erbjuder en funktion som gör att du kan skicka resursloggar och aktivitetsloggar till ett Azure-lagringskonto, eventhubbars namnområde eller till en Log Analytics-arbetsyta i Azure Monitor. För att lösa ett systemprestandaproblem ändrades formatet för loggdata som skickas till blob storage den **1 november 2018 klockan 12:00 midnatt.** Om du har verktyg som läser data ur blob-lagring måste du uppdatera verktygen för att förstå det nya dataformatet.

* Torsdagen den 1 november 2018 klockan 12:00 midnatt UTC ändrades blob-formatet till [JSON Lines](http://jsonlines.org/). Det innebär att varje post avgränsas av en ny rad, utan ytterpostar och inga kommatecken mellan JSON-poster.
* Blob-formatet har ändrats för alla diagnostikinställningar för alla prenumerationer samtidigt. Den första PT1H.json filen som släpptes ut för November 1 används detta nya format. Blob- och behållarnamnen förblir desamma.
* Inställningen av en diagnostisk inställning mellan före den 1 november fortsatte att avge data i det aktuella formatet fram till den 1 november.
* Den här ändringen inträffade samtidigt i alla offentliga molnregioner. Ändringen kommer inte att ske i Microsoft Azure som drivs av 21Vianet,Azure Tyskland eller Azure Government moln ännu.
* Den här ändringen påverkar följande datatyper:
  * [Azure-resursloggar](archive-diagnostic-logs.md) ([se lista över resurser här](diagnostic-logs-schema.md))
  * [Azure-resursmått exporteras av diagnostikinställningar](diagnostic-settings.md)
  * [Azure Activity-loggdata exporteras av loggprofiler](activity-log-collect.md)
* Denna ändring påverkar inte:
  * Nätverksflödesloggar
  * Azure-tjänstloggar har ännu inte gjorts tillgängliga via Azure Monitor (till exempel Azure App Service-resursloggar, lagringsanalysloggar)
  * Routning av Azure-resursloggar och aktivitetsloggar till andra destinationer (eventhubbar, logganalys)

### <a name="how-to-see-if-you-are-impacted"></a>Så här ser du om du påverkas

Du påverkas bara av den här ändringen om du:
1. Skickar loggdata till ett Azure-lagringskonto med hjälp av en diagnostikinställning och
2. Har verktyg som beror på JSON-strukturen för dessa loggar i lager.
 
Om du vill identifiera om du har diagnostikinställningar som skickar data till ett Azure-lagringskonto kan du navigera till avsnittet **Övervakare** på portalen, klicka på **Diagnostikinställningar**och identifiera alla resurser som har **diagnostikstatus** inställd **på Aktiverad:**

![Blad för diagnostikinställningar för Azure Monitor](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Om diagnostikstatus är inställd på aktiverad har du en aktiv diagnostikinställning på den resursen. Klicka på resursen för att se om några diagnostikinställningar skickar data till ett lagringskonto:

![Lagringskonto aktiverat](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Om du har resurser som skickar data till ett lagringskonto med hjälp av dessa resursdiagnostikinställningar påverkas formatet för data i det lagringskontot av den här ändringen. Om du inte har anpassade verktyg som fungerar utanför dessa lagringskonton påverkar inte formatändringen dig.

### <a name="details-of-the-format-change"></a>Information om formatändringen

Det aktuella formatet för FILEN PT1H.json i Azure blob storage använder en JSON-matris med poster. Här är ett exempel på en KeyVault loggfil nu:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Det nya formatet använder [JSON-rader](http://jsonlines.org/), där varje händelse är en rad och det nya radtecknet anger en ny händelse. Här är vad ovanstående prov kommer att se ut i PT1H.json filen efter ändringen:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Med det här nya formatet kan Azure Monitor skicka loggfiler med [tilläggsblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), som är mer effektiva för att kontinuerligt lägga till nya händelsedata.

## <a name="how-to-update"></a>Så här uppdaterar du

Du behöver bara göra uppdateringar om du har anpassade verktyg som intar dessa loggfiler för vidare bearbetning. Om du använder en extern logganalys eller SIEM-verktyg rekommenderar vi [att du använder händelsehubbar för att använda dessa data i stället](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integrering av händelsehubbar är enklare när det gäller bearbetning av loggar från många tjänster och bokmärksplats i en viss logg.

Anpassade verktyg bör uppdateras för att hantera både det aktuella formatet och JSON Lines-formatet som beskrivs ovan. Detta säkerställer att när data börjar visas i det nya formatet går inte verktygen sönder.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [arkivering av resursresursloggar till ett lagringskonto](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Lär dig mer om [arkivering av aktivitetsloggdata till ett lagringskonto](./../../azure-monitor/platform/archive-activity-log.md)

