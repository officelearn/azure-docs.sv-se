---
title: App Service i Azure Stack update 1 viktig information | Microsoft Docs
description: Lär dig mer om vad som finns i en uppdatering för App Service i Azure Stack, kända problem och var du kan hämta uppdateringen.
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
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 99b4134f8f2d6ed2f521a7f488c33e06c0f436bb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58165836"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service i Azure Stack update 1 viktig information

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure Stack Update 1 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1802 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service.
>
>

## <a name="build-reference"></a>Skapa referens

App Service på Azure Stack Update 1 build-nummer är **69.0.13698.9**

### <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Nya distributioner av Azure App Service i Azure Stack nu kräver en [tre ämne jokerteckencertifikat](azure-stack-app-service-before-you-get-started.md#get-certificates) tack vare förbättringar i hanteringen av där enkel inloggning för Kudu är nu i Azure App Service. Det nya ämnet är  **\*. sso.appservice.\< region\>.\< DomainName\>.\< tillägget\>**
>
>

Referera till den [innan du börjar dokumentation](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service i Azure Stack uppdatering 1 innehåller följande förbättringar och korrigeringar:

- **Hög tillgänglighet för Azure App Service** – The Azure Stack 1802 uppdatering aktiverat arbetsbelastningar som ska distribueras över feldomäner. App Service-infrastrukturen är därför att vara feltolerant som kommer att distribueras över feldomäner. Som standard har alla nya distributioner i Azure App Service med den här funktionen men för distributioner som har slutförts innan Azure Stack 1802 uppdateringen tillämpas avser den [Feldomän för App Service-dokumentation](azure-stack-app-service-fault-domain-update.md)

- **Distribuera i befintliga virtuella nätverket** -kunder kan nu distribuera App Service i Azure Stack i ett befintligt virtuellt nätverk. Distribuera i ett befintligt virtuellt nätverk ger kunder möjlighet att ansluta till SQL Server och filserver, krävs för Azure App Service över privata portar. Under distributionen meddelar kunderna kan välja för att distribuera i ett befintligt virtuellt nätverk, men [måste skapa undernät för användning av App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) före distributionen.

- Uppdaterar till **App Service-klient, Admin, portaler för funktioner och verktyg för Kudu**. Konsekvent med Azure Stack Portal SDK-version.

- **Uppdateringar till följande ramverk för programmet och verktyg**:
    - Lagt till **.NET Core 2.0** stöd
    - Lagt till **Node.JS** versioner:
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
    - Lagt till **NPM** versioner:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Lagt till **PHP** uppdateringar:
        - 5.6.32
        - 7.0.26 (x86 och x64)
        - 7.1.12 (x86 och x64)
    - Uppdatera **Git för Windows** till v 2.14.1
    - Uppdatera **Mercurial** till v4.5.0

  - Lagt till stöd för **endast HTTPS** funktion inom Custom Domain-funktionen i App Service-klient-Portal. 

  - Har lagts till verifiering av lagringsanslutning i väljaren för anpassad lagring för Azure Functions 

#### <a name="fixes"></a>Korrigeringar

- När du skapar ett offline distributionspaket kan får kunder inte längre ett felmeddelande om nekad när du öppnar mappen från App Service-installationsprogrammet

- Löst problem när du arbetar i funktionen anpassade domäner i App Service-klient-Portal.

- Förhindra att kunder som använder reserverade administratör namn under installationen

- Aktiverad App Service-distributionen med **domänanslutna** filserver

- Förbättrad hämtning av Azure Stack-rot-certifikatet i skriptet och verifiera nu rotcertifikatet i installationsprogrammet för App Service.

- Fast felaktig status som returneras till Azure Resource Manager när en prenumeration har tagits bort de inneslutna resurserna i namnområdet Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Kända problem med distributionsprocessen

- Verifieringsfel för certifikat

Vissa kunder ha uppstått problem när de tillhandahåller certifikat till App Service-installationsprogram när du distribuerar på ett integrerat system på grund av mycket begränsad verifiering i installationsprogrammet. App Service-installationsprogrammet har publicerats igen, bör kunderna [ladda ned installationsprogrammet till uppdaterade](https://aka.ms/appsvconmasinstaller). Kontakta supporten om du fortfarande har problem som verifierar certifikat med uppdaterade installationsprogrammet.

- Problem med att hämta Azure Stack-rotcertifikat från integrerade system.

Ett fel i Get-AzureStackRootCert.ps1 gjorde kunder inte kan hämta Azure Stack-rotcertifikat när du kör skriptet på en dator som inte har rotcertifikatet installerat. Skriptet har nu även publicerats igen, hur du löser det här problemet och begäran kunder [ladda ned de uppdaterade hjälpskript](https://aka.ms/appsvconmashelpers). Kontakta supporten om du fortfarande har problem med att hämta rotcertifikat med det uppdaterade skriptet.

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- Det finns inga kända problem för uppdatering av Azure App Service i Azure Stack uppdatering 1.

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

- Växling fungerar inte

Plats-växling är uppdelad i den här versionen. För att återställa funktioner, gör du följande:

1. Ändra ControllersNSG Nätverkssäkerhetsgrupp till **Tillåt** anslutningar till fjärrskrivbord till instanser för App Service-domänkontrollant. Ersätt AppService.local med namnet på resursgruppen som du har distribuerat App Service i.

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

2. Bläddra till den **CN0 VM** under virtuella datorer i Azure Stack-administratörsportalen och **Klicka på Anslut** att öppna en fjärrskrivbordssession med nätverksstyrenhetens instans. Använd autentiseringsuppgifterna som angetts under distributionen av App Service.
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

4. Stäng fjärrskrivbordssessionen.
5. Återställ ControllersNSG Nätverkssäkerhetsgrupp till **neka** anslutningar till fjärrskrivbord till instanser för App Service-domänkontrollant. Ersätt AppService.local med namnet på resursgruppen som du har distribuerat App Service i.

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

6. Arbetare är inte nå filserver när App Service har distribuerats i ett befintligt virtuellt nätverk och servern är endast tillgänglig i det privata nätverket.

Om du väljer att distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till filservern, du måste lägga till en utgående säkerhetsregel att aktivera SMB-trafik mellan worker-undernät och filservern. Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till en utgående säkerhetsregel med följande egenskaper:

- Källa: Alla
- Käll-portintervall: *
- Mål: IP-adresser
- Mål-IP-adressintervall: Intervall av IP-adresser för din filserver
- Målportintervall: 445
- Protokoll: TCP
- Åtgärd: Tillåt
- Prioritet: 700
- Namn: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure App Service i Azure Stack

Finns i dokumentationen i den [viktig information om Azure Stack 1802](azure-stack-update-1802.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure App Service finns i [Azure App Service i Azure Stack-översikt](azure-stack-app-service-overview.md).
- Mer information om hur du förbereder att distribuera App Service i Azure Stack finns i [innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md).
