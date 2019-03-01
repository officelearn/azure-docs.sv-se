---
title: Azure Key Vault-loggning | Microsoft Docs
description: Den här kursen hjälper dig att komma igång med Azure Key Vault-loggning.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: afec42551f124890dd2cc7b03cce48c359fc88c4
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194103"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du skapar en eller flera nyckelvalv kan du förmodligen vill du övervaka hur och när nyckelvalven är används, och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, vilket sparar information i ett Azure storage-konto som du anger. En ny behållare med namnet **insights-logs-auditevent** skapas automatiskt för det angivna lagringskontot. Du kan använda samma lagringskonto för att samla in loggar för flera nyckelvalv.

Du kan komma åt loggningsinformationen 10 minuter (högst) efter åtgärden nyckelvalv. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Den här kursen hjälper dig att komma igång med Azure Key Vault-loggning. Du skapar ett lagringskonto, aktiverar du loggning och tolkar insamlade logginformation.  

> [!NOTE]
> Självstudiekursen innehåller inte instruktioner för hur du skapar nyckelvalv, nycklar eller hemligheter. Den här information finns i [vad är Azure Key Vault?](key-vault-overview.md). Eller plattformsoberoende Azure CLI-instruktioner finns i [den här självstudiekursen](key-vault-manage-with-cli2.md).
>
> Den här artikeln innehåller instruktioner för Azure PowerShell för att uppdatera Diagnostisk loggning. Du kan också uppdatera Diagnostisk loggning genom att använda Azure Monitor i den **diagnostikloggar** på Azure portal. 
>

