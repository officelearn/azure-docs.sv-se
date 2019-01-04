---
title: Förbereda för formatändring av diagnostikloggar för Azure Monitor
description: Azure diagnostikloggar kommer att flyttas till använda tilläggsblobbar den 1 November 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: ba163b797cfaaf73c660f1ef3d8da18758e8139a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542007"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Förbereda för ändring av format till Azure Monitor arkiveras på ett lagringskonto för diagnostikloggar

> [!WARNING]
> Om du skickar [diagnostikloggar för Azure-resurs eller mått till ett lagringskonto med hjälp av resursdiagnostikinställningar](./../../azure-monitor/platform/archive-diagnostic-logs.md) eller [aktivitetsloggar till ett storage-konto med loggprofiler](./../../azure-monitor/platform/archive-activity-log.md), formatet för data i storage-konto ändras till JSON-rader från den 1 november 2018. Anvisningarna nedan beskriver effekten och hur du uppdaterar ditt verktyg för att hantera det nya formatet. 
>
> 

## <a name="what-is-changing"></a>Vad ändras

Azure Monitor erbjuder en funktion som gör det möjligt att skicka diagnostikdata för resursen och aktivitetsloggdata till ett Azure storage-konto, namnområde för Event Hubs eller till Log Analytics. För att lösa ett systemproblem av prestanda på **1 November 2018 vid midnatt UTC** formatet för log data skicka till blob storage kommer att ändras. Om du har verktyg som läser data från blob-lagring, måste du uppdatera ditt verktyg för att identifiera nya formatet.

* Blobbformatet ändras det torsdag, November 1 2018 vid midnatt UTC för att vara [JSON rader](http://jsonlines.org/). Det innebär att varje post ska vara avgränsade med en ny rad med inga yttre poster matris och inga kommatecken mellan JSON-poster.
* Blob-formatändringar för alla diagnostikinställningar för alla prenumerationer på samma gång. Den första PT1H.json-filen som genereras under den 1 November använder det nya formatet. Namnen på blob och behållare är desamma.
* Ange en diagnostikinställning från nu och den 1 November fortsätter att sända data i det aktuella formatet fram till den 1 November.
* Den här ändringen inträffar på en gång i alla regioner för offentliga moln. Ändringen inträffar inte ännu i Azure i Kina, Azure Tyskland och Azure Government-moln.
* Den här ändringen påverkar följande datatyper:
  * [Diagnostikloggar för Azure-resurs](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([finns i listan över resurser här](./../../azure-monitor/platform/tutorial-dashboards.md))
  * [Azure-Resursmått som exporteras av diagnostikinställningar](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [Azure aktivitetsloggdata som exporteras av loggprofiler](./../../azure-monitor/platform/archive-activity-log.md)
* Den här ändringen påverkar inte:
  * Flödesloggar för nätverk
  * Azure-tjänstloggar inte gjorts tillgängliga via Azure Monitor ännu (till exempel diagnostikloggar för Azure App Service, storage analytics-loggar)
  * Routning av Azure diagnostikloggar och aktivitetsloggar till andra mål (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Hur du ser om du påverkas

Du påverkas endast av den här ändringen om du:
1. Loggdata skickas till ett Azure storage-konto med hjälp av en resursdiagnostikinställning och
2. Har verktyg som är beroende av JSON-strukturen för de här loggarna i lagring.
 
Du kan se om du har resursdiagnostikinställningar som skickar data till ett Azure storage-konto som du kan navigera till den **övervakaren** avsnittet i portalen klickar du på **diagnostikinställningar**, och identifiera alla resurser som har **Diagnostikstatusen** inställd **aktiverad**:

![Bladet Azure Monitor diagnostikinställningar](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Om diagnostisk Status är aktiverad, har du en aktiv diagnostikinställning på den aktuella. Klicka på resursen för att se om några diagnostikinställningar skickar data till ett lagringskonto:

![Storage-konto som är aktiverad](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Om du har resurser som skickar data till ett lagringskonto med hjälp av dessa resursdiagnostikinställningar, kommer formatet för data i det lagringskontot att påverkas av den här ändringen. Om du inte har anpassat verktyg som körs på dessa konton, påverkar inte ändringen format du.

### <a name="details-of-the-format-change"></a>Information om ändringen format

Aktuella formatet på filen pt1h.JSON i Azure blob storage använder en JSON-matris med poster. Här är ett exempel på en loggfil för KeyVault nu:

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

Det nya formatet använder [JSON rader](http://jsonlines.org/), där varje händelse är en rad och tecknet för ny rad anger en ny händelse. Här är vad exemplet ovan ser ut i filen pt1h.JSON efter ändringen:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Detta nya format kan push-loggfiler med hjälp av Azure Monitor [tilläggsblobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), som är bäst för nya event data läggs till kontinuerligt.

## <a name="how-to-update"></a>Så här uppdaterar du

Du behöver bara göra uppdateringar om du har anpassade verktyg som matar in dessa loggfiler för vidare bearbetning. Om du gör användning av en extern för logganalys eller SIEM-verktyg, rekommenderar vi [med event hubs för att kunna hämta dessa data i stället](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Event hubs-integrering är enklare när det gäller bearbetning av loggar från flera olika tjänster och bokmärken plats i en viss logg.

Anpassade verktyg ska uppdateras för att hantera både det aktuella formatet och rader för JSON-formatet som beskrivs ovan. Det säkerställer att när data börjar visas i det nya formatet, dina verktyg inte skadar.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [arkivering resursdiagnostikloggar till ett lagringskonto](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Lär dig mer om [Arkivera aktivitetsloggdata till ett lagringskonto](./../../azure-monitor/platform/archive-activity-log.md)
