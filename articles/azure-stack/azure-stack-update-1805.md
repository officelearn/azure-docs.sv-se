---
title: Azure-stacken 1805 Update | Microsoft Docs
description: Läs mer om nyheterna i 1805 uppdateringen för Azure-stacken integrerat system, inklusive kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cee832b144faf505fbcb77d87253b854d19ed9df
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34800972"
---
# <a name="azure-stack-1805-update"></a>Azure-stacken 1805 uppdatering

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i 1805 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i direkt relaterad till uppdateringen problem och med version (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet gäller bara för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure-stacken 1805 uppdatera versionsnumret är **1.1805.1.47**.  

> [!TIP]  
> Utifrån feedback finns en uppdatering av schemat versionen för Microsoft Azure-stacken.  Från och med den här uppdateringen 1805, representerar det nya schemat bättre den aktuella molnversionen.  
> 
> Version-schemat är nu *Version.YearYearMonthMonth.MinorVersion.BuildNumber* där andra och tredje anger ange version och utgåva. Till exempel 1805.1 representerar den *släpper till tillverkning* (RTM) version av 1805.  


### <a name="new-features"></a>Nya funktioner
Den här uppdateringen innehåller följande förbättringar av Azure-stacken.
<!-- 2297790 - IS, ASDK --> 
- **Azure-stacken innehåller nu en *Syslog* klienten** som en *förhandsgranskningsfunktion*. Den här klienten gör att vidarebefordring av loggar för granskning och säkerhet som rör Azure Stack-infrastruktur till en Syslog-server eller säkerhet information och händelse (SIEM) hanteringsprogramvara som ligger utanför Azure-stacken. Syslog-klienten stöder för närvarande endast oautentiserade UDP-anslutningar via standardporten 514. Nyttolasten för varje Syslog-meddelande är formaterad gemensamma händelsen Format (CEF). 

  När du konfigurerar Syslog-klienten använder den **Set SyslogServer** cmdlet som exponeras i Privilegierade slutpunkten. 

  Med den här förhandsversionen kan du se följande tre aviseringar. När visas av Azure-stacken aviseringarna inkluderar *beskrivningar* och *reparation* vägledning. 
  - Rubrik: Kodintegritet ut  
  - Rubrik: Kodintegritet i granskningsläge 
  - Rubrik: Användarkonton som skapas

  Den här funktionen är i förhandsvisning, den bör inte förlita sig på i produktionsmiljöer.   




### <a name="fixed-issues"></a>Fast problem

<!-- # - applicability -->


- **Olika korrigeringar** för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Innan du börjar    

### <a name="prerequisites"></a>Förutsättningar
- Installera Azure-stacken [1804 uppdatera](azure-stack-update-1804.md) innan du installerar Azure Stack 1805 uppdateringen.    
- Innan du påbörjar installationen av uppdateringen 1805 kör [Test AzureStack](azure-stack-diagnostic-test.md) att verifiera statusen för din Azure-stacken och lösa alla operativa problem som hittas. Också granska aktiva aviseringar och lös eventuella som kräver åtgärder. 

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen   
- Du kan se aviseringar med rubriken under installationen av uppdateringen 1805 *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när 1805 uppdateringen är klar.   

- <!-- 2489559 - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Steg efter uppdateringen
*Det finns inga efter uppdateringen åtgärder för uppdatering 1805.*


## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Nedan följer efter installationen kända problem med den här versionen.

### <a name="portal"></a>Portalen  
- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Lösning: Lös problemet när du har installerat uppdateringen med steg 3 från de [utlösaren automation för att konfigurera anspråksproviderförtroende i Azure-stacken](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) proceduren för att återställa standard providern Prenumerationens ägare.   

- <!-- TBD - IS ASDK --> Some administrative subscription types are not available.  When you upgrade Azure Stack to this version, the two subscription types that were [introduced with version 1804](azure-stack-update-1804.md#new-features) are not visible in the console. This is expected. The unavailable subscription types are *Metering subscription*, and *Consumption subscription*. These subscription types are visible in new Azure Stack environments beginning with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Dynamiska länkar](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


### <a name="health-and-monitoring"></a>Hälsa och övervakning
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Avisera #1:
   - NAMN: Infrastrukturrollen ohälsosamt
   - ALLVARLIGHETSGRAD: varning
   - KOMPONENT: Hälsotillstånd domänkontrollant
   - Beskrivning: Hälsotillstånd controller pulsslag skanner är inte tillgänglig. Detta kan påverka hälsorapporter och mått.  

  Avisera #2:
   - NAMN: Infrastrukturrollen ohälsosamt
   - ALLVARLIGHETSGRAD: varning
   - KOMPONENT: Hälsotillstånd domänkontrollant
   - Beskrivning: Hälsotillstånd controller fel skanner är inte tillgänglig. Detta kan påverka hälsorapporter och mått.

  Båda aviseringarna kan ignoreras och de stängs automatiskt med tiden.  

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Som en lösning att använda någon av följande metoder för att distribuera en virtuell dator. Du måste ange VM-storlek som du vill använda för varje metod.

  - **Azure Resource Manager-mall:** när du använder en mall i *vmSize* i mallen ska vara lika med VM-storlek som du vill använda. Till exempel följande post används för att distribuera en virtuell dator som använder den *F32s_v2* storlek:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** du kan använda den [az vm skapa](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) kommando och ange VM-storlek som en parameter som liknar `--size "Standard_F32s_v2"`.

  - **PowerShell:** med PowerShell som du kan använda [ny AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) med parametern som anger VM-storlek liknar `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till en ny.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased.

  Undvik problemet genom att använda en plan för tillägg för att öka en kvot på nätverket när planen är redan kopplat till en prenumeration. Mer information finns i så här [tillgängliggöra en plan för tillägget](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription.


- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group.

    Lösning för Apptjänst: Om du behöver fjärrskrivbord till domänkontrollant instanser du ändra säkerhetsregler inom nätverkssäkerhetsgrupper med PowerShell.  Följande är exempel på hur du *Tillåt*, och sedan återställa konfigurationen av *neka*:  

    - *Tillåt:*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn’t work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

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

    - *Neka:*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn’t work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

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


### <a name="sql-and-mysql"></a>SQL- och MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1805, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially. For example, if you use version 1803, first apply version 1804, and then update to 1805.      
>   
> Installationen av uppdateringen 1805 påverkar inte den nuvarande användningen av SQL- eller MySQL resursproviders av användare.
> Dina användare data i sina databaser oavsett vilken version av resursleverantörer som du använder är inte vidröras och förblir tillgängligt.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- 2489178 - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.


### <a name="usage"></a>Användning  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned uppdateringspaketet Azure Stack 1805 från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda Privilegierade slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure-stacken, se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md).
- Mer information om hur du installerar uppdateringar med Azure-stacken finns [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md).
