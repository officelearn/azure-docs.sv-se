---
title: Azure Key Vault-loggning | Microsoft Docs
description: "Den här kursen hjälper dig att komma igång med Azure Key Vault-loggning."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: cabailey
translationtype: Human Translation
ms.sourcegitcommit: 30b30513d5563cf64679e29c4858bf15f65d3a44
ms.openlocfilehash: 015c997135eae9c936af1a1ec0b0064912baaa04
ms.lasthandoff: 02/28/2017


---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduktion
När du har skapat ett eller flera nyckelvalv vill du förmodligen övervaka hur och när nyckelvalven används, och av vem. Du kan göra det genom att aktivera loggning för nyckelvalvet, vilket sparar information i ett Azure-lagringskonto som du anger. En ny behållare med namnet **insights-logs-auditevent** skapas automatiskt för det angivna lagringskontot och du kan använda samma lagringskonto för att samla in loggar för flera nyckelvalv.

Loggningsinformationen är tillgänglig senast tio minuter efter att nyckelvalvsåtgärden ägde rum. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Den här självstudiekursen hjälper dig att komma igång med Azure Key Vault-loggning och förklarar hur du skapar ett lagringskonto, aktiverar loggning och tolkar loggningsinformation som samlas in.  

> [!NOTE]
> Självstudiekursen innehåller inte instruktioner för hur du skapar nyckelvalv, nycklar eller hemligheter. Den här informationen finns i [Komma igång med Azure Key Vault](key-vault-get-started.md). Anvisningar för plattformsoberoende kommandoradsgränssnitt finns i [den här självstudiekursen](key-vault-manage-with-cli.md).
> 
> För närvarande kan du inte konfigurera Azure Key Vault på Azure-portalen. I stället använder du dessa Azure PowerShell-instruktioner.
> 
> 

Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Krav
För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett befintligt nyckelvalv som du har använt.  
* Azure PowerShell, **minst version 1.0.1**. Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs). Om du redan har installerat Azure PowerShell och inte vet vilken version du har skriver du `(Get-Module azure -ListAvailable).Version` i Azure PowerShell-konsolen.  
* Tillräckligt med utrymme i Azure för Key Vault-loggarna.

## <a name="a-idconnectaconnect-to-your-subscriptions"></a><a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

    Login-AzureRmAccount

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer kan du behöva ange en som användes för att skapa Azure Key Vault. Skriv följande för att visa prenumerationerna för ditt konto:

    Get-AzureRmSubscription

Skriv sedan följande för att ange den prenumeration som är associerad med nyckelvalvet som du ska logga:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Mer information om hur du konfigurerar Azure PowerShell finns  i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="a-idstorageacreate-a-new-storage-account-for-your-logs"></a><a id="storage"></a>Skapa ett nytt lagringskonto för dina loggar
Även om du kan använda ett befintligt lagringskonto för dina loggar ska vi skapa ett nytt lagringskonto som ska användas specifikt för Key Vault-loggar. För att underlätta för oss när vi senare ska ange detta så lagrar vi informationen i en variabel med namnet **sa**.

För att underlätta ytterligare använder vi också samma resursgrupp som den som innehåller vårt nyckelvalv. I [Komma igång-självstudiekursen](key-vault-get-started.md) heter den här resursgruppen **ContosoResourceGroup** och vi kommer även att fortsätta att använda platsen East Asia (Östasien). Ersätt värdena med dina egna efter behov:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> Om du vill använda ett befintligt lagringskonto måste det använda samma prenumeration som ditt nyckelvalv samt använda Resource Manager-distributionsmodellen i stället för den klassiska distributionsmodellen.
> 
> 

## <a name="a-ididentifyaidentify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identifiera nyckelvalvet för dina loggar
I Komma igång-självstudien heter nyckelvalvet **ContosoKeyVault** och vi fortsätter att använda det namnet och lagrar informationen i en variabel med namnet **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a name="a-idenableaenable-logging"></a><a id="enable"></a>Aktivera loggning
För att aktivera loggning för nyckelvalvet ska vi använda cmdleten Set-AzureRmDiagnosticSetting tillsammans med variabeln som vi skapade för vårt nya lagringskonto och vårt nyckelvalv. Vi kan också ange flaggan **-Enabled** till **$true** och ange kategorin till AuditEvent (den enda kategorin för Nyckelvalvloggning):

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

