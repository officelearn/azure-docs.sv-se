---
title: Kundägda lagrings konton för logg inmatning
description: Använd ditt eget lagrings konto för inmatning av loggdata till en Log Analytics arbets yta i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 58d6f98c87e37254e77bcc8dda1cdca6e608cafc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962680"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Kundägda lagrings konton för logg inmatning i Azure Monitor

Azure Monitor använder lagrings konton i inmatnings processen för vissa data typer, till exempel [anpassade loggar](data-sources-custom-logs.md) och vissa [Azure-loggar](./diagnostics-extension-logs.md). Under inmatnings processen skickas loggar först till ett lagrings konto och senare matas in i Log Analytics eller Application Insights. Om du vill ha kontroll över dina data under inmatningen kan du använda dina egna lagrings konton i stället för den tjänst hanterade lagringen. Genom att använda ditt eget lagrings konto får du kontroll över åtkomst, innehåll, kryptering och kvarhållning av loggarna under inmatningen. Vi refererar till detta som att ta din egen lagring eller BYOS. 

Ett scenario som kräver BYOS är nätverks isolering via privata länkar. När du använder ett virtuellt nätverk är nätverks isolering ofta ett krav och åtkomst till offentligt Internet är begränsad. I sådana fall blockeras åtkomsten till Azure Monitor tjänst lagring för logg inmatning antingen helt och hållet eller anses vara en felaktig praxis. I stället ska loggar matas in via ett kundägda lagrings konto i VNet eller enkelt åtkomlig från det.

Ett annat scenario är kryptering av loggar med Kundhanterade nycklar (CMK). Kunder kan kryptera loggade data genom att använda CMK på de kluster som lagrar loggarna. Samma nyckel kan också användas för att kryptera loggar under inmatnings processen.

## <a name="data-types-supported"></a>Data typer som stöds

Följande data typer matas in från ett lagrings konto: Mer information om inmatningen av dessa typer finns i [samla in data från Azure Diagnostics-tillägget till Azure Monitor-loggar](./diagnostics-extension-logs.md) .

| Typ | Tabell information |
|:-----|:------------------|
| IIS-loggar | BLOB: wad-IIS-loggfiler|
|Windows-händelseloggar | Tabell: WADWindowsEventLogsTable |
| Syslog | Tabell: LinuxsyslogVer2v0 |
| Windows ETW-loggar | Tabell: WADETWEventTable|
| Service fabric | Tabell: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Anpassade loggar | saknas |
| Azure Security Center Watson-dumpfiler | saknas|  

## <a name="storage-account-requirements"></a>Krav för lagringskonto 
Lagrings kontot måste uppfylla följande krav:

- Tillgängligt för resurser i ditt VNet som skriver loggar till lagringen.
- Måste finnas i samma region som arbets ytan som den är länkad till.
- Tillåt Azure Monitor-åtkomst – om du har valt att begränsa åtkomsten till ditt lagrings konto för att välja nätverk, se till att tillåta detta undantag: *Tillåt betrodda Microsoft-tjänster att komma åt det här lagrings kontot*.

## <a name="process-to-configure-customer-owned-storage"></a>Process för att konfigurera kundägda lagrings enheter
Den grundläggande processen med att använda ditt eget lagrings konto för inmatning är följande:

1. Skapa ett lagrings konto eller Välj ett befintligt konto.
2. Länka lagrings kontot till en Log Analytics-arbetsyta.
3. Hantera lagringen genom att granska belastningen och kvarhållning för att säkerställa att den fungerar som förväntat.

Den enda metoden som är tillgänglig för att skapa och ta bort länkar är via REST API. Information om den speciella API-begäran som krävs för varje process finns i avsnitten nedan.

## <a name="command-line-and-rest-api"></a>Kommando rad och REST API

### <a name="command-line"></a>Kommandorad
Om du vill skapa och hantera länkade lagrings konton använder du [AZ Monitor Log-Analytics arbets yta länkad lagring](/cli/azure/monitor/log-analytics/workspace/linked-storage). Med det här kommandot kan du länka och ta bort länkar till lagrings konton från en arbets yta och lista de länkade lagrings kontona.

