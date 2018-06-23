---
title: Azure-stacken 1804 Update | Microsoft Docs
description: Lär dig mer om vad som finns i 1804 uppdateringen för Azure-stacken integrerat system, kända problem och var du kan hämta uppdateringen.
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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 9fc58d971db18db63e4dc76123ff1311b77e0191
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316441"
---
# <a name="azure-stack-1804-update"></a>Azure-stacken 1804 uppdatering

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i 1804 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i direkt relaterad till uppdateringen problem och med version (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet gäller bara för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure-stacken 1804 uppdatera versionsnumret är **20180513.1**.   

### <a name="new-features"></a>Nya funktioner
Den här uppdateringen innehåller följande förbättringar av Azure-stacken.

- <!-- 15028744 - IS -->  **Visual Studio-stöd för den frånkopplade Azure Stack distributioner med hjälp av AD FS**. I Visual Studio du nu kan lägga till prenumerationer och autentisera federerade med hjälp av AD FS användarens autentiseringsuppgifter. 
 
- <!-- 1779474, 1779458 - IS --> **Använda Av2 och F serien virtuella datorer**. Azure-stacken kan nu använda virtuella datorer baserat på storlekar för virtuella datorer med Av2-serien och F-serien. Mer information finns i [storlekar för virtuella datorer stöds i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Nya administrativa prenumerationer**. Med 1804 finns det två nya prenumerationstyper av i portalen. Dessa nya prenumerationstyper är utöver Standard Provider-prenumeration och visas med den nya Azure-stacken installationer från och med version 1804. *Använd inte typerna prenumeration med den här versionen av Azure-stacken*. Vi kommer att meddela tillgängligheten för att använda dessa prenumerationstyper av i en kommande uppdatering. 

  Om du uppdaterar Azure Stack till version 1804 syns inte de två typerna för ny prenumeration. Dock nya distributioner av Azure-stacken integrerat system och installationer av Azure-stacken Development Kit version 1804 eller senare har åtkomst till alla prenumerationstyper av tre.  

  Dessa nya prenumerationstyper är en del av en större ändring att säkra Standard Provider-prenumeration och för att göra det enklare att distribuera delade resurser som värd för SQL-servrar. När vi lägger till flera delar av den här större ändringar med framtida uppdateringar till Azure-stacken kan resurser har distribuerats under typerna prenumeration gå förlorade. 

  De tre prenumerationen nu visas är:  
  - Standard prenumeration på leverantör: fortsätta att använda den här prenumerationstypen. 
  - Avläsning av prenumerationen: *inte använder den här prenumerationstypen.*
  - Förbrukning prenumerationen: *inte använder den här prenumerationstypen*

  



## <a name="fixed-issues"></a>Fast problem

- <!-- IS, ASDK -->  I administrationsportal behöver du inte längre uppdatera panelen uppdatering innan den visar information.
 
- <!-- 2050709  -->  Du kan nu använda administrationsportal för att redigera storage-mätvärden för Blob-tjänsten, tabell och kötjänsten.
 
- <!-- IS, ASDK --> Under **nätverk**när du klickar på **anslutning** att ställa in en VPN-anslutning **plats-till-plats (IPsec)** nu är det enda tillgängliga alternativet.

- **Olika korrigeringar** för operativsystemet som används av Azure-stacken, säkerhet, stabilitet och prestanda.

## <a name="additional-releases-timed-with-this-update"></a>Ytterligare versioner tidsgränsen med den här uppdateringen  
Följande är nu tillgängliga, men kräver inte Azure Stack-uppdatering 1804.
- **Uppdatera till Microsoft Azure-stacken System Center Operations Manager Övervakningspaket**. Det finns en ny version (1.0.3.0) av Microsoft System Center Operations Manager Monitoring Pack för Azure-Stack för [hämta](https://www.microsoft.com/download/details.aspx?id=55184). Med den här versionen kan du använda tjänstens huvudnamn när du lägger till en ansluten Azure Stack-distribution. Den här versionen har också en uppdateringshantering upplevelse som gör att du kan ta Reparationsåtgärd direkt från Operations Manager. Det finns också nya instrumentpaneler som visar resursproviders, skalningsenheter och skala enhet noder.

- **Nya Azure stacken Admin PowerShell Version 1.3.0**.  Azure-stacken PowerShell 1.3.0 är nu tillgängliga för installation. Den här versionen innehåller kommandon för alla providrar för administratören att hantera Azure-stacken.  Med den här versionen innehåll att bli inaktuell från Azure-stacken verktyg GitHub [databasen](https://github.com/Azure/AzureStack-Tools). 

   För information om installationen, följer du de [instruktioner](azure-stack-powershell-install.md) eller [hjälp](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) innehåll för Azure-stacken modulen 1.3.0. 

- **Inledande versionen av Azure Rest API-referens Stack**. Den [API-referens för alla Azure-stacken Admin-providrar](https://docs.microsoft.com/rest/api/azure-stack/) publiceras nu. 


## <a name="before-you-begin"></a>Innan du börjar    

### <a name="prerequisites"></a>Förutsättningar
- Installera Azure-stacken [1803 uppdatera](azure-stack-update-1803.md) innan du installerar Azure Stack 1804 uppdateringen.    

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen   
- Du kan se aviseringar med rubriken under installationen av uppdateringen 1804 *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när 1804 uppdateringen är klar.   
 
- <!-- TBD - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Steg efter uppdateringen
Installera alla tillämpliga snabbkorrigeringar efter installationen av 1804. Mer information läser du följande knowledge base-artiklar, samt våra [Servicing princip](azure-stack-servicing-policy.md).  
 - [KB 4341392 - Azure Stack snabbkorrigering 1.0.180523.11](https://support.microsoft.com/en-us/help/4341392).




### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Följande är efter installationen kända problem för version **20180513.1**.

#### <a name="portal"></a>Portalen
- <!-- 1272111 - IS --> Du kan inte visa Azure-stacken skalenheter i administrationsportal när du installerar eller uppdatera till den här versionen av Azure-stacken.  
  Lösning: Använd PowerShell för att visa information om Skalningsenheter. Mer information finns i [hjälp](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) innehåll för Azure-stacken modulen 1.3.0. 

- <!-- 2332636 - IS -->  När du använder AD FS för Azure-stacken identitetssystem och uppdatera till den här versionen av Azure-stacken standardägaren av providern standardabonnemang återställs till inbyggt **CloudAdmin** användare.  
  Lösning: Lös problemet när du har installerat uppdateringen med steg 3 från de [utlösaren automation för att konfigurera anspråksproviderförtroende i Azure-stacken](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) proceduren för att återställa standard providern Prenumerationens ägare.   

- <!-- TBD - IS ASDK --> Vissa prenumerationstyper av administrativa är inte tillgängliga.  När du uppgraderar Azure stacken för den här versionen, två prenumerationstyper som har [introducerades i version 1804](#new-features) visas inte i konsolen. Detta är normalt. Tillgänglig prenumerationstyper är *avläsning prenumeration*, och *förbrukning prenumeration*. Dessa prenumerationstyper av visas i den nya Azure-stacken miljöer från och med version 1804 men ännu inte är redo för användning. Du bör fortsätta att använda den *Standard Provider* prenumerationstyp.  


- <!-- TBD -  IS ASDK -->Möjlighet [att öppna en ny supportförfrågan i listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - För Azure-Stack integrerad system använda https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned i administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd de sökvägarna att navigera till ett tidigare blad i portalen genom att markera namnet på bladet som du vill visa i listan spåret som finns längst upp till vänster i portalen.
  ![Dynamiska länkar](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Det kanske inte går att visa beräkning eller lagring resurser i administratörsportalen. Orsaken till det här problemet uppstår ett fel under installationen av uppdateringen som gör att uppdateringen som ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- <!-- TBD - IS --> Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.

- <!-- TBD - IS ASDK --> Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- <!-- TBD - IS ASDK --> Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.

- <!-- TBD - IS ASDK --> I administrationsportal kan du se en kritisk varning för den *Microsoft.Update.Admin* komponent. Varningens namn, beskrivning och reparation alla visas som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

  Den här aviseringen kan ignoreras. 


#### <a name="health-and-monitoring"></a>Hälsa och övervakning
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

  Båda aviseringarna kan ignoreras. De stängs automatiskt med tiden.  
 

#### <a name="compute"></a>Compute
- <!-- TBD - IS --> När du väljer en storlek på virtuell dator för distribution av virtuella datorer, vissa F-serien VM-storlekar visas inte som en del av Väljaren storlek när du skapar en virtuell dator. Storlek på följande Virtuella datorer visas inte i Väljaren: *F8s_v2*, *F16s_v2*, *F32s_v2*, och *F64s_v2*.  
  Som en lösning att använda någon av följande metoder för att distribuera en virtuell dator. Du måste ange VM-storlek som du vill använda för varje metod.

  - **Azure Resource Manager-mall:** när du använder en mall i *vmSize* i mallen ska vara lika med den önskade VM-storleken. Till exempel följande används för att distribuera en virtuell dator som använder den *F32s_v2* storlek:  

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

- <!-- TBD - IS ASDK --> När du skapar virtuella datorer på Azure-stacken användarportalen visar portalen ett felaktigt antal datadiskar som kan kopplas till D-serien VM. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD - IS ASDK --> När en VM-avbildning misslyckas skapas kan en misslyckade objekt som du inte kan ta bort läggas till bladet VM-avbildningar beräkning.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

- <!-- TBD - IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en distribution av Virtuella datorer, bör användarna kan etablering timeout-värde i stället för att stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostik stöds inte i Azure-stacken. När du distribuerar en Linux VM med diagnostik för Virtuella datorer aktiveras misslyckas distributionen. Distributionen misslyckas även om du aktiverar Linux VM grundläggande mätvärden via diagnostikinställningar.  


#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att ställa in en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **väg baserat** alternativet stöds i Azure-stacken.

- <!-- 2388980 - IS ASDK --> När en virtuell dator som associeras med en offentlig IP-adress, kan du koppla den virtuella datorn från att IP-adress. Disassociation verkar fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny.

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

#### <a name="sql-and-mysql"></a>SQL- och MySQL

- <!-- TBD - IS --> Endast resursprovidern stöds för att skapa objekt på servrar som SQL-värd eller MySQL. Objekt som har skapats på en värdserver som inte har skapats av resursprovidern kan resultera i ett felaktigt tillstånd.  

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** eller **nivå** namn när du skapar en SKU för SQL och MySQL-resursprovidrar.


> [!NOTE]  
> <!-- TBD - IS --> När du har uppdaterat till Azure-stacken 1804 kan du fortsätta att använda SQL och MySQL resursproviders som du tidigare har distribuerats.  Vi rekommenderar att du uppdaterar SQL och MySQL när en ny version blir tillgänglig. T.ex. Azure-stacken, tillämpa uppdateringar SQL och MySQL resursprovidrar sekventiellt.  Till exempel om du använder version 1802 först installera version 1803 och uppdatera sedan till 1804.      
>   
> Installationen av uppdateringen 1804 påverkar inte den nuvarande användningen av SQL- eller MySQL resursproviders av användare.
> Dina användare data i sina databaser oavsett vilken version av resursleverantörer som du använder är inte vidröras och förblir tillgängligt.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Användare måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

- <!-- TBD - IS ASDK --> För att skala ut infrastruktur (anställda, hantering, frontend roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.

- <!-- TBD - IS ASDK --> Apptjänst kan endast distribueras till den **standard prenumeration på leverantör** just nu.  I en kommande uppdatering Apptjänst ska distribuera till den nya avläsning prenumerationen som introducerades i Azure-stacken 1804 och alla befintliga distributioner kommer att migreras till den här nya prenumerationen också.

#### <a name="usage"></a>Användning  
- <!-- TBD - IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned uppdateringspaketet Azure Stack 1804 från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda Privilegierade slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure-stacken, se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md).
- Mer information om hur du installerar uppdateringar med Azure-stacken finns [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md).
