---
title: Apptjänst på Azure-stacken uppdatering 1 viktig information | Microsoft Docs
description: Lär dig mer om vad som finns i en uppdatering för Apptjänst Azure stacken kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: fedf511e06243d5c0652e422b397bb00da3b42c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Apptjänst på Azure-stacken uppdatering 1 viktig information

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure-stacken uppdatering 1 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1802 Azure Stack integrerade systemet eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service.
>
>

## <a name="build-reference"></a>Skapa referens

Tjänsten App på Azure-stacken uppdatering 1 build-nummer är **69.0.13698.9**

### <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Nya distributioner av Azure App Service på Azure-stacken kräver en [tre ämne jokerteckencertifikat](azure-stack-app-service-before-you-get-started.md#get-certificates) på grund av förbättringar i hanteringen av där enkel inloggning för Kudu är nu i Azure App Service. Det nya ämnet är ** *.sso.appservice.<region>.<domainname>.<extension>**
>
>

Referera till den [innan du börjar dokumentationen](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service på Azure-stacken uppdatering 1 innehåller följande förbättringar och korrigeringar:

- **Hög tillgänglighet i Azure App Service** -det Azure-stacken 1802 update har aktiverats arbetsbelastningar som ska distribueras över fault-domäner. Därför kan Apptjänst infrastruktur vara feltolerant som kommer att distribueras över feldomäner. Som standard har alla nya distributioner av Azure App Service funktionen men distributioner som har slutförts innan Azure Stack 1802 som uppdateringen finns i den [Feldomän för App Service-dokumentation](azure-stack-app-service-fault-domain-update.md)

- **Distribuera i befintligt virtuellt nätverk** -kunder kan nu distribuera Apptjänst Azure stacken inom ett befintligt virtuellt nätverk. Distribution i ett befintligt virtuellt nätverk ger kunder möjlighet att ansluta till SQL Server och filserver, krävs för Azure App Service via privata portar. Under distributionen kan kunder kan välja för att distribuera i ett befintligt virtuellt nätverk, men [skapa undernät för användning av App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) före distributionen.

- Uppdateringar för **App Service-klient, Admin funktioner portaler och Kudu verktyg**. Konsekvent med Azure-stacken Portal SDK-version.

- **Uppdateringar för följande ramverk för programmet och verktyg**:
    - Lägga till **.Net Core 2.0** stöd
    - Lägga till **Node.JS** versioner:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Lägga till **NPM** versioner:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Lägga till **PHP** uppdateringar:
        - 5.6.32
        - 7.0.26 (x86 och x64)
        - 7.1.12 (x86 och x64)
    - Uppdatera **Git för Windows** v 2.14.1
    - Uppdatera **ett** till v4.5.0

  - Tillagt stöd för **endast HTTPS** funktion inom anpassad domän funktion i App Service-klient-portalen. 

  - Tillagda verifiering av anslutning till lagringsplatsen i Väljaren anpassad lagring för Azure Functions 

#### <a name="fixes"></a>Korrigeringar

- När du skapar ett offline distributionspaket får kunder inte längre ett åtkomst nekad när öppna mappen från installationsprogrammet för App Service

- Lösa problem när du arbetar i funktionen anpassade domäner i App Service-klient-portalen.

- Förhindra att kunder som använder reserverade administratör namn under installationen

- Aktiverad App Service-distributionen med **domänanslutna** filserver

- Förbättrad hämtning av Azure-stacken root certifikat i skript och nu Validera cert rot i installationsprogrammet för App Service.

- Fast felaktig status som returneras till Azure Resource Manager när en prenumeration är ta bort de befintliga resurserna i namnområdet Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Kända problem med distributionen

- Validering av certifikatfel

Vissa kunder ha uppstått problem när de tillhandahåller certifikat till installationsprogrammet för App Service när du distribuerar på ett integrerat system på grund av mycket begränsad verifiering i installationsprogrammet. Installationsprogrammet för App Service har publicerats igen, kunder bör [hämta uppdaterade installationsprogrammet](https://aka.ms/appsvconmasinstaller). Kontakta supporten om du fortsätter att ha problem med att verifiera certifikat med uppdaterade installationsprogrammet.

- Problem som hämtar Azure Stack rotcertifikat från integrerade systemet.

Ett fel i Get-AzureStackRootCert.ps1 orsakade kunder misslyckas att hämta rotcertifikatet Azure Stack när du kör skriptet på en dator som inte har rotcertifikatet installerat. Skriptet har nu också publicerats igen, hur du löser det här problemet och begäran kunder [hämta uppdaterade helper skripten](https://aka.ms/appsvconmashelpers). Kontakta supporten om du fortsätter att ha problem med att hämta rotcertifikatet med det uppdaterade skriptet.

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- Det finns inga kända problem för uppdatering av Azure App Service på Azure-stacken uppdatering 1.

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

- Fack växlingen fungerar inte

Platsen fack växlingen delas upp i den här versionen. Om du vill återställa funktionerna måste du utföra följande steg:

1. Ändra ControllersNSG Nätverkssäkerhetsgruppen till **Tillåt** fjärrskrivbordsanslutningar till App controller tjänstinstanser. Ersätt AppService.local med namnet på resursgruppen som du har distribuerat Apptjänst i.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

2. Bläddra till den **CN0 VM** under virtuella datorer i Azure Stack-administratörsportalen och **Klicka på Anslut** att öppna en fjärrskrivbordssession med controller-instans. Använd de autentiseringsuppgifter som anges under distributionen av App Service.
3. Starta **PowerShell som administratör** och kör följande skript

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Stäng fjärrskrivbords-sessionen.
5. Återställa ControllersNSG Nätverkssäkerhetsgruppen till **neka** fjärrskrivbordsanslutningar till App controller tjänstinstanser. Ersätt AppService.local med namnet på resursgruppen som du har distribuerat Apptjänst i.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
- Anställda kan inte nå filserver när Apptjänst distribueras i ett befintligt virtuellt nätverk och filservern är endast tillgängligt i det privata nätverket.
 
Om du vill distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till din filserver, du måste lägga till utgående säkerhetsregel aktivera SMB-trafik mellan worker-undernät och filservern. Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till utgående säkerhetsregel med följande egenskaper:
 * Källa: alla
 * Datakällan portintervall: *
 * Mål: IP-adresser
 * Mål-IP-adressintervall: intervall av IP-adresser för din filserver
 * Målportintervall: 445
 * Protokoll: TCP
 * Åtgärd: Tillåt
 * Prioritet: 700
 * Namn: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem för molnet administratörer fungerar Azure App Service på Azure-stacken

Dokumentationen i det [Azure Stack 1802 viktig information](azure-stack-update-1802.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure App Service finns [Azure App Service på Azure-stacken översikt](azure-stack-app-service-overview.md).
- Mer information om hur du distribuerar Apptjänst Azure stacken finns [innan du börjar med App Service på Azure-stacken](azure-stack-app-service-before-you-get-started.md).
