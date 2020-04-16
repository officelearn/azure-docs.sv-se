---
title: Loggning av Azure Key Vault | Microsoft-dokument
description: Den här kursen hjälper dig att komma igång med Azure Key Vault-loggning.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: e9198892f95635add27bcfe9e479d0dd6fe3f08d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422594"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

När du har skapat ett eller flera nyckelvalv vill du förmodligen övervaka hur och när dina nyckelvalv används och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, som sparar information i ett Azure-lagringskonto som du anger. En ny behållare med namnet **insights-logs-auditevent** skapas automatiskt för ditt angivna lagringskonto. Du kan använda samma lagringskonto för att samla in loggar för flera nyckelvalv.

Du kan komma åt loggningsinformationen 10 minuter (högst) efter nyckelvalvsåtgärden. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Den här kursen hjälper dig att komma igång med Azure Key Vault-loggning. Du skapar ett lagringskonto, aktiverar loggning och tolkar den insamlade logginformationen.  

> [!NOTE]
> Självstudiekursen innehåller inte instruktioner för hur du skapar nyckelvalv, nycklar eller hemligheter. Den här informationen finns i [Vad är Azure Key Vault?](overview.md)). Eller, för Azure CLI-instruktioner över flera plattformar, se [den här motsvarande självstudien](manage-with-cli2.md)).
>
> Den här artikeln innehåller Azure PowerShell-instruktioner för uppdatering av diagnostikloggning. Du kan också uppdatera diagnostikloggning med hjälp av Azure Monitor i avsnittet **Diagnostikloggar** i Azure-portalen. 
>

