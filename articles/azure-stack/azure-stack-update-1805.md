---
title: Azure Stack 1805 Update | Microsoft Docs
description: Läs mer om nyheter i uppdateringen 1805 för integrerade Azure Stack-system, inklusive kända problem och var du kan hämta uppdateringen.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 3ef910cc588b095b0c10f5e0928d7fadf17113f6
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989817"
---
# <a name="azure-stack-1805-update"></a>Uppdatering av Azure Stack 1805

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskrivs förbättringarna och korrigeringar i 1805 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure Stack 1805 update build-nummer är **1.1805.1.47**.  

> [!TIP]  
> Baserat på feedback från kunder, finns det en uppdatering av schemat version som används för Microsoft Azure Stack.  Från och med den här uppdateringen kan 1805, representerar det nya schemat bättre den aktuella molnversionen.  
> 
> Version-schemat är nu *Version.YearYearMonthMonth.MinorVersion.BuildNumber* där andra och tredje uppsättningarna tyda på version och utgåva. Till exempel 1805.1 representerar den *versionen till tillverkning* (RTM)-versionen av 1805.  


### <a name="new-features"></a>Nya funktioner
Den här uppdateringen innehåller följande förbättringar för Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack innehåller nu en *Syslog* klienten** som en *förhandsgranskningsfunktion*. Den här klienten gör att vidarebefordring av gransknings- och loggar som är relaterade till Azure Stack-infrastruktur till en Syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara som är extern till Azure Stack. Syslog-klienten stöder för närvarande endast oautentiserade UDP-anslutningar via standardporten 514. Nyttolasten för varje Syslog-meddelande är formaterad i Common Event Format (CEF). 

  Du konfigurerar Syslog-klienten använder den **Set-SyslogServer** cmdleten som visas i den privilegierade slutpunkten. 

  Med den här förhandsversionen kan du se följande tre aviseringar. När den får från Azure Stack, dessa aviseringar innehåller *beskrivningar* och *reparation* vägledning. 
  - Rubrik: Kodintegritet av  
  - Rubrik: Kodintegritet i granskningsläge 
  - Rubrik: Användarkontot som skapade

  Den här funktionen är i förhandsversion, det bör inte förlita sig på detta i produktionsmiljöer.   




### <a name="fixed-issues"></a>Åtgärdade problem

