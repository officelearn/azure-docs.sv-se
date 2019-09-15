---
title: Integrera loggar från Azure Key Vault med Event Hubs | Microsoft Docs
description: Själv studie kurs som innehåller de steg som krävs för att göra Key Vault-loggar tillgängliga för en SIEM med hjälp av Azure Log Integration
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004118"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration själv studie kurs: Bearbeta Azure Key Vault händelser med Event Hubs

>[!IMPORTANT]
> Funktionen Azure logg integrering kommer att föråldras med 06/15/2019. AzLog hämtningar inaktiverades den 27 juni 2018. Information om vad du kan göra när du flyttar framåt finns i [använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Du kan använda Azure Log Integration för att hämta loggade händelser och göra dem tillgängliga för ditt SIEM-system (Security information and Event Management). I den här självstudien visas ett exempel på hur Azure Log Integration kan användas för att bearbeta loggar som förvärvas via Azure Event Hubs.

Den bästa metoden för att integrera Azure-loggar är att använda din SIEM-leverantörs Azure Monitor-anslutning och följa dessa [anvisningar](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Men om din SIEM-leverantör inte tillhandahåller någon koppling till Azure Monitor kan du kanske använda Azure Log Integration som en tillfällig lösning (om din SIEM stöds av Azure Log Integration) tills en sådan koppling är tillgänglig.

 
Använd den här självstudien för att bekanta dig med hur Azure Log Integration och Event Hubs fungerar tillsammans genom att följa anvisningarna i exemplet och förstå hur varje steg stöder lösningen. Sedan kan du ta det du har lärt dig här för att skapa egna steg som stöder företagets unika krav.

> [!WARNING]
> Stegen och kommandona i den här självstudien är inte avsedda att kopieras och klistras in. De är bara exempel. Använd inte PowerShell-kommandona "i befintligt skick" i din Live-miljö. Du måste anpassa dem baserat på din unika miljö.


Den här självstudien vägleder dig genom processen att ta Azure Key Vault aktivitet som loggats till en händelsehubben och göra den tillgänglig som JSON-filer till ditt SIEM-system. Sedan kan du konfigurera SIEM-systemet för att bearbeta JSON-filerna.

>[!NOTE]
>De flesta av stegen i den här självstudien omfattar konfigurering av nyckel valv, lagrings konton och händelse nav. De särskilda Azure Log Integration stegen finns i slutet av den här självstudien. Utför inte de här stegen i en produktions miljö. De är endast avsedda för en labb miljö. Du måste anpassa stegen innan du använder dem i produktion.

Information som tillhandahålls på vägen hjälper dig att förstå orsakerna bakom varje steg. Länkar till andra artiklar ger dig mer information om vissa ämnen.

Mer information om de tjänster som nämns i den här kursen finns i: 

- [Azure Key Vault](/azure/key-vault/key-vault-overview)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Azure Log Integration](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Första installation

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Innan du kan slutföra stegen i den här artikeln behöver du följande:

* En Azure-prenumeration och ett konto för den prenumerationen med administratörs behörighet. Om du inte har någon prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/).
 
* Ett system med åtkomst till Internet som uppfyller kraven för installation av Azure Log Integration. Systemet kan finnas i en moln tjänst eller finnas lokalt.

* Azure Log Integration installerat. Så här installerar du det:

   a. Använd fjärr skrivbord för att ansluta till det system som anges i steg 2.   
   b. Kopiera installations programmet för Azure Log Integration till systemet. c. Starta installations programmet och godkänn licens villkoren för program vara från Microsoft.

* Lämna kryss rutan markerad om du vill tillhandahålla telemetri information. Om du hellre inte vill skicka användnings information till Microsoft avmarkerar du kryss rutan.

   Mer information om Azure Log Integration och hur du installerar den finns i [Azure log integration med Azure-diagnostik loggning och vidarebefordran av Windows-händelser](azure-log-integration-get-started.md).

* Den senaste versionen av PowerShell.

   Om du har Windows Server 2016 installerat har du minst PowerShell 5,0. Om du använder någon annan version av Windows Server kanske du har en tidigare version av PowerShell installerad. Du kan kontrol lera versionen genom att ```get-host``` ange i ett PowerShell-fönster. Om du inte har PowerShell 5,0 installerat kan du [Ladda ned det](https://www.microsoft.com/download/details.aspx?id=50395).

   När du har minst PowerShell 5,0 kan du fortsätta med att installera den senaste versionen genom att följa anvisningarna i [installera Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Skapa stöd för infrastruktur element

1. Öppna ett upphöjt PowerShell-fönster och gå till **C:\Program Files\Microsoft Azure log integration**.
1. Importera AzLog-cmdletarna genom att köra skriptet LoadAzLogModule. ps1. `.\LoadAzLogModule.ps1` Ange kommandot. (Observera ". \" i det kommandot.) Du bör se något liknande följande:</br>

   ![Lista över inlästa moduler](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. `Connect-AzAccount` Ange kommandot. I inloggnings fönstret anger du information om autentiseringsuppgifter för den prenumeration som du ska använda för den här självstudien.

   >[!NOTE]
   >Om det här är första gången du loggar in på Azure från den här datorn visas ett meddelande om att Microsoft kan samla in PowerShell-användnings data. Vi rekommenderar att du aktiverar den här data insamlingen eftersom den kommer att användas för att förbättra Azure PowerShell.

1. Efter en lyckad autentisering är du inloggad. Anteckna prenumerations-ID och prenumerations namn eftersom du behöver dem för att slutföra senare steg.

1. Skapa variabler för att lagra värden som ska användas senare. Ange var och en av följande PowerShell-rader. Du kan behöva justera värdena för att matcha din miljö.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(Ditt prenumerations namn kan vara ett annat. Du kan se det som en del av utdata från föregående kommando.)
    - ```$location = 'West US'```(Den här variabeln kommer att användas för att skicka platsen där resurser ska skapas. Du kan ändra den här variabeln till valfri plats där du väljer.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(Namnet kan vara något, men det får bara innehålla gemener och siffror.)
    - ```$storageName = $name```(Den här variabeln kommer att användas för lagrings kontots namn.)
    - ```$rgname = $name```(Den här variabeln kommer att användas för resurs gruppens namn.)
    - ```$eventHubNameSpaceName = $name```(Det här är namnet på Event Hub-namnområdet.)
1. Ange den prenumeration som du kommer att arbeta med:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Skapa en resursgrupp:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Om du anger `$rg` det här läget bör du se utdata som liknar den här skärm bilden:

   ![Utdata efter att en resurs grupp har skapats](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Skapa ett lagrings konto som ska användas för att hålla reda på statusinformation:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Skapa namn området för händelsehubben. Detta krävs för att skapa en Event Hub.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Hämta regel-ID: t som ska användas med insikts leverantören:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Hämta alla möjliga Azure-platser och Lägg till namnen i en variabel som kan användas i ett senare steg:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Om du anger `$locations` det här läget visas plats namnen utan den ytterligare information som returneras av Get-AzLocation.
1. Skapa en Azure Resource Manager logg profil: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Mer information om Azures logg profil finns i [Översikt över Azure aktivitets loggen](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Du kan få ett fel meddelande när du försöker skapa en logg profil. Sedan kan du läsa dokumentationen för Get-AzLogProfile och Remove-AzLogProfile. Om du kör get-AzLogProfile visas information om logg profilen. Du kan ta bort den befintliga logg profilen genom att ```Remove-AzLogProfile -name 'Log Profile Name'``` ange kommandot.
>
>![Resurs hanterarens profil fel](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

1. Skapa nyckel valvet:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Konfigurera loggning för nyckel valvet:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Generera logg aktivitet

Begär Anden måste skickas till Key Vault för att generera logg aktivitet. Åtgärder som att skapa nycklar, spara hemligheter eller läsa hemligheter från Key Vault skapar logg poster.

1. Visa de aktuella lagrings nycklarna:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generera en ny **key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Visa nycklarna igen och se att **key2** innehåller ett annat värde:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Ange och Läs en hemlighet för att generera ytterligare logg poster:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Returnerade hemlighet](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurera Azure Log Integration

Nu när du har konfigurerat alla nödvändiga element för att Key Vault loggning till en händelsehubben måste du konfigurera Azure Log Integration:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Kör AzLog-kommandot för varje händelsehubben:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Efter en minut eller så att du kan köra de senaste två kommandona bör du se JSON-filer som genereras. Du kan bekräfta detta genom att övervaka katalogen **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Nästa steg

- [Azure Log Integration vanliga frågor och svar](azure-log-integration-faq.md)
- [Kom igång med Azure Log Integration](azure-log-integration-get-started.md)
- [Integrera loggar från Azure-resurser i dina SIEM-system](azure-log-integration-overview.md)
