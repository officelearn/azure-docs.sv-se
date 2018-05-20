---
title: Azure-stacken 1803 Update | Microsoft Docs
description: Lär dig mer om vad som finns i 1803 uppdateringen för Azure-stacken integrerat system, kända problem och var du kan hämta uppdateringen.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 095356e76dc72c3e549c23ab3855962e9c2f1d26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="azure-stack-1803-update"></a>Azure-stacken 1803 uppdatering

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i 1803 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i direkt relaterad till uppdateringen problem och med version (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet gäller bara för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure-stacken 1803 uppdatera versionsnumret är **20180329.1**.


## <a name="before-you-begin"></a>Innan du börjar    
> [!IMPORTANT]    
> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Förutsättningar
- Installera Azure-stacken [1802 uppdatera](azure-stack-update-1802.md) innan du installerar Azure Stack 1803 uppdateringen.   

- Installera **AzS Hotfix – 1.0.180312.1-Skapa 20180222.2** innan du installerar Azure Stack 1803 uppdateringen. Den här snabbkorrigeringen uppdaterar Windows Defender och är tillgänglig när du hämtar uppdateringar för Azure-stacken.

  Följ vanliga procedurer för att installera snabbkorrigeringen [installerar uppdateringar för Azure-Stack](azure-stack-apply-updates.md). Namnet på uppdateringen visas som **AzS Hotfix – 1.0.180312.1**, och innehåller följande filer: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.XML

  När du överför dessa filer till ett lagringskonto och en behållare, kör du installationen från panelen uppdatering i administrationsportal. 
  
  Till skillnad från uppdateringar till Azure-stacken ändras installera den här uppdateringen inte versionen av Azure-stacken. För att bekräfta den här uppdateringen har installerats, visas en lista med **installerade uppdateringar**.

### <a name="post-update-steps"></a>Steg efter uppdateringen
- Installera alla tillämpliga snabbkorrigeringar efter installationen av 1803. Mer information läser du följande knowledge base-artiklar, samt våra [Servicing princip](azure-stack-servicing-policy.md).

  - [KB 4103348 - Nätverksstyrenhetens API-tjänsten kraschar när du försöker installera en uppdatering för Azure-stacken](https://support.microsoft.com/en-us/help/4103348)

- Granska brandväggskonfigurationen så när du har installerat uppdateringen [nödvändiga portarna](azure-stack-integrate-endpoints.md) är öppna. Den här uppdateringen införs till exempel Azure-Monitor som innehåller en ändring av granskningsloggar för att skicka aktivitetsloggar. Med den här ändringen port 13012 används nu och måste också vara öppen.  

### <a name="new-features"></a>Nya funktioner 
Den här uppdateringen innehåller följande förbättringar och korrigeringar för Azure-stacken.

- **Uppdatera Azure Stack hemligheter** - (konton och certifikat). Mer information om hur du hanterar hemligheter finns [rotera hemligheter i Azure-stacken](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   Också som en del av detta ändras under **fler tjänster**, *granskningsloggar* nu visas som *aktivitetsloggar*. Funktionen är nu konsekvent med den Azure-portalen. 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   Sparse-filer är ett effektivt filformat som används för att minska användningen av diskutrymme för lagring och förbättra i/o.  Mer information finns i [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) för Windows Server. 

### <a name="fixed-issues"></a>Fast problem

- <!-- 1739988 --> Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 1868283 --> Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324 --> Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- När du skapar en virtuell dator meddelandet *det gick inte att visa priserna* visas inte längre när du väljer en storlek för VM-storlek.

- Olika korrigeringar för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda.


### <a name="changes"></a>Ändringar
- Ett sätt att ändra tillståndet för ett nyskapat erbjudande från *privata* till *offentliga* eller *inaktiverade* har ändrats. Mer information finns i [skapa ett erbjudande](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen    
<!-- 2328416 --> During installation of the 1803 update, there can be downtime of the blob service and internal services that use blob service. This includes some virtual machine operations. This down time can cause failures of tenant operations or alerts from services that can’t access data. This issue resolves itself when the update completes installation. 


### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Följande är efter installationen kända problem för version **20180323.2**.

#### <a name="portal"></a>Portalen
- Möjlighet [att öppna en ny supportförfrågan i listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - För Azure-Stack integrerad system använda https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Det kanske inte går att visa beräkning eller lagring resurser i administratörsportalen. Orsaken till det här problemet uppstår ett fel under installationen av uppdateringen som gör att uppdateringen som ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.

- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.

- På instrumentpanelen i administrationsportal panelen Update kan inte visa information om uppdateringar. Klicka på ikonen Uppdatera det för att lösa problemet.

- I administrationsportal kan du se en kritisk varning för den *Microsoft.Update.Admin* komponent. Varningens namn, beskrivning och reparation alla visas som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

  Den här aviseringen kan ignoreras. 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.



#### <a name="compute"></a>Compute
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- När du skapar en tillgänglighetsuppsättning i portalen genom att gå till **ny** > **Compute** > **tillgänglighetsuppsättning**, du kan bara skapa en tillgänglighetsuppsättning med en feldomän och uppdateringsdomän 1. Som en lösning när du skapar en ny virtuell dator, skapa tillgänglighetsuppsättning med hjälp av PowerShell, CLI eller från portalen.

- När du skapar virtuella datorer på Azure-stacken användarportalen visar portalen ett felaktigt antal datadiskar som kan kopplas till DS-serien VM. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- När en VM-avbildning misslyckas skapas kan en misslyckade objekt som du inte kan ta bort läggas till bladet VM-avbildningar beräkning.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

-  Om det tar för lång tid att etablera ett tillägg på en distribution av Virtuella datorer, bör användarna kan etablering timeout-värde i stället för att stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Nätverk
- När en virtuell dator som associeras med en offentlig IP-adress, kan du koppla den virtuella datorn från att IP-adress. Disassociation verkar fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny.



- Azure-stacken stöder en enda *lokal nätverksgateway* per IP-adress. Detta gäller över alla klient-prenumerationer. Efter att skapa den första gateway nätverksanslutningen, efterföljande försök att skapa en gateway för lokala nätverksresurser med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server inställning av *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna är inte pushas till virtuella datorer i det virtuella nätverket.

- Azure-stacken stöder inte att lägga till ytterligare nätverksgränssnitt i en VM-instans när den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste ha definierats vid tidpunkten för distribution.

- <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Lösning för Apptjänst: Om du behöver fjärrskrivbord till domänkontrollant instanser du ändra säkerhetsregler inom nätverkssäkerhetsgrupper med PowerShell.  Följande är exempel på hur du *Tillåt*, och sedan återställa konfigurationen av *neka*:  
    
    - *Tillåt:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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


#### <a name="sql-and-mysql"></a>SQL- och MySQL
- Innan du fortsätter bör du granska viktigt i [innan du börjar](#before-you-begin) vid start av dessa viktig information.

- Det kan ta upp till en timme innan användarna kan skapa databaser i en ny SQL- eller MySQL-distribution.

- Endast resursprovidern stöds för att skapa objekt på servrar som SQL-värd eller MySQL. Objekt som har skapats på en värdserver som inte har skapats av resursprovidern kan resultera i ett felaktigt tillstånd.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

> [!NOTE]  
> När du har uppdaterat till Azure-stacken 1803 kan du fortsätta att använda SQL och MySQL resursproviders som du tidigare har distribuerats.  Vi rekommenderar att du uppdaterar SQL och MySQL när en ny version blir tillgänglig. T.ex. Azure-stacken, tillämpa uppdateringar SQL och MySQL resursprovidrar sekventiellt.  Till exempel om du använder version 1711 först installera version 1712 sedan 1802 och sedan uppdatera till 1803.      
>   
> Installationen av uppdateringen 1803 påverkar inte den nuvarande användningen av SQL- eller MySQL resursproviders av användare.
> Dina användare data i sina databaser oavsett vilken version av resursleverantörer som du använder är inte vidröras och förblir tillgängligt.    



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


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned uppdateringspaketet Azure Stack 1803 från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda Privilegierade slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure-stacken, se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md).
- Mer information om hur du installerar uppdateringar med Azure-stacken finns [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md).
