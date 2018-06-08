---
title: Integrera loggar från Azure Key Vault med Händelsehubbar | Microsoft Docs
description: Självstudiekurs som innehåller de nödvändiga stegen för att Key Vault loggar tillgängliga för en SIEM med hjälp av Azure Log-integrering
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 06/06/2018
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 9065d237057bf47d469b6f6fbf5b588c7e348232
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839365"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration Självstudier: processen Azure Key Vault-händelser med hjälp av Händelsehubbar


>[!IMPORTANT]
> Funktionen Azure logganalys integration att bli inaktuell av 2019-06/01.  Att kommer inaktiveras AzLog hämtningar av 27 Jun 2018. För information om vad du gör glidande vidarebefordra granskning efter [Använd Azure Övervakaren för integrering med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Du kan använda Azure Log-integrering för att hämta loggade händelser och göra dem tillgängliga för din information och händelse (SIEM) hanteringssystem för informationssäkerhet. Den här kursen visar ett exempel på hur Azure Log-integrering kan användas för att bearbeta loggar som skaffas genom Azure Event Hubs.

Den bästa metoden för att integrera Azure loggar är med hjälp av leverantören SIEM Azure-Monitor koppling och följande [instruktioner](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Men om din SIEM-leverantör inte ger en koppling till Azure-Monitor, du kan använda Azure Log-integrering som en tillfällig lösning (om din SIEM stöds av Azure Log-integrering) tills en sådan anslutning är tillgänglig.

 
Använd den här självstudiekursen för att bekanta dig med hur Azure Log-integrering och Händelsehubbar kan fungera tillsammans med följande exempel och förstå hur varje steg stöder lösningen. Du kan sedan ta vad du har lärt dig här för att skapa egna steg för att stödja ditt företags unika krav.

>[!WARNING]
Åtgärder och kommandon i den här kursen är inte avsedda att kopieras och klistras in. Det är bara exempel. Använd inte PowerShell-kommandon ”i befintligt skick” i live-miljön. Du måste anpassa dem utifrån din miljö.


Den här självstudiekursen vägleder dig genom processen för att Azure Key Vault-aktivitet som loggas på en händelsehubb och göra den tillgänglig som JSON-filer för SIEM-system. Du kan sedan konfigurera SIEM-system för att bearbeta JSON-filer.

>[!NOTE]
>De flesta av stegen i den här kursen omfattar konfigurera nyckelvalv, lagringskonton och händelsehubbar. Hur Azure Log-integrering finns i slutet av den här kursen. Utför inte de här stegen i en produktionsmiljö. De är avsedda för en labbmiljö. Du måste anpassa instruktionerna innan du använder dem i produktion.

Informationen på vägen hjälper dig att förstå skälen till varje steg. Länkar till andra artiklar får du mer information om vissa ämnen.

Mer information om de tjänster som nämns i den här kursen finns: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure logganalys-integrering](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Installationen

Innan du kan slutföra stegen i den här artikeln, behöver du följande:

1. En Azure-prenumeration och konto i den prenumerationen med administratörsrättigheter. Om du inte har en prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/).
 
2. Ett system med åtkomst till internet som uppfyller kraven för att installera Azure Log-integrering. Systemet kan vara på en molnbaserad tjänst eller lagras lokalt.

3. [Azure Log-integrering](https://www.microsoft.com/download/details.aspx?id=53324) installerad. Så här installerar du det:

   a. Använda Fjärrskrivbord för att ansluta till system som anges i steg 2.   
   b. Kopiera installationsprogrammet för Azure Log-integrering i systemet. Du kan [ladda ned installationsfilerna](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Starta installationsprogrammet och acceptera Licensvillkor för programvara från Microsoft.   
   d. Om du ger telemetri information, lämnar du kryssrutan är markerad. Om du inte skulle istället skicka användningsinformation till Microsoft, avmarkerar du kryssrutan.
   
   Läs mer om Azure Log-integrering och hur du installerar det [Azure Log integrering med Azure diagnostikloggning och vidarebefordran av Windows-händelse](security-azure-log-integration-get-started.md).

4. Den senaste versionen i PowerShell.
 
   Om du har Windows Server 2016 installerad, så att du har minst PowerShell 5.0. Om du använder någon annan version av Windows Server, kanske en tidigare version av PowerShell som är installerad. Du kan kontrollera versionen genom att ange ```get-host``` i PowerShell-fönstret. Om du inte har PowerShell 5.0 installerat, kan du [ladda ned den](https://www.microsoft.com/download/details.aspx?id=50395).

   När du har minst PowerShell 5.0, du kan fortsätta att installera den senaste versionen:
   
   a. Ange i ett PowerShell-fönster i ```Install-Module Azure``` kommando. Slutför följande steg.    
   b. Ange den ```Install-Module AzureRM``` kommando. Slutför följande steg.

   Mer information finns i [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Skapa stödjande infrastrukturelement

1. Öppna ett upphöjt PowerShell-fönster och gå till **C:\Program Files\Microsoft Azure Log integrering**.
2. Importera AzLog-cmdlets genom att köra skriptet LoadAzLogModule.ps1. Ange den `.\LoadAzLogModule.ps1` kommando. (Observera den ”. \” i det aktuella kommandot.) Du bör se något liknande följande:</br>

   ![Lista med inlästa moduler](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Ange den `Connect-AzureRmAccount` kommando. Ange autentiseringsuppgifter för den prenumeration som du ska använda för den här kursen i inloggningsfönstret.

   >[!NOTE]
   >Om det här är första gången du loggar till Azure från den här datorn visas ett meddelande om att tillåta Microsoft att samla in användningsdata för PowerShell. Vi rekommenderar att du aktiverar den här Datasamlingen eftersom den används för att förbättra Azure PowerShell.

4. Du är inloggad och informationen i följande skärmbild visas efter en lyckad autentisering. Anteckna prenumerations-ID och namn, eftersom du behöver dem för att slutföra senare steg.

   ![PowerShell-fönster](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Skapa variabler för att lagra värden som kommer att användas senare. Ange var och en av följande rader i PowerShell. Du kan behöva justera värden som stämmer överens din miljö.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (Namnet på din prenumeration kan vara olika. Du kan se det som en del av utdata från det föregående kommandot.)
    - ```$location = 'West US'``` (Den här variabeln används för att skicka den plats där resurser ska skapas. Du kan ändra den här variabeln för att vara vilken plats som du väljer.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Namnet kan vara allt, men den bör innehålla endast små bokstäver och siffror.)
    - ``` $storageName = $name``` (Den här variabeln används för lagringskontonamnet.)
    - ```$rgname = $name ``` (Den här variabeln används för resursgruppens namn.)
    - ``` $eventHubNameSpaceName = $name``` (Detta är namnet på namnområdet event hub.)
6. Ange den prenumeration som du kommer att arbeta med:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Skapa en resursgrupp:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Om du anger `$rg` du bör nu se utdata som liknar denna skärmbild:

   ![Utdata när den har skapats av en resursgrupp](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Skapa ett lagringskonto som ska användas för att hålla reda på statusinformation:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Skapa namnområdet event hub. Detta är nödvändigt att skapa en händelsehubb.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Hämta regel-ID som ska användas med insikter providern:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Hämta alla möjliga Azure platser och lägga till namn till en variabel som kan användas i ett senare steg:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Om du anger `$locations` nu visas platsnamnen utan ytterligare information som returneras av Get-AzureRmLocation.
12. Skapa en profil för Azure Resource Manager-loggen: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Mer information om Azure logganalys-profilen finns [översikt över Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> Du kan få ett felmeddelande när du försöker skapa en profil för loggen. Du kan granska dokumentationen för Get-AzureRmLogProfile och ta bort AzureRmLogProfile. Om du kör Get-AzureRmLogProfile finns information om profilen för loggen. Du kan ta bort den befintliga profilen för loggfilen genom att ange den ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` kommando.
>
>![Fel för Resource Manager-profil](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

1. Skapa nyckelvalvet:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Konfigurera loggning för nyckelvalvet:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Generera loggaktivitet

Förfrågningar måste skickas till Key Vault för att generera loggfilen aktivitet. Åtgärder som nyckelgenerering, lagring av hemligheter, eller läsa hemligheter från Nyckelvalvet skapas loggposter.

1. Visa de aktuella lagringsnycklar:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Generera en ny **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Visa nycklarna igen och se som **key2** innehåller ett annat värde:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Ange och läsa en hemlighet för att generera ytterligare loggposter:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Returnerade hemliga](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurera Azure logganalys-integrering

Nu när du har konfigurerat de element som krävs för Key Vault-loggning till en händelsehubb, måste du konfigurera Azure Log-integrering:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Kör kommandot AzLog för varje händelsehubb:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Du bör se JSON-filer som skapas efter en minut eller så körs de sista två kommandona. Du kan bekräfta att genom att övervaka katalogen **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Nästa steg

- [Azure logganalys Integration vanliga frågor och svar](security-azure-log-integration-faq.md)
- [Kom igång med Azure Log-integrering](security-azure-log-integration-get-started.md)
- [Integrera loggar från Azure-resurser i din SIEM-system](security-azure-log-integration-overview.md)