Följande utdata returneras för detta:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Detta bekräftar att loggning är aktiverat för nyckelvalvet och att information sparas i ditt lagringskonto.

Du kan också ange bevarandeprincip för loggar så att äldre loggar tas bort automatiskt. Ange till exempel bevarandeprincip genom att ange flaggan **- RetentionEnabled** till **$true** och ange parametern **- RetentionInDays** till **90** så att de loggar som är äldre än 90 dagar tas bort automatiskt.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Vad loggas:

* Alla autentiserade REST-API-förfrågningar loggas, vilket omfattar förfrågningar som misslyckats på grund av åtkomstbehörigheter, systemfel eller ogiltiga förfrågningar.
* Åtgärder i själva nyckelvalvet, t.ex. att skapa, ta bort eller konfigurera åtkomstprinciper för nyckelvalvet eller att uppdatera attribut för nyckelvalvet, t.ex. taggar.
* Åtgärder med nycklar och hemligheter i nyckelvalvet, t.ex. att skapa, ändra eller ta bort nycklar eller hemligheter; åtgärder som att signera, verifiera, kryptera, dekryptera, omsluta eller ta bort en nyckelomslutning, hämta hemligheter, visa nycklar och hemligheter samt deras versioner.
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Till exempel förfrågningar som inte har någon ägartoken, som är felaktiga, som har upphört att gälla eller som har en ogiltig token.  

## <a name="a-idaccessaaccess-your-logs"></a><a id="access"></a>Komma åt loggarna
Loggarna för nyckelvalvet lagras i behållaren **insights-logs-auditevent** i det lagringskonto som du angav. Om du vill visa alla blobbar i den här behållaren skriver du:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Följande utdata returneras för detta:

**Behållarens URI: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Namn**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

Som du ser i dessa utdata följer blobbarna en namngivningskonvention: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filnamn.json**

Datum- och tidsvärdena använder UTC.

Eftersom samma lagringskonto kan användas för att samla in loggar för flera resurser är det fullständiga resurs-ID:t i blobbnamnet mycket användbart för att komma åt eller hämta endast de blobbar som du behöver. Men innan vi gör det ska vi titta på hur du hämtar alla blobbar.

Börja med att skapa en mapp som du vill ladda ned blobbarna till. Till exempel:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Hämta sedan en lista över alla blobbar:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Skicka den här listan via ”Get-AzureStorageBlobContent” för att ladda ned blobbarna till målmappen:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

