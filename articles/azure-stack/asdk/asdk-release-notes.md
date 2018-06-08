---
title: Viktig information för Microsoft Azure-stacken Development Kit | Microsoft Docs
description: Förbättringar av korrigeringar och kända problem med Azure-stacken Development Kit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 185cd6f20cdb19531777ccb58e72be14ea656549
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850128"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure-stacken Development Kit viktig information  
Dessa versionsanmärkningar innehåller information om förbättringar, korrigeringar och kända problem i Azure-stacken Development Kit. Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](.\.\azure-stack-updates.md#determine-the-current-version).

> Hålla dig uppdaterad med vad är nytt i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Skapa 1.1805.1.47

> [!TIP]  
> Utifrån feedback finns en uppdatering av schemat versionen för Microsoft Azure-stacken. Från och med den här uppdateringen 1805, representerar det nya schemat bättre den aktuella molnversionen.  
> 
> Version-schemat är nu *Version.YearYearMonthMonth.MinorVersion.BuildNumber* där andra och tredje anger ange version och utgåva. Till exempel 1805.1 representerar den *släpper till tillverkning* (RTM) version av 1805.  


### <a name="new-features"></a>Nya funktioner 
Den här versionen innehåller följande förbättringar och korrigeringar för Azure-stacken.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack now includes a *Syslog* client** as a *preview feature*. This client allows the forwarding of audit and security logs related to the Azure Stack infrastructure to a Syslog server or security information and event management (SIEM) software that is external to Azure Stack. Currently, the Syslog client only supports unauthenticated UDP connections over default port 514. The payload of each Syslog message is formatted in Common Event Format (CEF). 

  När du konfigurerar Syslog-klienten använder den **Set SyslogServer** cmdlet som exponeras i Privilegierade slutpunkten. 

  Med den här förhandsversionen kan du se följande tre aviseringar. När visas av Azure-stacken aviseringarna inkluderar *beskrivningar* och *reparation* vägledning. 
  - Rubrik: Kodintegritet ut  
  - Rubrik: Kodintegritet i granskningsläge 
  - Rubrik: Användarkonton som skapas

  Den här funktionen är i förhandsvisning, den bör inte förlita sig på i produktionsmiljöer.   


### <a name="fixed-issues"></a>Fast problem

- **Olika korrigeringar** för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- <!-- 2551834 - IS, ASDK --> When you select **Overview** for a storage account in either the admin or user portals, the information from the *Essentials* pane does not display.  The Essentials pane displays information about the account like its *Resource group*, *Location*, and *Subscription ID*.  Other options for Overview  are accessible, like *Services* and *Monitoring*, as well as options to *Open in Explorer* or to *Delete storage account*.  

  Du kan visa informationen tillgänglig i [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-cmdlet. 

- <!-- TBD - IS ASDK --> Do not use the new administrative subscription types of *Metering subscription*, and *Consumption subscription*. These new subscription types were introduced with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Dynamiska länkar](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


#### <a name="health-and-monitoring"></a>Hälsa och övervakning
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

  Båda aviseringarna kan ignoreras och stängs automatiskt med tiden.  

- <!-- 2392907 – ASDK -->   You might see a *critical* alert for **Low memory capacity**. This alert has the following description: *The region has consumed more than 95.00 % of available memory. Creating virtual machines with large amounts of memory may fail.*

  Den här aviseringen kan genereras när Azure Stack felaktigt konton för minnesanvändning på Azure-stacken development kit.  

  Den här aviseringen kan ignoreras och problemet har ingen effekt på placeringen av virtuella datorer. 

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


#### <a name="compute"></a>Compute
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


- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till en ny.


- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Undvik problemet genom att använda en plan för tillägg för att öka en kvot på nätverket när planen är redan kopplat till en prenumeration. Mer information finns i så här [tillgängliggöra en plan för tillägget](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.  

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.

#### <a name="usage"></a>Användning  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->



## <a name="build-201805131"></a>Skapa 20180513.1

### <a name="new-features"></a>Nya funktioner 
Den här versionen innehåller följande förbättringar och korrigeringar för Azure-stacken.  

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  Dessa nya prenumerationstyper är synliga, men en del av en större ändring att säkra Standard Provider-prenumeration och för att göra det enklare att distribuera delade resurser, som värd för SQL-servrar. 

  De tre prenumeration finns nu är:  
  - Standard prenumeration på leverantör: fortsätta att använda den här prenumerationstypen. 
  - Avläsning av prenumerationen: *inte använder den här prenumerationstypen.*
  - Förbrukning prenumerationen: *inte använder den här prenumerationstypen*

### <a name="fixed-issues"></a>Fast problem
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Olika korrigeringar** för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Ytterligare versioner tidsgränsen med den här uppdateringen  
Följande är nu tillgängliga, men kräver inte Azure Stack-uppdatering 1804.
- **Uppdatera till Microsoft Azure-stacken System Center Operations Manager Övervakningspaket**. Det finns en ny version (1.0.3.0) av Microsoft System Center Operations Manager Monitoring Pack för Azure-Stack för [hämta](https://www.microsoft.com/download/details.aspx?id=55184). Med den här versionen kan du använda tjänstens huvudnamn när du lägger till en ansluten Azure Stack-distribution. Den här versionen har också en uppdateringshantering upplevelse som gör att du kan ta Reparationsåtgärd direkt från Operations Manager. Det finns också nya instrumentpaneler som visar resursproviders, skalningsenheter och skala enhet noder.

- **Nya Azure stacken Admin PowerShell Version 1.3.0**.  Azure-stacken PowerShell 1.3.0 är nu tillgängliga för installation. Den här versionen innehåller kommandon för alla providrar för administratören att hantera Azure-stacken.  Med den här versionen innehåll att bli inaktuell från Azure-stacken verktyg GitHub [databasen](https://github.com/Azure/AzureStack-Tools). 

   För information om installationen, följer du de [instruktioner](.\.\azure-stack-powershell-install.md) eller [hjälp](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) innehåll för Azure-stacken modulen 1.3.0. 

- **Inledande versionen av Azure Rest API-referens Stack**. Den [API-referens för alla Azure-stacken Admin-providrar](https://docs.microsoft.com/rest/api/azure-stack/) publiceras nu. 

### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Azure-stacken Development Kit använda https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Dynamiska länkar](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

    Den här aviseringen kan ignoreras. 

#### <a name="health-and-monitoring"></a>Hälsa och övervakning
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

  Båda aviseringarna kan ignoreras. De stängs automatiskt med tiden.  

#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.
 
#### <a name="compute"></a>Compute
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till en ny.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Undvik problemet genom att använda en plan för tillägg för att öka en kvot på nätverket när planen är redan kopplat till en prenumeration. Mer information finns i så här [tillgängliggöra en plan för tillägget](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Användning  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub
- När du använder den *anropa webrequest* PowerShell-cmdlet för att ladda ned Azure-stacken verktyg från Github, du får ett felmeddelande:     
    -  *anropa webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Detta fel uppstår på grund av en senaste utfasningen för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standardinställning för PowerShell). Mer information finns i [svaga kryptografiska standarder borttagning meddelande](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Skapa 20180302.1

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Nya funktioner och korrigeringar som släppts för Azure-stacken integrerade system version 1803 gäller för Azure-stacken Development Kit. Finns det [nya funktioner](.\.\azure-stack-update-1803.md#new-features) och [fast problem](.\.\azure-stack-update-1803.md#fixed-issues) avsnitt i Azure-stacken 1803 uppdatera viktig information för information.  
> [!IMPORTANT]    
> Några av de objekt som visas i den **nya funktioner** och **fast problem** avsnitt är endast relevant för Azure-stacken integrerat system.

### <a name="changes"></a>Ändringar
- Ett sätt att ändra tillståndet för ett nyskapat erbjudande från *privata* till *offentliga* eller *inaktiverade* har ändrats. Mer information finns i [skapa ett erbjudande](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- Möjlighet [att öppna en ny supportförfrågan i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - Azure-stacken Development Kit använda https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Du ser en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.

- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.

- På instrumentpanelen i administrationsportal panelen Update kan inte visa information om uppdateringar. Klicka på ikonen Uppdatera det för att lösa problemet.

-   Du kan se en kritisk varning för komponenten Microsoft.Update.Admin i administrationsportal. Varningens namn, beskrivning och reparation alla visas som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

    Den här aviseringen kan ignoreras. 

- I både administrationsportal och användarportalen, bladet översikt misslyckas att läsa in när du väljer bladet översikt för lagringskonton som skapats med en äldre API-version (exempel: 2015-06-15). 

  Som en tillfällig lösning kan använda PowerShell för att köra den **Start ResourceSynchronization.ps1** skript för att återställa åtkomsten till information om lagringskonto. [Skriptet är tillgänglig från GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), måste köras med administratörsbehörighet för tjänsten på development kit värden om du använder ASDK.  

- Den **tjänstens hälsa** bladet inte laddas. När du öppnar bladet tjänstens hälsa i administratören eller användaren portal, Azure stacken visas ett fel och läses inte in information. Detta är förväntat. Även om du kan markera och öppna tjänstens hälsa, funktionen är inte tillgänglig ännu, men de kommer att genomföras i en framtida version av Azure-stacken.


#### <a name="health-and-monitoring"></a>Hälsa och övervakning
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

  Båda aviseringarna kan ignoreras. De stängs automatiskt med tiden.  

- I administrationsportalen för Azure-stacken kan du se en kritisk varning med namnet **väntande externa certifikatet upphör att gälla**.  Den här aviseringen kan ignoreras och påverkar driften av Azure-stacken Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.
 
#### <a name="compute"></a>Compute
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- När du skapar virtuella datorer på Azure-stacken användarportalen visar portalen ett felaktigt antal datadiskar som kan kopplas till DS-serien VM. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- När en VM-avbildning misslyckas skapas kan en misslyckade objekt som du inte kan ta bort läggas till bladet VM-avbildningar beräkning.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

-  Om det tar för lång tid att etablera ett tillägg på en distribution av Virtuella datorer, bör användarna kan etablering timeout-värde i stället för att stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Nätverk
- Under **nätverk**, om du klickar på **anslutning** att ställa in en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.

- När en virtuell dator som associeras med en offentlig IP-adress, kan du koppla den virtuella datorn från att IP-adress. Disassociation verkar fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till en ny.



- Azure-stacken stöder en enda *lokal nätverksgateway* per IP-adress. Detta gäller över alla klient-prenumerationer. Efter att skapa den första gateway nätverksanslutningen, efterföljande försök att skapa en gateway för lokala nätverksresurser med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server inställning av *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna är inte pushas till virtuella datorer i det virtuella nätverket.
 
- Azure-stacken stöder inte att lägga till ytterligare nätverksgränssnitt i en VM-instans när den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste ha definierats vid tidpunkten för distribution.



#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- Det kan ta upp till en timme innan användarna kan skapa databaser i en ny SQL eller MySQL SKU.

- Den databas som värd för servrar måste vara dedikerade för användning av resource provider och arbetsbelastningar. Du kan inte använda en instans som används av några andra konsumenten, inklusive Apptjänster.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>App Service
- Användare måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

- För att skala ut infrastruktur (anställda, hantering, frontend roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.
 
#### <a name="usage"></a>Användning  
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub
- När du använder den *anropa webrequest* PowerShell-cmdlet för att ladda ned Azure-stacken verktyg från Github, du får ett felmeddelande:     
    -  *anropa webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Detta fel uppstår på grund av en senaste utfasningen för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standardinställning för PowerShell). Mer information finns i [svaga kryptografiska standarder borttagning meddelande](https://githubengineering.com/crypto-removal-notice/).

  Lös problemet genom att lägga till `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` till början av skriptet för att tvinga PowerShell-konsolen att använda TLSv1.2 när laddas ned från GitHub-lagringsplatser.





