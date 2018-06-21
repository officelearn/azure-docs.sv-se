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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 80ed0d2353fc6ea3a515c0d05475c713920abe46
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295749"
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
- Vi löst problemet som blockerats [öppnar en ny supportförfrågan från listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från i administrationsportal. Det här alternativet nu fungerar som avsett. 

- **Olika korrigeringar** för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Innan du börjar    

### <a name="prerequisites"></a>Förutsättningar
- Installera Azure-stacken [1804 uppdatera](azure-stack-update-1804.md) innan du installerar Azure Stack 1805 uppdateringen.    
- Innan du påbörjar installationen av uppdateringen 1805 kör [Test AzureStack](azure-stack-diagnostic-test.md) att verifiera statusen för din Azure-stacken och lösa alla operativa problem som hittas. Också granska aktiva aviseringar och lös eventuella som kräver åtgärder. 

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen   
- Du kan se aviseringar med rubriken under installationen av uppdateringen 1805 *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när 1805 uppdateringen är klar.   

- <!-- 2489559 - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar, seSe [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Steg efter uppdateringen
Installera alla tillämpliga snabbkorrigeringar efter installationen av 1805. Mer information läser du följande knowledge base-artiklar, samt våra [Servicing princip](azure-stack-servicing-policy.md).  
 - [KB 4340474 - Azure Stack snabbkorrigering 1.1805.4.53](https://support.microsoft.com/en-us/help/4340474).


## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Nedan följer efter installationen kända problem med den här versionen.

### <a name="portal"></a>Portalen  
- <!-- 2551834 - IS, ASDK --> När du väljer **översikt** för ett lagringskonto på den administratör eller användaren portaler, information från den *Essentials* fönstret inte visas.  Fönstret Essentials visar information om kontot som dess *resursgruppen*, *plats*, och *prenumerations-ID*.  Andra alternativ för översikt är tillgängliga som *Services* och *övervakning*, samt som alternativ till *öppna i Utforskaren i* eller *ta bort lagringskontot* . 

  Du kan visa informationen tillgänglig i [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-cmdlet. 

- <!-- 2551834 - IS, ASDK --> När du väljer **taggar** för ett lagringskonto på den administratör eller användaren portaler informationen läsas in och visas inte.  

  Du kan visa informationen tillgänglig i [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-cmdlet.


- <!-- 2332636 - IS -->  När du använder AD FS för Azure-stacken identitetssystem och uppdatera till den här versionen av Azure-stacken standardägaren av providern standardabonnemang återställs till inbyggt **CloudAdmin** användare.  
  Lösning: Lös problemet när du har installerat uppdateringen med steg 3 från de [utlösaren automation för att konfigurera anspråksproviderförtroende i Azure-stacken](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) proceduren för att återställa standard providern Prenumerationens ägare.   

- <!-- TBD - IS ASDK --> Vissa prenumerationstyper av administrativa är inte tillgängliga.  När du uppgraderar Azure stacken för den här versionen, två prenumerationstyper som har [introducerades i version 1804](azure-stack-update-1804.md#new-features) visas inte i konsolen. Detta är normalt. Tillgänglig prenumerationstyper är *avläsning prenumeration*, och *förbrukning prenumeration*. Dessa prenumerationstyper av visas i den nya Azure-stacken miljöer från och med version 1804 men ännu inte är redo för användning. Du bör fortsätta att använda den *Standard Provider* prenumerationstyp.  

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned i administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd de sökvägarna att navigera till ett tidigare blad i portalen genom att markera namnet på bladet som du vill visa i listan spåret som finns längst upp till vänster i portalen.
  ![Dynamiska länkar](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Det kanske inte går att visa beräkning eller lagring resurser i administratörsportalen. Orsaken till det här problemet uppstår ett fel under installationen av uppdateringen som gör att uppdateringen som ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- <!-- TBD - IS --> Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.

- <!-- TBD - IS ASDK --> Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- <!-- TBD - IS ASDK --> Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.


### <a name="health-and-monitoring"></a>Hälsa och övervakning
- <!-- 1264761 - IS ASDK -->  Du kan se aviseringar för den *hälsa controller* komponenten som har följande information:  

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

- <!-- 2368581 - IS. ASDK --> En Azure Stack-operator, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure-stacken stämpeln är slut på minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för din arbetsbelastning. 


### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> När du väljer en storlek på virtuell dator för distribution av virtuella datorer, vissa F-serien VM-storlekar visas inte som en del av Väljaren storlek när du skapar en virtuell dator. Storlek på följande Virtuella datorer visas inte i Väljaren: *F8s_v2*, *F16s_v2*, *F32s_v2*, och *F64s_v2*.  
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


- <!-- TBD - IS ASDK --> Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- <!-- TBD - IS --> När du skapar en tillgänglighetsuppsättning i portalen genom att gå till **ny** > **Compute** > **tillgänglighetsuppsättning**, du kan bara skapa en tillgänglighetsuppsättning med en feldomän och uppdateringsdomän 1. Som en lösning när du skapar en ny virtuell dator, skapa tillgänglighetsuppsättning med hjälp av PowerShell, CLI eller från portalen.

- <!-- TBD - IS ASDK --> När du skapar virtuella datorer på Azure-stacken användarportalen visar portalen ett felaktigt antal datadiskar som kan kopplas till DS-serien VM. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD - IS ASDK --> När en VM-avbildning misslyckas skapas kan en misslyckade objekt som du inte kan ta bort läggas till bladet VM-avbildningar beräkning.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

- <!-- TBD - IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en distribution av Virtuella datorer, bör användarna kan etablering timeout-värde i stället för att stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostik stöds inte i Azure-stacken. När du distribuerar en Linux VM med diagnostik för Virtuella datorer aktiveras misslyckas distributionen. Distributionen misslyckas även om du aktiverar Linux VM grundläggande mätvärden via diagnostikinställningar.  


### <a name="networking"></a>Nätverk
- <!-- TBD - IS ASDK --> Du kan skapa användardefinierade vägar antingen i portalen administratören eller användaren. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att ställa in en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **väg baserat** alternativet stöds i Azure-stacken.

- <!-- 2388980 - IS ASDK --> När en virtuell dator som associeras med en offentlig IP-adress, kan du koppla den virtuella datorn från att IP-adress. Disassociation verkar fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till en ny.

- <!-- 2292271 - IS ASDK --> Om du höjer en kvotgräns för en nätverksresurs som är en del av ett erbjudande och planera som är associerad med en klientprenumeration tillämpas inte den nya gränsen till prenumerationen. Den nya gränsen gäller dock att nya prenumerationer som skapas efter kvoten ökas.

  Undvik problemet genom att använda en plan för tillägg för att öka en kvot på nätverket när planen är redan kopplat till en prenumeration. Mer information finns i så här [tillgängliggöra en plan för tillägget](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Du kan inte ta bort en prenumeration som har DNS-zon eller routningstabellen resurser som är kopplade till den. Om du vill ta bort prenumerationen har, måste du först radera DNS-zonen och routningstabellen resurser från prenumerationen klient.


- <!-- 1902460 - IS ASDK --> Azure-stacken stöder en enda *lokal nätverksgateway* per IP-adress. Detta gäller över alla klient-prenumerationer. Efter att skapa den första gateway nätverksanslutningen, efterföljande försök att skapa en gateway för lokala nätverksresurser med samma IP-adress blockeras.

- <!-- 16309153 - IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server inställning av *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna är inte pushas till virtuella datorer i det virtuella nätverket.

- <!-- TBD - IS ASDK --> Azure-stacken stöder inte att lägga till ytterligare nätverksgränssnitt i en VM-instans när den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste ha definierats vid tidpunkten för distribution.

- <!-- 2096388 IS --> Du kan inte använda administrationsportal för att uppdatera regler för en nätverkssäkerhetsgrupp.

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

- <!-- TBD - IS --> Endast resursprovidern stöds för att skapa objekt på servrar som SQL-värd eller MySQL. Objekt som har skapats på en värdserver som inte har skapats av resursprovidern kan resultera i ett felaktigt tillstånd.  

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** eller **nivå** namn när du skapar en SKU för SQL och MySQL-resursprovidrar.


> [!NOTE]  
> <!-- TBD - IS --> När du har uppdaterat till Azure-stacken 1805 kan du fortsätta att använda SQL och MySQL resursproviders som du tidigare har distribuerats.  Vi rekommenderar att du uppdaterar SQL och MySQL när en ny version blir tillgänglig. T.ex. Azure-stacken, tillämpa uppdateringar SQL och MySQL resursprovidrar sekventiellt. Till exempel om du använder version 1803 först installera version 1804 och uppdatera sedan till 1805.      
>   
> Installationen av uppdateringen 1805 påverkar inte den nuvarande användningen av SQL- eller MySQL resursproviders av användare.
> Dina användare data i sina databaser oavsett vilken version av resursleverantörer som du använder är inte vidröras och förblir tillgängligt.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Användare måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

- <!-- 2489178 - IS ASDK --> För att skala ut infrastruktur (anställda, hantering, frontend roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.

- <!-- TBD - IS ASDK --> Apptjänst kan endast distribueras till den *prenumeration på Default leverantör* just nu. I en kommande uppdatering distribuerar Apptjänst till den nya *avläsning prenumeration* som introducerades i Azure-stacken 1804. Alla befintliga distributioner kommer att migreras till den här nya prenumerationstypen när avläsning stöds för användning.


### <a name="usage"></a>Användning  
- <!-- TBD - IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned uppdateringspaketet Azure Stack 1805 från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda Privilegierade slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure-stacken, se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md).
- Mer information om hur du installerar uppdateringar med Azure-stacken finns [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md).
