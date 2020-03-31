---
title: Azure Key Vault-lösning i Azure Monitor | Microsoft-dokument
description: Du kan använda Azure Key Vault-lösningen i Azure Monitor för att granska Azure Key Vault-loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7a2becf8cb43568383c324bb9f4f5b2e7b844268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667151"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics-lösning i Azure Monitor

![Symbol för Nyckelvalv](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda Azure Key Vault-lösningen i Azure Monitor för att granska Azure Key Vault AuditEvent-loggar.

Om du vill använda lösningen måste du aktivera loggning av Azure Key Vault-diagnostik och dirigera diagnostiken till en Log Analytics-arbetsyta. Det är inte nödvändigt att skriva loggarna till Azure Blob-lagring.

> [!NOTE]
> I januari 2017 ändrades sättet att skicka loggar från Key Vault till Log Analytics. Om den Key Vault-lösning som du använder visas *(föråldrad)* i titeln läser du [migrering från den gamla Key Vault-lösningen](#migrating-from-the-old-key-vault-solution) för steg som du behöver följa.
>
>

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Key Vault-lösningen:

1. Använd processen som beskrivs i [Lägg till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md) för att lägga till Azure Key Vault-lösningen på din Log Analytics-arbetsyta.
2. Aktivera diagnostikloggning för Key Vault-resurser att övervaka, antingen med hjälp av [portalen](#enable-key-vault-diagnostics-in-the-portal) eller [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Aktivera Key Vault-diagnostik i portalen

1. I Azure-portalen navigerar du till Key Vault-resursen för att övervaka
2. Välj *Diagnostikinställningar* för att öppna följande sida

   ![bild av panelen Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klicka *på Aktivera diagnostik* för att öppna följande sida

   ![bild av panelen Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Ge diagnostikinställningen ett namn.
5. Klicka på kryssrutan för *Skicka till logganalys*
6. Välj en befintlig Log Analytics-arbetsyta eller skapa en arbetsyta
7. Om du vill aktivera *AuditEvent-loggar* klickar du på kryssrutan under Logg
8. Klicka på *Spara* om du vill aktivera loggning av diagnostik till Log Analytics-arbetsytan.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Aktivera Diagnostik för Key Vault med PowerShell
Följande PowerShell-skript är ett exempel `Set-AzDiagnosticSetting` på hur du använder för att aktivera resursloggning för Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Granska information om datainsamling i Azure Key Vault
Azure Key Vault-lösningen samlar diagnostikloggar direkt från Key Vault.
Det är inte nödvändigt att skriva loggarna till Azure Blob-lagring och ingen agent krävs för datainsamling.

I följande tabell visas datainsamlingsmetoder och annan information om hur data samlas in för Azure Key Vault.

| Plattform | Direkt agent | System Center Operations Manager-agent | Azure | Krävs Operations Manager? | Operations Manager-agentdata som skickas via hanteringsgrupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | vid ankomsten |

## <a name="use-azure-key-vault"></a>Använda Azure Key Vault
När du [har installerat lösningen](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)visar du Key Vault-data genom att klicka på panelen Key Vault **Analytics** från **översiktssidan** för Azure Monitor. Öppna den här sidan från **Azure Monitor-menyn** genom att klicka på **Mer** under avsnittet **Insikter.** 

![bild av panelen Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

När du har klickat på panelen **Key Vault Analytics** kan du visa sammanfattningar av dina loggar och sedan gå in på detaljer för följande kategorier:

* Volymen för alla nyckelvalvsoperationer över tid
* Misslyckade operationsvolymer över tid
* Genomsnittlig driftfördröjning efter operation
* Tjänsternas kvalitet för verksamheten med antalet verksamheter som tar mer än 1000 ms och en lista över åtgärder som tar mer än 1000 ms

![bild av instrumentpanelen i Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![bild av instrumentpanelen i Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Så här visar du information om alla åtgärder
1. Klicka på panelen **Key Vault Analytics** på sidan **Översikt.**
2. På instrumentpanelen **i Azure Key Vault** granskar du sammanfattningsinformationen i ett av bladen och klickar sedan på ett för att visa detaljerad information om den på loggsöksidan.

    På någon av loggsöksidorna kan du visa resultat efter tid, detaljerade resultat och loggsökhistorik. Du kan också filtrera efter fasor för att begränsa resultaten.

## <a name="azure-monitor-log-records"></a>Loggposter för Azure Monitor
Azure Key Vault-lösningen analyserar poster som har en typ av **KeyVaults** som samlas in från [AuditEvent-loggar](../../key-vault/key-vault-logging.md) i Azure Diagnostics.  Egenskaper för dessa poster finns i följande tabell:  

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |IP-adress för den klient som gjorde begäran |
| `Category` | *AuditEvent* |
| `CorrelationId` |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| `DurationMs` |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Den här gången innehåller inte nätverksfördröjning, så den tid som du mäter på klientsidan kanske inte matchar den här gången. |
| `httpStatusCode_d` |HTTP-statuskod som returneras av begäran (till exempel *200*) |
| `id_s` |Unikt ID för begäran |
| `identity_claim_appid_g` | GUID för program-ID |
| `OperationName` |Namnet på åtgärden, som dokumenterats i [Azure Key Vault Logging](../../key-vault/key-vault-logging.md) |
| `OperationVersion` |REST API-version begärd av klienten (till exempel *2015-06-01)* |
| `requestUri_s` |Uri av begäran |
| `Resource` |Namnet på nyckelvalvet |
| `ResourceGroup` |Resursgruppen för nyckelvalvet |
| `ResourceId` |Azure Resource Manager Resource-ID. För Key Vault-loggar är detta resurs-ID för Key Vault. |
| `ResourceProvider` |*Microsoft. KEYVAULT (VIKTIGT)* |
| `ResourceType` | *Valv* |
| `ResultSignature` |HTTP-status (till exempel *OK*) |
| `ResultType` |Resultat av REST API-begäran (till exempel *lyckades)* |
| `SubscriptionId` |Azure-prenumerations-ID för prenumerationen som innehåller Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrera från den gamla Key Vault-lösningen
I januari 2017 ändrades sättet att skicka loggar från Key Vault till Log Analytics. Dessa ändringar ger följande fördelar:
+ Loggar skrivs direkt till en Log Analytics-arbetsyta utan att behöva använda ett lagringskonto
+ Mindre latens från den tidpunkt då loggar genereras till dem är tillgängliga i Log Analytics
+ Färre konfigurationssteg
+ Ett vanligt format för alla typer av Azure-diagnostik

Så här använder du den uppdaterade lösningen:

1. [Konfigurera diagnostik som ska skickas direkt till en Log Analytics-arbetsyta från Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Aktivera Azure Key Vault-lösningen med hjälp av processen som beskrivs i [Lägg till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md)
3. Uppdatera sparade frågor, instrumentpaneler eller aviseringar för att använda den nya datatypen
   + Typen ändras från: KeyVaults till AzureDiagnostics. Du kan använda ResourceType för att filtrera till Key Vault-loggar.
   + I stället `KeyVaults`för: , använd`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Fält: (Fältnamn är skiftlägeskänsliga)
   + För alla fält som har \_ett \_suffix \_med s, d eller g i namnet ändrar du det första tecknet till gemener
   + För alla fält som har \_ett suffix med o i namn delas data upp i enskilda fält baserat på de kapslade fältnamnen. Till exempel lagras UPN för den som ringer i ett fält`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Field CallerIpAddress har ändrats till CallerIPAddress
   + Field RemoteIPCountry finns inte längre
4. Ta bort *lösningen för Key Vault Analytics (Föråldrad).* Om du använder PowerShell använder du`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data som samlas in före ändringen visas inte i den nya lösningen. Du kan fortsätta att fråga efter dessa data med hjälp av gamla typ- och fältnamn.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [loggfrågor i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerade Azure Key Vault-data.