När du kör det här andra kommandot skapar **/**-avgränsaren i blobbnamnen en fullständig mappstruktur under målmappen. Den här strukturen används för att hämta och spara blobbarna som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Till exempel:

* Om du har flera nyckelvalv och bara vill hämta loggar för ett av dem, mer specifikt nyckelvalvet CONTOSOKEYVAULT3:
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* Om du har flera resursgrupper och bara vill hämta loggar för en av dem använder du `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* Om du vill hämta alla loggar för januari 2016 använder du `-Blob '*/year=2016/m=01/*'`:
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Nu är det dags att börja titta på vad som finns i loggarna. Men innan vi går vidare till det ska vi nämna ytterligare två parametrar för Get-AzureRmDiagnosticSetting som det kan vara bra att känna till:

* Om du vill fråga efter statusen för nyckelvalvsresursens diagnostikinställningar: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Om du vill inaktivera loggning för nyckelvalvsresursen: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a name="a-idinterpretainterpret-your-key-vault-logs"></a><a id="interpret"></a>Tolka Key Vault-loggarna
Enskilda blobbar lagras som text, formaterad som en JSON-blobb. Det här är ett exempel på en loggpost från `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

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


Följande tabell innehåller fältnamnen och beskrivningarna.

| Fältnamn | Beskrivning |
| --- | --- |
| time |Datum och tid (UTC). |
| resourceId |Azure Resource Manager Resource-ID. För Key Vault-loggar är detta alltid nyckelvalvets resurs-ID. |
| operationName |Namnet på åtgärden, som beskrivs i nästa tabell. |
| operationVersion |Det här är REST-API-versionen som begärs av klienten. |
| category |För Key Vault-loggar är AuditEvent det enda, tillgängliga värdet. |
| resultType |Resultatet av REST-API-begäran. |
| resultSignature |HTTP-status. |
| resultDescription |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| durationMs |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| callerIpAddress |IP-adressen för klienten som gjorde begäran. |
| correlationId |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| identity |Identiteten från den token som angavs när REST-API-begäran gjordes. Detta är vanligtvis en ”användare”, ”tjänstens huvudnamn” eller en kombination av ”användare+app-ID”, till exempel då en begäran är resultatet av en Azure PowerShell-cmdlet. |
| properties |Vilken information som visas i det här fältet beror på åtgärden (operationName). I de flesta fall innehåller det klientinformation (useragent-strängen som skickas av klienten), REST-API-begärans exakta URI och HTTP-statuskoden. När ett objekt returneras som ett resultat av en begäran (till exempel KeyCreate eller VaultGet) innehåller det även nyckelns URI (som ”id”), valvets URI eller hemlighetens URI. |

**operationName**-fältvärdena har ObjectVerb-format. Till exempel:

* Alla åtgärder med ett nyckelvalv har formatet ”Vault`<action>`”, till exempel `VaultGet` och `VaultCreate`.
* Alla åtgärder med nycklar har formatet ”Key`<action>`”, till exempel `KeySign` och `KeyList`.
* Alla åtgärder med hemligheter har formatet ”secret`<action>`”, till exempel `SecretGet` och `SecretListVersions`.

Följande tabell innehåller operationName och motsvarande REST-API-kommando.

| operationName | REST-API-kommando |
| --- | --- |
| Autentisering |Via Azure Active Directory-slutpunkt |
| VaultGet |[Hämta information om ett nyckelvalv](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx) |
| VaultPut |[Skapa eller uppdatera ett nyckelvalv](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx) |
| VaultDelete |[Ta bort ett nyckelvalv](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx) |
| VaultPatch |[Uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Visa en lista med alla nyckelvalv i en resursgrupp](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx) |
| KeyCreate |[Skapa en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx) |
| KeyGet |[Hämta information om en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx) |
| KeyImport |[Importera en nyckel till ett valv](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx) |
| KeyBackup |[Säkerhetskopiera en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx). |
| KeyDelete |[Ta bort en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx) |
| KeyRestore |[Återställa en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx) |
| KeySign |[Signera med en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx) |
| KeyVerify |[Verifiera med en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx) |
| KeyWrap |[Omsluta en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx) |
| KeyUnwrap |[Ta bort en nyckelomslutning](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx) |
| KeyEncrypt |[Kryptera med en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx) |
| KeyDecrypt |[Dekryptera med en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx) |
| KeyUpdate |[Uppdatera en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx) |
| KeyList |[Visa en lista med nycklarna i ett valv](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx) |
| KeyListVersions |[Visa en lista över versionerna av en nyckel](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx) |
| SecretSet |[Skapa en hemlighet](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx) |
| SecretGet |[Hämta en hemlighet](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx) |
| SecretUpdate |[Uppdatera en hemlighet](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx) |
| SecretDelete |[Ta bort en hemlighet](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx) |
| SecretList |[Visa en lista över hemligheterna i ett valv](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx) |
| SecretListVersions |[Visa en lista över versionerna av en hemlighet](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx) |

## <a name="a-idloganalyticsause-log-analytics"></a><a id="loganalytics"></a>Använda Log Analytics

Du kan använda Azure Key Vault-lösningen i Log Analytics för att läsa igenom AuditEvent-loggarna i Azure Key Vault. Mer information och information om hur du konfigurerar detta finns i [Azure Key Vault i Log Analytics](../log-analytics/log-analytics-azure-key-vault.md). I artikeln hittar du dessutom anvisningar ifall du behöver migrera från den gamla Key Vault-lösningen som fanns i förhandsversionen av Log Analytics. Där började du med att dirigera loggarna till ett Azure Storage-konto och konfigurerade Log Analytics till att läsa därifrån.

## <a name="a-idnextanext-steps"></a><a id="next"></a>Nästa steg
En självstudiekurs där Azure Key Vault används i en webbapp finns i [Använda Azure Key Vault från en webbapp](key-vault-use-from-web-application.md).

Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).

En lista med Azure PowerShell 1.0-cmdlets för Azure Key Vault finns i [Cmdlets för Azure Key Vault](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Självstudierna om nyckelrotering och logggranskning med Azure Key Vault finns i [Ställa in Key Vault med heltäckande nyckelrotering och granskning](key-vault-key-rotation-log-monitoring.md).