Mer information om Key Vault finns i [vad är Azure Key Vault?](key-vault-whatis.md). Läs om hur där Key Vault är tillgängligt i [prissättningssidan](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett befintligt nyckelvalv som du har använt.  
* Azure PowerShell, lägst version 1.0.0. Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Om du redan har installerat Azure PowerShell och inte vet vilken version från Azure PowerShell-konsolen, ange `$PSVersionTable.PSVersion`.  
* Tillräckligt med utrymme i Azure för Key Vault-loggarna.

## <a id="connect"></a>Ansluta till key vault-prenumeration

Det första steget i att konfigurera viktiga loggning är att punkt Azure PowerShell till nyckelvalvet som du vill logga in.

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med hjälp av följande kommando:  

```PowerShell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot. Som standard använder den första mallen i PowerShell.

Du kan behöva ange den prenumeration som du använde för att skapa ett nyckelvalv. Ange följande kommando för att visa prenumerationerna för ditt konto:

```PowerShell
Get-AzSubscription
```

Ange sedan om du vill ange den prenumeration som är associerad med nyckelvalvet som du ska logga:

```PowerShell
Set-AzContext -SubscriptionId <subscription ID>
```

Pekar rätt prenumeration PowerShell är ett viktigt steg, särskilt om du har flera prenumerationer som är associerat med ditt konto. Mer information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Skapa ett lagringskonto för dina loggar

Men du kan använda ett befintligt lagringskonto för dina loggar, ska vi skapa ett lagringskonto som ska användas specifikt för Key Vault-loggar. För att underlätta när vi har senare ska ange detta, så lagrar vi informationen i en variabel med namnet **sa**.

För ytterligare underlätta använder vi också samma resursgrupp som det som innehåller nyckelvalvet. Från den [introduktionskursen](key-vault-get-started.md), heter den här resursgruppen **ContosoResourceGroup**, och vi kommer att fortsätta att använda Östasien plats. Ersätt värdena med dina egna efter behov:

```PowerShell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Om du vill använda ett befintligt lagringskonto måste det använda samma prenumeration som ditt nyckelvalv. Och det måste använda Azure Resource Manager-distributionsmodellen i stället för den klassiska distributionsmodellen.
>
>

## <a id="identify"></a>Identifiera nyckelvalvet för dina loggar

I den [introduktionskursen](key-vault-get-started.md), namn på key vault har **ContosoKeyVault**. Vi fortsätter att använda det namnet och lagrar informationen i en variabel med namnet **kv**:

```PowerShell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Aktivera loggning

Om du vill aktivera loggning för Nyckelvalvet, använder vi den **Set-AzDiagnosticSetting** cmdlet, tillsammans med de variabler som vi skapade för det nya lagringskontot och nyckelvalvet. Vi kan också ange den **-aktiverad** flaggan till **$true** och ange kategorin till **AuditEvent** (den enda kategorin för nyckelvalvloggning):

```PowerShell
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

Det här resultatet bekräftar att loggning är aktiverat för nyckelvalvet och information sparas i ditt storage-konto.

Du kan ange en bevarandeprincip för dina loggar så att äldre loggar tas bort automatiskt. Till exempel bevarandeprincip genom att ange den **- RetentionEnabled** flaggan till **$true**, och ange den **- RetentionInDays** parameter **90**så att loggar som är äldre än 90 dagar tas bort automatiskt.

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Vad loggas:

* Alla autentiserade REST API-begäranden, inklusive misslyckade begäranden på grund av åtkomstbehörigheter, systemfel eller ogiltiga förfrågningar.
* Åtgärder i nyckelvalvet själva, inklusive skapa, ta bort eller inställningen nyckelvalvets åtkomstprinciper och uppdatera nyckelvärdesattribut som taggar.
* Åtgärder för nycklar och hemligheter i nyckelvalvet, inklusive:
  * Skapa, ändra eller ta bort nycklar eller hemligheter.
  * Signering, verifiera, kryptera, dekryptera, omsluta och borttagning av nycklar, hämta hemligheter, och lista nycklar och hemligheter (och deras versioner).
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begäranden som inte har någon ägartoken, som är felaktiga eller har upphört att gälla eller som har en ogiltig token.  

## <a id="access"></a>Komma åt loggarna

Key Vault-loggar lagras i den **insights-logs-auditevent** behållare i lagringskontot som du angav. Om du vill visa loggfilerna kan behöva du ladda ned blobar.

Börja med att skapa en variabel för containerns namn. Du använder den här variabeln i resten av den här genomgången.

```PowerShell
$container = 'insights-logs-auditevent'
```

Om du vill visa alla blobbar i den här behållaren, anger du:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Utdata ser ut ungefär så här:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Som du kan se dessa utdata följer blobbarna en namngivningskonvention: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom du kan använda samma lagringskonto för att samla in loggar för flera resurser, är fullständigt resurs-ID i blobnamnet användbar för att komma åt eller hämta endast de blobbar som du behöver. Men innan vi gör det ska vi titta på hur du hämtar alla blobbar.

Skapa en mapp för att ladda ned blobbarna. Exempel:

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Hämta sedan en lista över alla blobbar:  

```PowerShell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Skicka den här listan via **Get-AzStorageBlobContent** att ladda ned blobbarna till målmappen:

```PowerShell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

När du kör den här andra kommandot skapar den **/** avgränsaren i blobbnamnen skapar en fullständig mappstruktur under målmappen. Du använder den här strukturen för att ladda ned och spara blobbarna som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera nyckelvalv och bara vill hämta loggar för ett av dem, mer specifikt nyckelvalvet CONTOSOKEYVAULT3:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Om du har flera resursgrupper och bara vill hämta loggar för en av dem använder du `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Om du vill hämta alla loggar för januari 2019 använda `-Blob '*/year=2019/m=01/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nu är det dags att börja titta på vad som finns i loggarna. Men innan vi går vidare till som du bör känna till två fler kommandon:

* Om du vill fråga efter statusen för nyckelvalvsresursens diagnostikinställningar: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Om du vill inaktivera loggning för nyckelvalvsresursen: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Tolka Key Vault-loggarna

Enskilda blobbar lagras som text, formaterad som en JSON-blobb. Låt oss titta på ett exempel på post i loggen. Kör följande kommando:

```PowerShell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Den returnerar en loggpost liknande detta:

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

I följande tabell visas de fältnamnen och beskrivningarna:

| Fältnamn | Beskrivning |
| --- | --- |
| **tid** |Datum och tid i UTC. |
| **Resurs-ID** |Azure Resource Manager-resurs-ID. För Key Vault-loggar är detta alltid Nyckelvalvets resurs-ID. |
| **OperationName** |Namnet på åtgärden, som beskrivs i nästa tabell. |
| **operationVersion** |REST API-version som begärs av klienten. |
| **Kategori** |Typ av resultat. För Key Vault-loggar är **AuditEvent** är det enda, tillgängliga värdet. |
| **resultType** |Resultatet av REST API-begäran. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| **durationMs** |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| **callerIpAddress** |IP-adressen för klienten som gjorde begäran. |
| **Korrelations-ID** |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| **Identitet** |Identiteten från den token som angavs i REST API-begäran. Detta är vanligtvis en ”användare”, ”tjänstens huvudnamn” eller ”användare + App-ID”, kombination som i fallet med en begäran som är ett resultat från en Azure PowerShell-cmdlet. |
| **Egenskaper** |Information som varierar utifrån igen (**operationName**). I de flesta fall innehåller det här fältet klientinformation (user agent strängen skickades av klienten), den exakta REST API-begäran URI och HTTP-statuskoden. Dessutom när ett objekt returneras till följd av en begäran (till exempel **KeyCreate** eller **VaultGet**), den innehåller också nyckeln URI (som ”id”), valvets URI eller hemliga URI: N. |

Den **operationName** fältvärdena har *ObjectVerb* format. Exempel:

* Alla åtgärder i nyckelvalv har den `Vault<action>` format, till exempel `VaultGet` och `VaultCreate`.
* Alla åtgärder med nycklar har den `Key<action>` format, till exempel `KeySign` och `KeyList`.
* Alla åtgärder med hemligheter har den `Secret<action>` format, till exempel `SecretGet` och `SecretListVersions`.

Följande tabell listar de **operationName** värden och motsvarande REST API-kommandon:

| operationName | REST API-kommando |
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
| **SecretGet** |[Hämta en hemlighet](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Uppdatera en hemlighet](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Ta bort en hemlighet](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Visa en lista över hemligheterna i ett valv](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Visa en lista över versionerna av en hemlighet](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor-loggar för att granska Key Vault **AuditEvent** loggar. I Azure Monitor-loggar använder du loggfrågor för att analysera data och få den information du behöver. 

Mer information, inklusive hur du konfigurerar detta finns i [Azure Key Vault-lösningen i Azure Monitor-loggar](../azure-monitor/insights/azure-key-vault.md). Den här artikeln innehåller också anvisningar om du vill migrera från den gamla Key Vault-lösningen som fanns under Azure Monitor-loggar, förhandsgranska, där du först dirigera loggarna till ett Azure storage-konto och konfigurerade Azure Monitor-loggar för att läsa därifrån.

## <a id="next"></a>Nästa steg

En självstudiekurs som använder Azure Key Vault i en .NET-webbprogram, finns i [använda Azure Key Vault från ett webbprogram](tutorial-net-create-vault-azure-web-app.md).

Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).

En lista över Azure PowerShell 1.0-cmdlets för Azure Key Vault finns i [Azure Key Vault-cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

En självstudiekurs om nyckelrotering och logggranskning med Azure Key Vault finns i [konfigurera Key Vault med nyckelgranskning för slutpunkt till slutpunkt och](key-vault-key-rotation-log-monitoring.md).
