---
title: Azure-stacken 1802 Update | Microsoft Docs
description: Lär dig mer om vad som finns i 1802 uppdateringen för Azure-stacken integrerat system, kända problem och var du kan hämta uppdateringen.
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
ms.date: 03/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 71862463a62f11a4f2cea7dfcc60961331ded377
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-1802-update"></a>Azure-stacken 1802 uppdatering

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i 1802 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i direkt relaterad till uppdateringen problem och med version (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet gäller bara för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure-stacken 1802 uppdatera versionsnumret är **20180302.1**.  


## <a name="before-you-begin"></a>Innan du börjar    
> [!IMPORTANT]    
> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns [hantera uppdateringar i Azure-stacken översikt](/azure-stack-updates#plan-for-updates).


### <a name="prerequisites"></a>Förutsättningar
- Installera Azure-stacken [1712 uppdatera](azure-stack-update-1712.md) innan du installerar Azure Stack 1802 uppdateringen.    

- Installera **AzS Hotfix – 1.0.180312.1-Skapa 20180222.2** innan du installerar Azure Stack 1802 uppdateringen. Den här snabbkorrigeringen uppdaterar Windows Defender och är tillgänglig när du hämtar uppdateringar för Azure-stacken.

  Följ vanliga procedurer för att installera snabbkorrigeringen [installerar uppdateringar för Azure-Stack](azure-stack-apply-updates.md). Namnet på uppdateringen visas som **AzS Hotfix – 1.0.180312.1**, och innehåller följande filer: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  När du överför dessa filer till ett lagringskonto och en behållare, kör du installationen från panelen uppdatering i administrationsportal. 
  
  Till skillnad från uppdateringar till Azure-stacken ändras installera den här uppdateringen inte versionen av Azure-stacken.  För att bekräfta den här uppdateringen har installerats, visas en lista med **installerade uppdateringar**.
 


### <a name="post-update-steps"></a>Steg efter uppdateringen
*Det finns inga efter uppdateringen åtgärder för uppdatering 1802.*


### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Den här uppdateringen innehåller följande förbättringar och korrigeringar för Azure-stacken.

- **Stöd har lagts till för följande Azure Storage Service API-versioner**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Mer information finns i [Azure Stack Storage: skillnader och överväganden](/azure/azure-stack/user/azure-stack-acs-differences).

- **Stöd för större [Blockblobbar](azure-stack-acs-differences.md)**:
    - Maximalt tillåten blockstorleken ökas från 4 MB till 100 MB.
    - Maximal blob-storlek ökas från 195 GB till 4,75 TB.  

- **Infrastruktur för säkerhetskopiering** nu visas i panelen Resursproviders och aviseringar för säkerhetskopiering är aktiverade. Mer information om tjänsten infrastruktur säkerhetskopiering finns [säkerhetskopia och återställa data för Azure-stacken med Backup-tjänsten infrastruktur](azure-stack-backup-infrastructure-backup.md).

- **Uppdatera till den *Test AzureStack* cmdlet** att förbättra diagnostik för lagring. Mer information om denna cmdlet finns [verifieringen av Azure-stacken](azure-stack-diagnostic-test.md).

- **Rollbaserad åtkomstkontroll (RBAC) förbättringar** – nu kan du använda RBAC för att delegera behörigheter till Universal användargrupper när Azure Stack distribueras med AD FS. Läs mer om RBAC i [hantera RBAC](azure-stack-manage-permissions.md).

- **Stöd har lagts till för flera feldomäner**.  Mer information finns i [hög tillgänglighet för Azure-stacken](azure-stack-key-features.md#high-availability-for-azure-stack).

- **Olika korrigeringar** för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen    
*Det finns inga kända problem med installationen av uppdateringen 1802.*


### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Följande är efter installationen kända problem för version **20180302.1**

#### <a name="portal"></a>Portalen
- Möjlighet [att öppna en ny supportförfrågan i listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - För Azure-Stack integrerad system använda https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Det kanske inte går att visa beräkning eller lagring resurser i administratörsportalen. Orsaken till det här problemet uppstår ett fel under installationen av uppdateringen som gör att uppdateringen som ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.

- När du visar egenskaperna för en resurs eller en resursgrupp, den **flytta** är inaktiverat. Det är förväntat. Flytta resurser eller resursgrupper mellan resursgrupper eller prenumerationer stöds inte för närvarande.

- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.

- På instrumentpanelen i administrationsportal panelen Update kan inte visa information om uppdateringar. Klicka på ikonen Uppdatera det för att lösa problemet.

-   Du kan se en kritisk varning för komponenten Microsoft.Update.Admin i administrationsportal. Varningens namn, beskrivning och reparation alla visas som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

    Den här aviseringen kan ignoreras. 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- I både administrationsportal och användarportalen, bladet översikt misslyckas att läsa in när du väljer bladet översikt för lagringskonton som skapats med en äldre API-version (exempel: 2015-06-15). Detta inkluderar system storage-konton som **updateadminaccount** som används under korrigeringar och uppdateringar. 

  Som en tillfällig lösning kan använda PowerShell för att köra den **Start ResourceSynchronization.ps1** skript för att återställa åtkomsten till information om lagringskonto. [Skriptet är tillgänglig från GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), måste köras med administratörsbehörighet för tjänsten på Privilegierade slutpunkten. 


#### <a name="health-and-monitoring"></a>Hälsa och övervakning
Det finns inga kända problem när du har uppdaterat till 1802.

#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.

#### <a name="compute"></a>Compute
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- Azure-stacken stöder endast fasta typ av virtuella hårddiskar. Vissa bilder som erbjuds via marketplace Azure stacken använder dynamiska virtuella hårddiskar, men de har tagits bort. Ändra storlek på en virtuell dator (VM) med en dynamisk disk som är ansluten till den lämnar den virtuella datorn i ett felaktigt tillstånd.

  Om du vill undvika det här problemet måste du ta bort den virtuella datorn utan att ta bort den Virtuella datorns disk, en VHD-blobben i ett lagringskonto. Sedan konvertera den virtuella Hårddisken från en dynamisk disk till en fast disk och skapa den virtuella datorn.

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

- Internt Load Balancing (ILB) hanterar felaktigt MAC-adresser för backend-virtuella datorer, vilket gör ILB att bryta när du använder Linux-instanser på backend-nätverket.  ILB fungerar bra med Windows-instanser på backend-nätverket.

-   Funktionen för IP-vidarebefordran är visas på portalen, men att aktivera IP-vidarebefordring har ingen effekt. Den här funktionen stöds inte ännu.

- Azure-stacken stöder en enda *lokal nätverksgateway* per IP-adress. Detta gäller över alla klient-prenumerationer. Efter att skapa den första gateway nätverksanslutningen, efterföljande försök att skapa en gateway för lokala nätverksresurser med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server inställning av *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna är inte pushas till virtuella datorer i det virtuella nätverket.

- Azure-stacken stöder inte att lägga till ytterligare nätverksgränssnitt i en VM-instans när den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste ha definierats vid tidpunkten för distribution.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Lösning för Apptjänst: Om du behöver fjärrskrivbord till domänkontrollant instanser du ändra säkerhetsregler inom nätverkssäkerhetsgrupper med PowerShell.  Följande är exempel på hur du *Tillåt*, och sedan återställa konfigurationen av *neka*:  
    
    - *Tillåt:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
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
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
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


> [!NOTE]  
> När du har uppdaterat till Azure-stacken 1802 kan du fortsätta att använda SQL och MySQL resursproviders som du tidigare har distribuerats.  Vi rekommenderar att du uppdaterar SQL och MySQL när en ny version blir tillgänglig. T.ex. Azure-stacken, tillämpa uppdateringar SQL och MySQL resursprovidrar sekventiellt.  Till exempel om du använder version 1710 först installera version 1711 sedan 1712 och sedan uppdatera till 1802.      
>   
> Installationen av uppdateringen 1802 påverkar inte den nuvarande användningen av SQL- eller MySQL resursproviders av användare.
> Dina användare data i sina databaser oavsett vilken version av resursleverantörer som du använder är inte vidröras och förblir tillgängligt.    


#### <a name="app-service"></a>App Service
- Användare måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

- För att skala ut infrastruktur (anställda, hantering, frontend roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub
- När du använder den *anropa webrequest* PowerShell-cmdlet för att ladda ned Azure-stacken verktyg från Github, du får ett felmeddelande:     
    -  *anropa webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Detta fel uppstår på grund av en senaste utfasningen för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standardinställning för PowerShell). Mer information finns i [svaga kryptografiska standarder borttagning meddelande](https://githubengineering.com/crypto-removal-notice/).

  Lös problemet genom att lägga till `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` till början av skriptet för att tvinga PowerShell-konsolen att använda TLSv1.2 när laddas ned från GitHub-lagringsplatser.


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned uppdateringspaketet Azure Stack 1802 från [här](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Mer information
Microsoft tillhandahåller ett sätt att övervaka och återuppta uppdateringar med hjälp av detta Privilegierade slutpunkt (program) installeras med uppdatering 1710.

- Finns det [övervaka uppdateringar i Azure-stacken använder Privilegierade endpoint-dokumentationen](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Se också

- En översikt över uppdateringshantering i Azure-stacken, se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md).
- Mer information om hur du installerar uppdateringar med Azure-stacken finns [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md).