Mer information om Key Vault finns i [Vad är Azure Key Vault?](overview.md)). Information om var Key Vault är tillgängligt finns på [prissidan](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett befintligt nyckelvalv som du har använt.  
* Azure PowerShell, minsta version av 1.0.0. Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Om du redan har installerat Azure PowerShell och inte känner till `$PSVersionTable.PSVersion`versionen anger du från Azure PowerShell-konsolen .  
* Tillräckligt med utrymme i Azure för Key Vault-loggarna.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Ansluta till din nyckelvalvsprenumeration

Det första steget i att konfigurera nyckelloggning är att peka Azure PowerShell till nyckelvalvet som du vill logga.

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell hämtar alla prenumerationer som är associerade med det här kontot. Som standard använder PowerShell den första.

Du kanske måste ange den prenumeration som du använde för att skapa nyckelvalvet. Ange följande kommando för att se prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Om du sedan vill ange vilken prenumeration som är associerad med nyckelvalvet som du ska logga anger du:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Att peka PowerShell på rätt prenumeration är ett viktigt steg, särskilt om du har flera prenumerationer kopplade till ditt konto. Mer information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Skapa ett lagringskonto för dina loggar

Även om du kan använda ett befintligt lagringskonto för dina loggar skapar vi ett lagringskonto som ska vara dedikerat till Key Vault-loggar. För enkelhetens skull när vi måste ange detta senare, kommer vi att lagra detaljerna i en variabel som heter **sa**.

För ytterligare enkel hantering använder vi också samma resursgrupp som den som innehåller nyckelvalvet. Från [den kom igång-självstudien](../secrets/quick-create-cli.md)heter den här resursgruppen **ContosoResourceGroup**och vi fortsätter att använda platsen för Östasien. Ersätt dessa värden med dina egna, beroende på vad som är tillämpligt:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Om du bestämmer dig för att använda ett befintligt lagringskonto måste det använda samma prenumeration som ditt nyckelvalv. Och den måste använda Azure Resource Manager-distributionsmodellen i stället för den klassiska distributionsmodellen.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identifiera nyckelvalvet för dina loggar

I [den kom igång-självstudien](../secrets/quick-create-cli.md)var nyckelvalvets namn **ContosoKeyVault**. Vi fortsätter att använda det namnet och lagra informationen i en variabel med namnet **kv:**

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Aktivera loggning

För att aktivera loggning för Key Vault använder vi cmdleten **Set-AzDiagnosticSetting,** tillsammans med de variabler som vi skapade för det nya lagringskontot och nyckelvalvet. Vi ställer också in **flaggan -Aktiverad** på **$true** och ställer in kategorin till **AuditEvent** (den enda kategorin för loggning av Nyckelvalv):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Utdata ser ut så här:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Den här utdata bekräftar att loggning nu är aktiverat för ditt nyckelvalv och att den sparar information i ditt lagringskonto.

Du kan också ange en bevarandeprincip för dina loggar så att äldre loggar tas bort automatiskt. Ange till exempel bevarandeprincipen genom att ange flaggan **-RetentionEnabled** på **$true**och ange parametern **-RetentionInDays** till **90** så att loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Vad loggas:

* Alla autentiserade REST API-begäranden, inklusive misslyckade begäranden som ett resultat av åtkomstbehörigheter, systemfel eller felaktiga begäranden.
* Åtgärder på själva nyckelvalvet, inklusive skapande, borttagning, inställning av nyckelvalvsåtkomstprinciper och uppdatering av nyckelvalvsattribut som taggar.
* Åtgärder på nycklar och hemligheter i nyckelvalvet, inklusive:
  * Skapa, ändra eller ta bort dessa nycklar eller hemligheter.
  * Signering, verifiera, kryptera, dekryptera, slå in och packa upp nycklar, få hemligheter och lista nycklar och hemligheter (och deras versioner).
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begäranden som inte har en innehavartoken, som är felaktig eller har upphört att gälla eller som har en ogiltig token.  

## <a name="access-your-logs"></a><a id="access"></a>Komma åt loggarna

Key Vault-loggar lagras i behållaren **insights-logs-auditevent** i lagringskontot som du angav. Om du vill visa loggarna måste du hämta blobbar.

Börja med att skapa en variabel för containerns namn. Du ska använda den här variabeln under resten av genomgången.

```powershell
$container = 'insights-logs-auditevent'
```

Om du vill visa en lista över alla blobbar i den här behållaren anger du:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Utdata ser ut ungefär så här:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Som du kan se av den här utdata följer blobbar en namngivningskonvention:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom du kan använda samma lagringskonto för att samla in loggar för flera resurser är det fullständiga resurs-ID:t i blobnamnet användbart för att komma åt eller hämta bara de blobbar som du behöver. Men innan vi gör det ska vi titta på hur du hämtar alla blobbar.

Skapa en mapp för att hämta blobbar. Ett exempel:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Hämta sedan en lista över alla blobbar:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Pipe denna lista genom **Get-AzStorageBlobContent** att ladda ner blobbar till målmappen:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

När du kör det **/** här andra kommandot skapar avgränsare i blobnamnen en fullständig mappstruktur under målmappen. Du ska använda den här strukturen för att hämta och lagra blobbar som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Ett exempel:

* Om du har flera nyckelvalv och bara vill hämta loggar för ett av dem, mer specifikt nyckelvalvet CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Om du har flera resursgrupper och bara vill hämta loggar för en av dem använder du `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Om du vill ladda ner alla loggar för januari månad 2019 använder du: `-Blob '*/year=2019/m=01/*'`

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nu är det dags att börja titta på vad som finns i loggarna. Men innan vi går vidare till det, bör du veta två kommandon:

* Om du vill fråga efter statusen för nyckelvalvsresursens diagnostikinställningar: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Om du vill inaktivera loggning för nyckelvalvsresursen: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Tolka Key Vault-loggarna

Enskilda blobbar lagras som text, formaterad som en JSON-blobb. Låt oss titta på en exempelloggpost. Kör följande kommando:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Den returnerar en loggpost som liknar den här:

```json
    {
        "records":
        [
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
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

I följande tabell visas fältnamn och beskrivningar:

| Fältnamn | Beskrivning |
| --- | --- |
| **Tid** |Datum och tid i UTC. |
| **resourceId (resourceId)** |Resurs-ID för Azure Resource Manager. För Key Vault-loggar är detta alltid key vault-resurs-ID. |
| **operationName** |Namnet på åtgärden, som beskrivs i nästa tabell. |
| **operationVersion** |REST API-version som begärs av klienten. |
| **Kategori** |Typ av resultat. För Key Vault-loggar är **AuditEvent** det enda tillgängliga värdet. |
| **resultType** |Resultatet av REST API-begäran. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| **durationMs** |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| **callerIpAddress** |IP-adressen för klienten som gjorde begäran. |
| **correlationId (korrelationId)** |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| **Identitet** |Identitet från token som presenterades i REST API-begäran. Detta är vanligtvis en "användare", ett "tjänsthuvudnamn" eller kombinationen "user+appId", som i fallet med en begäran som är resultatet av en Azure PowerShell-cmdlet. |
| **Egenskaper** |Information som varierar beroende på åtgärden (**operationName**). I de flesta fall innehåller det här fältet klientinformation (användaragentsträngen som skickas av klienten), den exakta REST API-begäran URI och HTTP-statuskoden. När ett objekt returneras som ett resultat av en begäran (till exempel **KeyCreate** eller **VaultGet)** innehåller det också nyckeln URI (som "id"), valv-URI eller hemlig URI. |

Fältvärdena **operationName** är i *ObjectVerb-format.* Ett exempel:

* Alla nyckelvalvsåtgärder `Vault<action>` har formatet, `VaultGet` `VaultCreate`till exempel och .
* Alla nyckelåtgärder `Key<action>` har formatet, `KeySign` `KeyList`till exempel och .
* Alla hemliga `Secret<action>` åtgärder har `SecretGet` formatet, till exempel och `SecretListVersions`.

I följande tabell visas **operationsName-värdena** och motsvarande REST API-kommandon:

| operationName | KOMMANDOT REST API |
| --- | --- |
| **Autentisering** |Autentisera via Azure Active Directory-slutpunkt |
| **VaultGet** |[Hämta information om ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Skapa eller uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Ta bort ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Visa en lista med alla nyckelvalv i en resursgrupp](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Skapa en nyckel](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Hämta information om en nyckel](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importera en nyckel till ett valv](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Säkerhetskopiera en nyckel](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Ta bort en nyckel](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Återställa en nyckel](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Signera med en nyckel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verifiera med en nyckel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Omsluta en nyckel](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Ta bort en nyckelomslutning](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Kryptera med en nyckel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Dekryptera med en nyckel](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Uppdatera en nyckel](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Visa en lista med nycklarna i ett valv](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Visa en lista över versionerna av en nyckel](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Skapa en hemlighet](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Få en hemlighet](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Uppdatera en hemlighet](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Ta bort en hemlighet](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Visa en lista över hemligheterna i ett valv](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Visa en lista över versionerna av en hemlighet](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor-loggar för att granska Key Vault **AuditEvent-loggar.** I Azure Monitor-loggar använder du loggfrågor för att analysera data och få den information du behöver. 

Mer information, inklusive hur du konfigurerar detta, finns [i Azure Key Vault-lösningen i Azure Monitor-loggar](../../azure-monitor/insights/azure-key-vault.md). Den här artikeln innehåller också instruktioner om du behöver migrera från den gamla Key Vault-lösningen som erbjöds under förhandsversionen av Azure Monitor-loggarna, där du först dirigerade loggarna till ett Azure-lagringskonto och konfigurerade Azure Monitor-loggar att läsa därifrån.

## <a name="next-steps"></a><a id="next"></a>Nästa steg

En självstudiekurs som använder Azure Key Vault i ett .NET-webbprogram finns i [Använda Azure Key Vault från ett webbprogram](tutorial-net-create-vault-azure-web-app.md).

Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](developers-guide.md).

En lista över Azure PowerShell 1.0 cmdlets för Azure Key Vault finns i [Azure Key Vault cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

En självstudiekurs om nyckelrotation och logggranskning med Azure Key Vault finns i [Konfigurera Key Vault med end-to-end-nyckelrotation och granskning](../secrets/key-rotation-log-monitoring.md).
