---
title: Integrera loggar från Azure Key Vault med hjälp av Event Hubs | Microsoft Docs
description: Självstudiekurs som innehåller de nödvändiga stegen för att Key Vault-loggar tillgängliga för en SIEM med hjälp av Azure Log Integration
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 790f49f0fae98162b3443c78d813b1070e4514a0
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303895"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Självstudie för Azure Log Integration: Bearbeta händelser med Azure Key Vault med hjälp av Event Hubs

>[!IMPORTANT]
> Funktionen Azure Log integration upphör att gälla genom 06/01/2019. AzLog hämtningar har inaktiverats på den 27 juni 2018. För information om vad du gör Flytta framåt granska inlägget [Använd Azure monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Du kan använda Azure Log Integration för att hämta loggade händelser och göra dem tillgängliga för din säkerhet och händelsehantering (SIEM) hanteringssystemet. Den här självstudien visar ett exempel på hur Azure Log Integration kan användas för att bearbeta loggar som skaffas genom Azure Event Hubs.

Den bästa metoden för att integrera Azure loggar är genom att använda din SIEM-leverantör Azure Monitor-koppling och följa de här [instruktioner](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Men om din SIEM-leverantör inte tillhandahåller en anslutning till Azure Monitor, du kan använda Azure Log Integration som en tillfällig lösning (om din siem-server som stöds av Azure Log Integration) tills en sådan anslutning är tillgänglig.

 
Använd den här självstudiekursen för att bekanta dig med hur Azure Log Integration och Händelsehubbar tillsammans genom att följa stegen exempel och förstå hur varje steg stöder lösningen. Du kan sedan ta vad du har lärt oss här att skapa dina egna steg för att stödja ditt företags unika krav.

>[!WARNING]
De steg och kommandon i den här självstudien är inte avsedda att kopieras och klistras in. Det är bara exempel. Använd inte PowerShell-kommandon ”i befintligt skick” i live-miljön. Du måste anpassa dem baserat på din miljö.


Den här självstudien vägleder dig genom processen för att möjliggöra Azure Key Vault-aktivitet som loggas till en händelsehubb och gör den tillgänglig som JSON-filer till din SIEM-system. Sedan kan du konfigurera din SIEM-system för att bearbeta JSON-filerna.

>[!NOTE]
>De flesta av stegen i den här självstudien omfattar konfigurera nyckelvalv, lagringskonton och händelsehubbar. Vilka specifika åtgärder i Azure Log Integration finns i slutet av den här självstudien. Utför inte de här stegen i en produktionsmiljö. De är avsedda för en labbmiljö. Du måste anpassa stegen innan du använder dem i produktion.

Informationen på vägen hjälper dig att förstå orsaker bakom varje steg. Länkar till andra artiklar får du mer information om vissa ämnen.

Mer information om de tjänster som nämns i den här självstudien finns: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure-Loggintegrering](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Första installation

Innan du kan slutföra stegen i den här artikeln behöver du följande:

1. En Azure-prenumeration och konto för den prenumerationen med administratörsbehörighet. Om du inte har en prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/).
 
1. Ett system med åtkomst till internet som uppfyller kraven för att installera Azure Log Integration. Systemet kan vara på en tjänst i molnet eller lokala.

1. Azure Log Integration installerad. Att installera den:

   a. Använda Fjärrskrivbord för att ansluta till system som nämns i steg 2.   
   b. Kopiera installationsprogrammet för Azure Log Integration i systemet. c. Starta installationsprogrammet och acceptera licensvillkoren för programvara från Microsoft.

1. Om du tillhandahåller telemetriinformation, lämnar du kryssrutan är markerad. Om du inte skulle hellre skicka användningsinformation till Microsoft, avmarkerar du kryssrutan.

   Läs mer om Azure Log Integration och hur du installerar den [Azure Log Integration med Azure Diagnostisk loggning och vidarebefordran av Windows-händelser](security-azure-log-integration-get-started.md).

1. Den senaste versionen av PowerShell.

   Om du har Windows Server 2016 har installerats kommer du ha minst PowerShell 5.0. Om du använder någon annan version av Windows Server, kanske en tidigare version av PowerShell installerad. Du kan kontrollera versionen genom att ange ```get-host``` i ett PowerShell-fönster. Om du inte har PowerShell 5.0 installerat, kan du [ladda ned den](https://www.microsoft.com/download/details.aspx?id=50395).

   När du har minst PowerShell 5.0, som du kan fortsätta att installera den senaste versionen:

   a. I en PowerShell-fönstret anger du den ```Install-Module Azure``` kommando. Slutför installationsstegen.    
   b. Ange den ```Install-Module AzureRM``` kommando. Slutför installationsstegen.

   Mer information finns i [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Skapa stödjande infrastrukturelement

1. Öppna en upphöjd PowerShell-kommandotolk och gå till **C:\Program Files\Microsoft Azure Log Integration**.
1. Importera AzLog-cmdlets genom att köra skriptet LoadAzLogModule.ps1. Ange den `.\LoadAzLogModule.ps1` kommando. (Observera de ”. \" i det kommandot.) Du bör se något liknande följande:</br>

   ![Lista med inlästa moduler](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Ange den `Connect-AzureRmAccount` kommando. I inloggningsfönstret anger du autentiseringsuppgifterna för den prenumeration som du ska använda för den här självstudien.

   >[!NOTE]
   >Om det är första gången som du loggat in till Azure från den här datorn visas ett meddelande om att tillåta Microsoft att samla in användningsdata för PowerShell. Vi rekommenderar att du aktiverar den här Datasamlingen eftersom den används för att förbättra Azure PowerShell.

1. Du har loggat in efter en lyckad autentisering, så ser du informationen i följande skärmbild. Anteckna prenumerations-ID och namn på prenumeration, eftersom du behöver dem att slutföra senare steg.

   ![PowerShell-fönster](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
1. Skapa variabler för att lagra värden som ska användas senare. Ange var och en av följande PowerShell-rader. Du kan behöva justera värden som matchar din miljö.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (Ditt prenumerationsnamn kan vara annorlunda. Du kan se det som en del av utdata från föregående kommando.)
    - ```$location = 'West US'``` (Den här variabeln används för att skicka den plats där resurser ska skapas. Du kan ändra den här variabeln för att vara vilken plats som du väljer.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Namnet kan vara vad som helst, men den bör innehålla endast gemener och siffror.)
    - ``` $storageName = $name``` (Den här variabeln används för lagringskontonamnet.)
    - ```$rgname = $name ``` (Den här variabeln används för resursgruppens namn.)
    - ``` $eventHubNameSpaceName = $name``` (Detta är namnet på namnområdet för event hub.)
1. Ange den prenumeration som du kommer att arbeta med:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
1. Skapa en resursgrupp:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Om du anger `$rg` då du bör se utdata som liknar denna skärmbild:

   ![Utdata efter skapandet av en resursgrupp](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Skapa ett lagringskonto som används för att hålla reda på statusinformation:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Skapa händelsehubbens namnområde. Detta är nödvändigt att skapa en event hub.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Hämta regel-ID som ska användas med insights-providern:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
1. Hämta alla möjliga Azure-platser och lägga till namn till en variabel som kan användas i ett senare steg:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Om du anger `$locations` nu kan du se platsnamn utan ytterligare information som returneras av Get-AzureRmLocation.
1. Skapa en loggprofil för Azure Resource Manager: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Mer information om Azure log-profilen finns i [översikt över Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Du kan få ett felmeddelande när du försöker skapa en loggprofil för. Du kan sedan granska dokumentationen för Get-AzureRmLogProfile och ta bort AzureRmLogProfile. Om du kör Get-AzureRmLogProfile kan se du information om loggprofil. Du kan ta bort den befintliga log-profilen genom att ange den ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` kommando.
>
>![Resource Manager-profilfel](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

1. Skapa key vault:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

1. Konfigurera loggning för key vault:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Generera loggaktivitet

Begäranden måste skickas till Key Vault för att generera loggaktivitet. Åtgärder som nyckelgenerering, lagra hemligheter, eller läsa hemligheter från Nyckelvalvet skapar loggposter.

1. Visa aktuella lagringsnycklar:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generera en ny **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Visa nycklarna igen och ser till att **key2** innehåller ett annat värde:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Ange och läsa en hemlighet för att generera ytterligare poster:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Returnerade hemliga](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurera Azure-Loggintegrering

Nu när du har konfigurerat de obligatoriska elementen för Key Vault-loggning till en händelsehubb, måste du konfigurera Azure Log Integration:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ``` $storagekey = $storagekeys[0].Value```

Kör kommandot AzLog för varje händelsehubb:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Du bör se JSON-filer som skapas efter en minut för att köra de sista två kommandona. Du kan bekräfta att genom att övervaka katalogen **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Nästa steg

- [Azure-Loggintegrering vanliga frågor och svar](security-azure-log-integration-faq.md)
- [Kom igång med Azure Log Integration](security-azure-log-integration-get-started.md)
- [Integrera loggar från Azure-resurser i dina SIEM system](security-azure-log-integration-overview.md)