<!-- # - applicability -->
- Vi har åtgärdat problemet som blockerats [att öppna en ny supportbegäran i listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från i administrationsportalen. Det här alternativet nu fungerar som avsett. 

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Innan du börjar    

### <a name="prerequisites"></a>Förutsättningar
- Installera Azure Stack [1804 uppdatera](azure-stack-update-1804.md) innan du installerar Azure Stack 1805 uppdateringen.    
- Innan du påbörjar installationen av uppdateringen 1805 kör [Test AzureStack](azure-stack-diagnostic-test.md) att verifiera statusen för din Azure Stack och lösa alla operativa problem hittades. Även granska aktiva aviseringar och lösningar som kräver åtgärd. 

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen   
- Du kan se aviseringar med rubriken under installationen av uppdateringen 1805 *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när uppdateringen till 1805 är klar.   

- <!-- 2489559 - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar, seSe [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Steg efter uppdateringen
När installationen av 1805, installerar du eventuella tillämpliga snabbkorrigeringar. Visa mer information i följande artiklar i kunskapsbasen, samt våra [Servicing princip](azure-stack-servicing-policy.md).  
 - [KB 4344102 – Azure Stack-snabbkorrigeringen 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Här följer efter installation kända problem för den här build-versionen.

### <a name="portal"></a>Portalen  
- <!-- 2551834 - IS, ASDK --> När du väljer **översikt** för ett lagringskonto på administratören eller användaren portalerna kan informationen från den *Essentials* fönstret visas inte.  Fönstret information visar information om kontot som dess *resursgrupp*, *plats*, och *prenumerations-ID*.  Andra alternativ för översikt över är tillgängliga, t.ex. *Services* och *övervakning*, samt som alternativ till *öppna i Explorer* eller *ta bort lagringskonto* . 

  Du kan visa informationen inte tillgänglig i [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-cmdlet. 

- <!-- 2551834 - IS, ASDK --> När du väljer **taggar** för ett lagringskonto i administratören eller användaren portaler informationen inte kan läsas in och visas inte.  

  Du kan visa informationen inte tillgänglig i [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-cmdlet.


- <!-- 2332636 - IS -->  När du använder AD FS för Azure Stack-identitetssystemet och uppdatera till den här versionen av Azure Stack standardägaren av providern Standardprenumeration återställs till inbyggt **CloudAdmin** användare.  
  Lösning: Lös problemet när du har installerat uppdateringen med steg 3 i den [utlösa automatisering för att konfigurera anspråk providern förtroende i Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) proceduren för att återställa standard-providern Prenumerationens ägare.   

- <!-- TBD - IS ASDK --> Vissa typer av administrativa prenumerationer är inte tillgängliga.  När du uppgraderar Azure Stack i den här versionen, två prenumerationstyper som var [introducerades i version 1804](azure-stack-update-1804.md#new-features) visas inte i konsolen. Detta är normalt. Typerna som inte tillgänglig prenumeration är *Avläsning av prenumeration*, och *förbrukning prenumeration*. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den *Standard Provider* prenumerationstyp.  

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Det kanske inte går att visa resurser för beräkning eller lagring i administratörsportalen. Orsaken till problemet är ett fel under installationen av uppdateringen som gör att uppdateringen ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- <!-- TBD - IS --> Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du kugghjulsikonen i det övre högra hörnet i portalen och välj sedan **Återställ standardinställningarna**.

- <!-- TBD - IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD - IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.


### <a name="health-and-monitoring"></a>Hälsa och övervakning
- <!-- 1264761 - IS ASDK -->  Du kan se aviseringar för den *hälsotillstånd controller* komponent som har följande information:  
- 
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

  Båda aviseringarna #1 och #2 kan ignoreras och de stängs automatiskt över tid. 

  Du kan också se följande avisering för *kapacitet*. Procentandelen minne som anges i beskrivningen kan variera för den här aviseringen:  

  Avisera #3:
   - NAMN: Låg minneskapacitet
   - ALLVARLIGHETSGRAD: kritisk
   - KOMPONENT: kapacitet
   - Beskrivning: Regionen har förbrukat fler än 80.00% av det tillgängliga minnet. Skapa virtuella datorer med stora mängder minne kan misslyckas.  

  I den här versionen av Azure Stack, kan den här aviseringen utlöses felaktigt. Om virtuella datorer fortsätter att distribuera har kan ignorera du den här aviseringen. 
  
  Aviseringen #3 stängs inte automatiskt. Om du stänger aviseringen skapar Azure Stack samma avisering inom 15 minuter.  

- <!-- 2368581 - IS. ASDK --> Som Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar. 


### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> När du väljer en VM-storlek för en distribution av virtuella datorer, vissa F-seriens storlekar visas inte som en del av storlek Väljaren när du skapar en virtuell dator. Storlek på följande Virtuella datorer visas inte i Väljaren: *F8s_v2*, *F16s_v2*, *F32s_v2*, och *F64s_v2*.  
  Som en lösning kan du använda en av följande metoder för att distribuera en virtuell dator. Du måste ange VM-storlek som du vill använda i varje metod.

  - **Azure Resource Manager-mall:** när du använder en mall kan du ange den *vmSize* i mallen ska vara lika med storleken som du vill använda. Till exempel följande post används för att distribuera en virtuell dator som använder den *F32s_v2* storlek:  

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

- <!-- TBD - IS ASDK --> När du skapar virtuella datorer på Azure Stack-användarportalen visar portalen ett felaktigt antal datadiskar som kan ansluta till virtuella datorer i DS-serien. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD - IS ASDK --> När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försöka redownload VM-avbildning som tidigare misslyckats.

- <!-- TBD - IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  


### <a name="networking"></a>Nätverk
- <!-- TBD - IS ASDK --> Du kan inte skapa användardefinierade vägar i antingen portalen administratör eller användare. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 2388980 - IS ASDK --> När en virtuell dator skapas och som är associerade med en offentlig IP-adress, kan du kopplingen mellan den virtuella datorn från IP-adress. Avassociationsåtgärden ser ut att fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn.

  För närvarande, måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till den nya servern.

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


### <a name="sql-and-mysql"></a>SQL- och MySQL

- <!-- TBD - IS --> Endast resursprovidern stöds för att skapa objekt på servrar som värd SQL eller MySQL. Objekt som har skapats på en värdserver som inte skapats med resursprovidern kan resultera i ett felaktigt tillstånd.  

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** eller **nivå** namn när du skapar en SKU för SQL- och MySQL-resursprovider.


> [!NOTE]  
> <!-- TBD - IS --> När du har uppdaterat till Azure Stack 1805 kan du fortsätta att använda SQL- och MySQL resursprovidrar som du tidigare har distribuerat.  Vi rekommenderar att du uppdaterar SQL- och MySQL när en ny version blir tillgänglig. Som Azure Stack gäller uppdateringar SQL och MySQL-resursprovidrar sekventiellt. Till exempel om du använder version 1803 först tillämpa versionen 1804 och uppdatera sedan till 1805.      
>   
> Installationen av uppdateringen 1805 påverkar inte den nuvarande användningen av SQL eller MySQL resursprovidrar av användarna.
> Oavsett vilken version av resursprovidrar som du använder dina data för användare i sina databaser är inte vidröras och är fortfarande tillgängliga.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

- <!-- 2489178 - IS ASDK --> För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.

- <!-- TBD - IS ASDK --> App Service kan bara distribueras till den *standard providerprenumeration* just nu. I en kommande uppdatering distribuerar App Service till den nya *Avläsning av prenumeration* som introducerades i Azure Stack 1804. När Avläsning av programvara stöds för användning, kommer alla befintliga distributioner att migreras till den här nya prenumerationstypen.


### <a name="usage"></a>Användning  
- <!-- TBD - IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned Azure Stack 1805 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
