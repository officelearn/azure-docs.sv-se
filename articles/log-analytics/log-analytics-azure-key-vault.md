---
title: Azure Key Vault-lösning i Log Analytics | Microsoft Docs
description: Du kan använda Azure Key Vault-lösning i Log Analytics till Azure Key Vault-loggarna.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 9c4b16ec11d1990de687014c5385314f0e0c602a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Azure Key Vault Analytics lösning i logganalys

![Key Vault symbol](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

Du kan använda Azure Key Vault-lösningen i Log Analytics för att läsa igenom AuditEvent-loggarna i Azure Key Vault.

Om du vill använda lösningen måste du aktivera loggning av Azure Key Vault-diagnostik och dirigera diagnostik till logganalys-arbetsytan. Du behöver inte skriva loggarna till Azure Blob storage.

> [!NOTE]
> Sättet att skicka loggar från Nyckelvalvet till logganalys ändras i januari 2017. Om Key Vault-lösningen som du använder visar *(föråldrad)* i titeln, referera till [migrera från den gamla Key Vault-lösningen](#migrating-from-the-old-key-vault-solution) anvisningar för hur du ska följa.
>
>

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Key Vault-lösningen:

1. Aktivera Azure Key Vault-lösning från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. Aktivera diagnostikloggning för Key Vault-resurser för att övervaka, antingen med hjälp av [portal](#enable-key-vault-diagnostics-in-the-portal) eller [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Aktivera Key Vault-diagnostik i portalen

1. Navigera till Key Vault-resurs för att övervaka i Azure-portalen
2. Välj *diagnostik loggar* att öppna följande sida

   ![Bild av Azure Key Vault sida vid sida](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klicka på *aktivera diagnostiken* att öppna följande sida

   ![Bild av Azure Key Vault sida vid sida](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Aktivera diagnostik, klicka på *på* under *Status*
5. Klicka på kryssrutan för *skicka till logganalys*
6. Välj en befintlig logganalys-arbetsyta eller skapa en arbetsyta
7. Så här aktiverar du *AuditEvent* loggar, klickar du på kryssrutan under logg
8. Klicka på *spara* att aktivera loggning av diagnostik till logganalys

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Aktivera diagnostik för Key Vault med hjälp av PowerShell
Följande PowerShell-skript innehåller ett exempel på hur du använder `Set-AzureRmDiagnosticSetting` diagnostikloggning för Nyckelvalvet ska aktiveras:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Granska Azure Key Vault information för samlingen
Azure Key Vault-lösningen samlar in diagnostik loggarna direkt från Nyckelvalvet.
Det är inte nödvändigt att skriva loggarna till Azure Blob storage och ingen agent krävs för datainsamling.

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för Azure Key Vault.

| Plattform | Styr agent | System Center Operations Manager-agenten | Azure | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | anländer |

## <a name="use-azure-key-vault"></a>Använda Azure Key Vault
När du [installerar lösningen](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), visa Key Vault-data genom att klicka på den **Azure Key Vault** panelen från den **översikt** sidan i logganalys.

![Bild av Azure Key Vault sida vid sida](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

När du klickar på den **översikt** panelen, kan du visa sammanfattningar av loggar och visa sedan detaljnivåerna till information i följande kategorier:

* Volymen för alla åtgärder i nyckelvalvet över tid
* Det gick inte åtgärden volymer över tid
* Genomsnittlig svarstid för operativa för åtgärden
* Tjänstkvalitet för åtgärder med antal åtgärder som tar mer än 1 000 ms och en lista över åtgärder som tar mer än 1 000 ms

![Bild av Azure Key Vault-instrumentpanelen](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Bild av Azure Key Vault-instrumentpanelen](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Att visa detaljer för någon åtgärd
1. På den **översikt** klickar du på den **Azure Key Vault** panelen.
2. På den **Azure Key Vault** instrumentpanel, Granska sammanfattningen i ett av bladen och klicka sedan på en om du vill visa detaljerad information om den på sidan Logga sökning.

    Du kan visa resultaten av tid, detaljerade resultat och Logghistoriken på någon av sidorna loggen sökning. Du kan också filtrera efter aspekter att begränsa resultaten.

## <a name="log-analytics-records"></a>Log Analytics-poster
Azure Key Vault-lösningen analyserar poster som har en typ av **KeyVaults** som samlas in från [AuditEvent loggar](../key-vault/key-vault-logging.md) i Azure-diagnostik.  Egenskaper för dessa poster finns i följande tabell:  

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |IP-adressen för klienten som gjorde begäran |
| Kategori | *AuditEvent* |
| CorrelationId |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| DurationMs |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Nu omfattar inte Nätverksfördröjningen, så att den tid som du mäta på klientsidan inte kanske stämmer med den här gången. |
| httpStatusCode_d |HTTP-statuskoden som returnerades av begäran (till exempel *200*) |
| id_s |Unikt ID för begäran |
| identity_claim_appid_g | GUID för det program-id |
| OperationName |Namnet på åtgärden, enligt beskrivningen i [Azure Key Vault-loggning](../key-vault/key-vault-logging.md) |
| OperationVersion |REST API-version som begärs av klienten (till exempel *2015-06-01*) |
| requestUri_s |URI för begäran |
| Resurs |Namnet på nyckelvalvet |
| ResourceGroup |Resursgruppen för nyckelvalvet |
| Resurs-ID |Azure Resource Manager Resource-ID. För Key Vault loggar är Key Vault-resurs-ID. |
| ResourceProvider |*MICROSOFT. KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP-status (till exempel *OK*) |
| ResultType |Resultatet av REST API-begäran (till exempel *lyckade*) |
| SubscriptionId |Azure prenumerations-ID för den prenumeration som innehåller Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrera från den gamla Key Vault-lösningen
Sättet att skicka loggar från Nyckelvalvet till logganalys ändras i januari 2017. Ändringarna ger följande fördelar:
+ Loggarna skrivs direkt till Log Analytics utan att behöva använda ett lagringskonto
+ Mindre fördröjning från när loggar genereras till dem som finns i logganalys
+ Färre konfigurationssteg
+ Ett vanligt format för alla typer av Azure-diagnostik

Använda den uppdaterade lösningen:

1. [Konfigurera diagnostik skickas direkt till Log Analytics från Nyckelvalvet](#enable-key-vault-diagnostics-in-the-portal)  
2. Aktivera Azure Key Vault-lösning med hjälp av den process som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md)
3. Uppdatera alla sparade frågor, instrumentpaneler eller aviseringar för att använda den nya datatypen
  + Typen är ändra från: KeyVaults till AzureDiagnostics. Du kan använda resurstypens för att filtrera till Key Vault loggar.
  - I stället för: `KeyVaults`, använda `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Fält: (fältnamn är skiftlägeskänsligt)
  - För alla fält som har suffixet \_s, \_d, eller \_g i namnet, ändra det första tecknet till gemener
  - För alla fält som har suffixet \_o i namn data delas upp i enskilda fält baserat på de kapslade fältnamn. Till exempel är UPN för anroparen lagrad i ett fält `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Fältet CallerIpAddress ändras till CallerIPAddress
   - Fältet RemoteIPCountry finns inte längre
4. Ta bort den *Key Vault Analytics (inaktuell)* lösning. Om du använder PowerShell använder du `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data som samlas in innan ändringen inte visas i den nya lösningen. Du kan fortsätta att fråga efter data med hjälp av den gamla typen och fältnamn.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerad Azure Key Vault-data.