### <a name="request-and-cli-values"></a>Request-och CLI-värden

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson – Använd det här värdet för Azure Security Center Azure Watson dump-filer.
- CustomLogs – Använd det här värdet för följande data typer:
  - Anpassade loggar
  - IIS-loggar
  - Händelser (Windows)
  - Syslog (Linux)
  - ETW-loggar
  - Service Fabric händelser
  - Utvärderings data  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Det här värdet använder följande struktur:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


## <a name="get-linked-storage-accounts"></a>Hämta länkade lagrings konton

### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>Hämta länkade lagrings konton för alla typer av data källor

#### <a name="api-request"></a>API-begäran

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Svarsåtgärder

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Hämta länkade lagrings konton för en bestämd typ av data Källa

#### <a name="api-request"></a>API-begäran

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Svarsåtgärder 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>Skapa eller ändra en länk

När du länkar ett lagrings konto till en arbets yta börjar Log Analytics att använda det i stället för det lagrings konto som ägs av tjänsten. Du kan registrera en lista över lagrings konton samtidigt, och du kan använda samma lagrings konto för flera arbets ytor.

Om din arbets yta hanterar både VNet-resurser och resurser utanför ett VNet bör du se till att den inte avvisar trafik som kommer från Internet. Lagringen ska ha samma inställningar som din arbets yta och göras tillgänglig för resurser utanför ditt VNet. 

### <a name="api-request"></a>API-begäran

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Innehållet

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Svarsåtgärder

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Ta bort länk till ett lagrings konto
Om du väljer att sluta använda ett lagrings konto för inmatning eller ersätta arbets ytan du använder, bör du ta bort länken till lagringen från arbets ytan.

Om du tar bort länken för alla lagrings konton från en arbets yta görs ett försök att förlita dig på tjänst hanterade lagrings konton. Om dina agenter körs i ett VNet med begränsad åtkomst till Internet förväntas inmatningen att fungera. Arbets ytan måste ha ett länkat lagrings konto som kan kommas åt från dina övervakade resurser.

Innan du tar bort ett lagrings konto bör du kontrol lera att alla data som den innehåller har matats in i arbets ytan. Som en försiktighet bör du hålla ditt lagrings konto tillgängligt när du har länkat ett alternativt lagrings utrymme. Ta bara bort den när allt innehåll har matats in och du kan se att nya data skrivs till det nyanslutna lagrings kontot.


### <a name="api-request"></a>API-begäran
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Ersätta ett lagrings konto

Om du vill ersätta ett lagrings konto som används för inmatning måste du först skapa en länk för ett nytt lagrings konto. Loggnings agenterna kommer att hämta den uppdaterade konfigurationen och börja skicka data till den nya lagrings platsen.

Ta sedan bort länken till det gamla lagrings kontot så att agenter slutar skriva till det borttagna kontot. Vid inmatnings processen sparas data från det här kontot tills allt har matats in. Ta inte bort lagrings kontot förrän du ser att alla loggar har matats in.

Agent konfigurationen kommer att uppdateras efter några minuter och de växlar till den nya lagrings platsen.

## <a name="manage-storage-account"></a>Hantera lagrings konto

### <a name="load"></a>Inläsning

Lagrings konton kan hantera en viss belastning av Läs-och skriv förfrågningar innan de startar begränsnings begär Anden. Begränsningen påverkar hur lång tid det tar att mata in loggar och kan leda till förlorade data. Om lagringen är överbelastad registrerar du ytterligare lagrings konton och sprider belastningen mellan dem. 

### <a name="related-charges"></a>Relaterade avgifter

Lagrings konton debiteras av volymen lagrade data, typer av lagring och typen av redundans. Mer information finns i avsnittet om [priser för Block-Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) och [Table Storage prissättning](https://azure.microsoft.com/pricing/details/storage/tables/).

Om det registrerade lagrings kontot för din arbets yta finns i en annan region debiteras du för utgående trafik enligt dessa [pris uppgifter för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Nästa steg

- Mer information om hur du konfigurerar en privat länk finns i [använda Azure Private Link för att ansluta nätverk på ett säkert sätt till Azure Monitor](private-link-security.md)

