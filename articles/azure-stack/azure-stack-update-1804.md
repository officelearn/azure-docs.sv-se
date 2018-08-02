---
title: Azure Stack 1804 Update | Microsoft Docs
description: Lär dig mer om vad som finns i 1804 uppdateringen för Azure Stack integrerade system, kända problem och var du kan hämta uppdateringen.
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
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 0190298cbf6352feeb71e365f5815e174c9e30cc
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413524"
---
# <a name="azure-stack-1804-update"></a>Uppdatering av Azure Stack 1804

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskrivs förbättringarna och korrigeringar i 1804 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure Stack 1804 update build-nummer är **20180513.1**.   

### <a name="new-features"></a>Nya funktioner
Den här uppdateringen innehåller följande förbättringar för Azure Stack.

- <!-- 15028744 - IS -->  **Visual Studio-stöd för frånkopplade Azure Stack-distributioner som använder AD FS**. Visual Studio du nu kan lägga till prenumerationer och autentisera externa med hjälp av AD FS användarens autentiseringsuppgifter. 
 
- <!-- 1779474, 1779458 - IS --> **Använda Av2 och F-serien virtuella datorer**. Azure Stack kan nu använda virtuella datorer baserat på storlekar för virtuella datorer i Av2-serien och F-serien. Mer information finns i [VM-storlekar som stöds i Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Nya administrativa prenumerationer**. Med 1804 finns det två typer av nya prenumerationer i portalen. Dessa nya typer av prenumerationer är utöver standard providerprenumeration och synliga i nya Azure Stack-installationer från och med version 1804. *Använd inte de här nya typer av prenumerationer med den här versionen av Azure Stack*. Vi meddelar tillgängligheten för att använda dessa typer av prenumerationer in med en kommande uppdatering. 

  Om du uppdaterar Azure Stack till version 1804, visas inte de två nya typerna av prenumerationer. Dock nya distributioner av Azure Stack-integrerade system och installationer av Azure Stack Development Kit version 1804 eller senare har åtkomst till alla tre prenumerationstyper.  

  Dessa nya typer av prenumerationer är en del av en större ändring att skydda standard providerprenumeration och för att göra det enklare att distribuera delade resurser som som är värd för SQL-servrar. När vi lägger till fler delar av den här större ändring med framtida uppdateringar till Azure Stack kan gå förlorade resurser som har distribuerats under dessa nya typer av prenumerationer. 

  De tre prenumerationen nu visas är:  
  - Standard providerprenumeration: fortsätta att använda den här prenumerationstypen. 
  - Avläsning av prenumerationen: *inte använder den här prenumerationstypen.*
  - Användning-prenumeration: *inte använder den här prenumerationstypen*

  



## <a name="fixed-issues"></a>Åtgärdade problem

- <!-- IS, ASDK -->  I administrationsportalen behöver du inte längre uppdatera uppdatera panel innan den visar information.
 
- <!-- 2050709  -->  Du kan nu använda administrationsportalen för att redigera lagringsmått för Blob service, Table service och kötjänsten.
 
- <!-- IS, ASDK --> Under **nätverk**när du klickar på **anslutning** att konfigurera en VPN-anslutning **plats-till-plats (IPsec)** nu är det enda tillgängliga alternativet.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Ytterligare versioner som taggats med den här uppdateringen  
Följande är nu tillgängliga, men kräver inte Azure Stack-uppdatering 1804.
- **Uppdatera till Microsoft Azure Stack-System Center Operations Manager Övervakningspaket**. En ny version (1.0.3.0) av Microsoft System Center Operations Manager Monitoring Pack för Azure Stack är tillgänglig för [hämta](https://www.microsoft.com/download/details.aspx?id=55184). Med den här versionen kan du använda tjänstens huvudnamn när du lägger till en ansluten Azure Stack-distribution. Den här versionen har också en uppdateringshantering upplevelse som låter dig dra Reparationsåtgärd direkt från Operations Manager. Det finns även nya instrumentpaneler som Visa resursprovidrar, skalningsenheter och skala enhet noder.

- **Nya Azure Stack Admin PowerShell-Version 1.3.0**.  Azure Stack PowerShell 1.3.0 är nu tillgängliga för installation. Den här versionen innehåller kommandon för alla providrar för administratören att hantera Azure Stack.  Den här versionen kan delar av innehållet upphör att gälla från Azure Stack verktyg GitHub [databasen](https://github.com/Azure/AzureStack-Tools). 

   Installationsinformation, följ den [instruktioner](azure-stack-powershell-install.md) eller [hjälpa](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) innehåll för Azure Stack-modulen 1.3.0. 

- **Första versionen av Azure Rest API-referens Stack**. Den [API-referens för alla Azure Stack-Admin-providrar](https://docs.microsoft.com/rest/api/azure-stack/) har nu publicerats. 


## <a name="before-you-begin"></a>Innan du börjar    

### <a name="prerequisites"></a>Förutsättningar
- Installera Azure Stack [1803 uppdatera](azure-stack-update-1803.md) innan du installerar Azure Stack 1804 uppdateringen.  
  
- Installera den senaste tillgängliga [uppdatering eller snabbkorrigering för version 1803](azure-stack-update-1803.md#post-update-steps). 


### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen   
- Du kan se aviseringar med rubriken under installationen av uppdateringen 1804 *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när uppdateringen till 1804 är klar.   
 
- <!-- TBD - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Steg efter uppdateringen
När installationen av 1804, installerar du eventuella tillämpliga snabbkorrigeringar. Visa mer information i följande artiklar i kunskapsbasen, samt våra [Servicing princip](azure-stack-servicing-policy.md).  
 - [KB 4344114 – Azure Stack-snabbkorrigeringen 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Följande är efter installation kända problem för version **20180513.1**.

#### <a name="portal"></a>Portalen
- <!-- TBD - IS ASDK --> Du kan inte använda drivrutinsuppdateringar med hjälp av en OEM-tillägg-paketet med den här versionen av Azure Stack.  Det finns ingen lösning på problemet.

- <!-- 1272111 - IS --> Du kan inte visa Azure Stack-skalningsenheter i Admin portal när du installerar eller uppdaterar till den här versionen av Azure Stack.  
  Lösning: Använd PowerShell för att visa information om Skalningsenheter. Mer information finns i den [hjälpa](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) innehåll för Azure Stack-modulen 1.3.0. 

- <!-- 2332636 - IS -->  När du använder AD FS för Azure Stack-identitetssystemet och uppdatera till den här versionen av Azure Stack standardägaren av providern Standardprenumeration återställs till inbyggt **CloudAdmin** användare.  
  Lösning: Lös problemet när du har installerat uppdateringen med steg 3 i den [utlösa automatisering för att konfigurera anspråk providern förtroende i Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) proceduren för att återställa standard-providern Prenumerationens ägare.   

- <!-- TBD - IS ASDK --> Vissa typer av administrativa prenumerationer är inte tillgängliga.  När du uppgraderar Azure Stack i den här versionen, två prenumerationstyper som var [introducerades i version 1804](#new-features) visas inte i konsolen. Detta är normalt. Typerna som inte tillgänglig prenumeration är *Avläsning av prenumeration*, och *förbrukning prenumeration*. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den *Standard Provider* prenumerationstyp.  


- <!-- TBD -  IS ASDK -->Möjligheten [att öppna en ny supportbegäran i listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - För Azure Stack integrerade system använder https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Det kanske inte går att visa resurser för beräkning eller lagring i administratörsportalen. Orsaken till problemet är ett fel under installationen av uppdateringen som gör att uppdateringen ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- <!-- TBD - IS --> Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du kugghjulsikonen i det övre högra hörnet i portalen och välj sedan **Återställ standardinställningarna**.

- <!-- TBD - IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD - IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

- <!-- TBD - IS ASDK --> I admin portal kan du se en kritisk varning i *Microsoft.Update.Admin* komponent. Aviseringens namn, beskrivning och åtgärder som alla Visa som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

  Den här varningen kan ignoreras. 


#### <a name="health-and-monitoring"></a>Hälsa och övervakning
- <!-- 1264761 - IS ASDK -->  Du kan se aviseringar för den *hälsotillstånd controller* komponent som har följande information:  

   Avisera #1:
   - NAMN: Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: varning
   - KOMPONENT: Health controller
   - Beskrivning: Kontrollanten hälsotillstånd pulsslag skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.  

  Avisera #2:
   - NAMN: Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: varning
   - KOMPONENT: Health controller
   - Beskrivning: Kontrollanten hälsotillstånd fel skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.

  Båda aviseringarna kan ignoreras. De stängs automatiskt över tid.  
 

#### <a name="compute"></a>Compute
- <!-- TBD - IS --> När du väljer en VM-storlek för en distribution av virtuella datorer, vissa F-seriens storlekar visas inte som en del av storlek Väljaren när du skapar en virtuell dator. Storlek på följande Virtuella datorer visas inte i Väljaren: *F8s_v2*, *F16s_v2*, *F32s_v2*, och *F64s_v2*.  
  Som en lösning kan du använda en av följande metoder för att distribuera en virtuell dator. Du måste ange VM-storlek som du vill använda i varje metod.

  - **Azure Resource Manager-mall:** när du använder en mall kan du ange den *vmSize* i mallen ska vara lika med önskad storlek. Till exempel följande används för att distribuera en virtuell dator som använder den *F32s_v2* storlek:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** du kan använda den [az vm skapa](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) kommandot och ange virtuella datorstorlek som en parameter, liknar `--size "Standard_F32s_v2"`.

  - **PowerShell:** med PowerShell som du kan använda [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) med parametern som anger virtuella datorns storlek, liknar `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- <!-- TBD - IS --> När du skapar en tillgänglighetsuppsättning i portalen genom att gå till **New** > **Compute** > **tillgänglighetsuppsättning**, du kan bara skapa en tillgänglighetsuppsättning med en feldomän och uppdateringsdomän 1. Som en lösning, när du skapar en ny virtuell dator, skapa tillgänglighetsuppsättning med hjälp av PowerShell, CLI, eller från portalen.

- <!-- TBD - IS ASDK --> När du skapar virtuella datorer på Azure Stack-användarportalen visar ett felaktigt antal datadiskar som kan kopplas till D-serien VM i portalen. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD - IS ASDK --> När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försöka redownload VM-avbildning som tidigare misslyckats.

- <!-- TBD - IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  


#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 2388980 - IS ASDK --> När en virtuell dator skapas och som är associerade med en offentlig IP-adress, kan du kopplingen mellan den virtuella datorn från IP-adress. Avassociationsåtgärden ser ut att fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn.

  För närvarande, måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till den nya servern.

- <!-- 2292271 - IS ASDK --> Om du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och en Plan som är associerad med en klientprenumeration, tillämpas inte den nya gränsen till den prenumerationen. Den nya gränsen gäller dock för nya prenumerationer som skapas när kvoten ökas. 

  Undvik problemet genom att använda en tilläggsplanen för att öka en kvot för datornätverk när planen är redan associerad med en prenumeration. Läs mer om hur du [tillgängliggöra en tilläggsplanen](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Du kan inte ta bort en prenumeration som har resurser som DNS-zon eller routningstabellen resurser som är associerade med den. Om du vill ta bort prenumerationen har, måste du först radera DNS-zon och routningstabellen resurser från prenumerationen klient. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- <!-- 16309153 - IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

- <!-- TBD - IS ASDK --> Azure Stack har inte stöd för att lägga till ytterligare nätverksgränssnitt i en VM-instans efter den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste de ha definierats vid tidpunkten för distribution.

- <!-- 2096388 IS --> Du kan inte använda administrationsportalen för att uppdatera regler för en grupp. 

    Lösning för App Service: Om du behöver fjärrskrivbord till Controller-instanser kan du ändra säkerhetsregler i nätverkssäkerhetsgrupper med PowerShell.  Följande är exempel på hur du *Tillåt*, och sedan återställa konfigurationen av *neka*:  
    
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

#### <a name="sql-and-mysql"></a>SQL- och MySQL

- <!-- TBD - IS --> Endast resursprovidern stöds för att skapa objekt på servrar som värd SQL eller MySQL. Objekt som har skapats på en värdserver som inte skapats med resursprovidern kan resultera i ett felaktigt tillstånd.  

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** eller **nivå** namn när du skapar en SKU för SQL- och MySQL-resursprovider.


> [!NOTE]  
> <!-- TBD - IS --> När du har uppdaterat till Azure Stack 1804 kan du fortsätta att använda SQL- och MySQL resursprovidrar som du tidigare har distribuerat.  Vi rekommenderar att du uppdaterar SQL- och MySQL när en ny version blir tillgänglig. Som Azure Stack gäller uppdateringar SQL och MySQL-resursprovidrar sekventiellt.  Till exempel om du använder version 1802 först installera version 1803 och uppdatera sedan till 1804.      
>   
> Installationen av uppdateringen 1804 påverkar inte den nuvarande användningen av SQL eller MySQL resursprovidrar av användarna.
> Oavsett vilken version av resursprovidrar som du använder dina data för användare i sina databaser är inte vidröras och är fortfarande tillgängliga.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

- <!-- TBD - IS ASDK --> För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.

- <!-- TBD - IS ASDK --> App Service kan bara distribueras till den **standard Providerprenumeration** just nu.  I en kommande uppdatering distribuerar App Service till den nya Avläsning av prenumeration som introducerades i Azure Stack 1804 och alla befintliga distributioner kommer att migreras till den här nya prenumerationen också.

#### <a name="usage"></a>Användning  
- <!-- TBD - IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned Azure Stack 1804 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
