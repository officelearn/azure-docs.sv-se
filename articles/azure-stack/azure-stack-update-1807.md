---
title: Azure Stack 1807 Update | Microsoft Docs
description: Läs mer om nyheter i uppdateringen 1807 för integrerade Azure Stack-system, inklusive kända problem och var du kan hämta uppdateringen.
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
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228112"
---
# <a name="azure-stack-1807-update"></a>Uppdatering av Azure Stack 1807

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskriver innehållet i 1807 uppdateringspaketet. Den här uppdateringen innehåller förbättringar, korrigeringar och kända problem för den här versionen av Azure Stack och var du kan hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure Stack 1807 update build-nummer är **1.1807.XX.X**.  


### <a name="new-features"></a>Nya funktioner
Den här uppdateringen innehåller följande förbättringar för Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Starta säkerhetskopieringar på ett fördefinierat schema** – som en enhet, Azure Stack kan nu automatiskt utlösa infrastruktur säkerhetskopieringar med jämna mellanrum för att eliminera mänsklig inblandning. Azure Stack kommer automatiskt att rensa upp den externa resursen för säkerhetskopieringar som är äldre än definierade kvarhållningsperioden. 

- <!-- 2496385 | ASDK, IS --> **Har lagts till dataöverföring tid i den totala tiden för säkerhetskopieringen.**

-   <!-- 1702130 | ASDK, IS --> **Kapacitet för säkerhetskopiering externa visar nu rätt kapaciteten för den externa resursen.** (Detta var tidigare hårdkoda till 10 GB.)

- <!-- 2508488 |  IS   -->   Utöka kapaciteten genom [att lägga till ytterligare skalning enhet noder](azure-stack-add-scale-node.md).



### <a name="fixed-issues"></a>Åtgärdade problem

- <!-- 448955 | IS ASDK --> Du kan nu har fråga aktivitetsloggar för system som har distribuerats i en UTC + N tidszon.    

- <!-- 2319627 |  ASDK, IS --> Förkontroll för konfiguration av säkerhetskopiering parametrar (sökväg/användarnamn/lösenord/krypteringsnyckeln) anger inte längre felaktiga inställningar till konfigurationen för säkerhetskopiering. (Tidigare felaktiga inställningar har angetts i säkerhetskopieringen och säkerhetskopiering skulle skulle misslyckas när tirggered.)

- <!-- 2215948 |  ASDK, IS --> Säkerhetskopieringen i listan uppdaterar nu när du manuellt ta bort säkerhetskopian från den externa resursen.

- <!-- 2332636 - IS -->  Uppdatera till den här versionen återställs inte längre standardägaren av providern Standardprenumeration för inbyggda CloudAdmin användaren när de distribueras med AD FS.

- <!-- 2360715 |  ASDK, IS -->  När du har konfigurerat datacenter-integrering kan du inte längre komma åt AD FS-metadatafilen från en resurs. Mer information finns i [om du konfigurerar AD FS-integrationen genom att tillhandahålla federation metadatafil](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Vi har åtgärdat ett problem att förhindras användare från som har tilldelats en befintlig offentlig IP-adress som hade tidigare tilldelats en nätverksgränssnitt eller belastningsutjämnaren till en ny nätverksgränssnitt eller belastningsutjämnare.  

-   <!--2292271 | ASDK, IS --> Vi ett problem har åtgärdats där en ändrad kvotgränsen inte tillämpas för befintliga prenumerationer.  Nu när du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och Plan som är associerade med en klientprenumeration, gäller den nya gränsen för befintliga prenumerationer, samt nya prenumerationer.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack.


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Innan du börjar    

### <a name="prerequisites"></a>Förutsättningar
- Installera Azure Stack [1805 uppdatera](azure-stack-update-1805.md) innan du installerar Azure Stack 1807 uppdateringen.  Det fanns ingen uppdatering 1806.  

- Innan du påbörjar installationen av uppdateringen 1807 kör [Test AzureStack](azure-stack-diagnostic-test.md) att verifiera statusen för din Azure Stack och lösa alla operativa problem hittades. Även granska aktiva aviseringar och lösningar som kräver åtgärd.

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen   
- <!-- 2468613 - IS --> Under installationen av den här uppdateringen kan du se aviseringar med rubriken *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.   

- <!-- 2489559 - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Steg efter uppdateringen
*Det finns inga efter uppdateringen åtgärder för uppdatering 1807.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Här följer efter installation kända problem för den här build-versionen.

### <a name="portal"></a>Portalen  
- <!-- 2551834 - IS, ASDK --> När du väljer **översikt** för ett lagringskonto på administratören eller användaren portalerna kan informationen från den *Essentials* fönstret visas inte.  Fönstret information visar information om kontot som dess *resursgrupp*, *plats*, och *prenumerations-ID*.  Andra alternativ för översikt över är tillgängliga, t.ex. *Services* och *övervakning*, samt som alternativ till *öppna i Explorer* eller *ta bort lagringskonto* .

  Du kan visa informationen inte tillgänglig i [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-cmdlet.

- <!-- 2551834 - IS, ASDK --> När du väljer **taggar** för ett lagringskonto i administratören eller användaren portaler informationen inte kan läsas in och visas inte.  

  Du kan visa informationen inte tillgänglig i [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-cmdlet. 

- <!-- TBD - IS ASDK --> Vissa typer av administrativa prenumerationer är inte tillgängliga.  När du uppgraderar Azure Stack i den här versionen, två prenumerationstyper som var [introducerades i version 1804](azure-stack-update-1804.md#new-features) visas inte i konsolen. Detta är normalt. Typerna som inte tillgänglig prenumeration är *Avläsning av prenumeration*, och *förbrukning prenumeration*. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den *Standard Provider* prenumerationstyp.  

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Det kanske inte går att visa resurser för beräkning eller lagring i administratörsportalen. Orsaken till problemet är ett fel under installationen av uppdateringen som gör att uppdateringen ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- <!-- TBD - IS --> Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du kugghjulsikonen i det övre högra hörnet i portalen och välj sedan **Återställ standardinställningarna**.

- <!-- TBD - IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD - IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.


### <a name="health-and-monitoring"></a>Hälsa och övervakning
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

  Båda aviseringarna kan ignoreras och de stängs automatiskt över tid.  

- <!-- 2368581 - IS. ASDK --> Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.


### <a name="compute"></a>Compute
- <!-- 2724873 - IS --> När du använder PowerShell-cmdlets **Start AzsScaleUnitNode** eller **Stop-AzsScaleunitNode** för att hantera skalningsenheter, det första försöket att starta eller stoppa skalningsenheten kan misslyckas. Om cmdleten misslyckas på den första körningen, kör du cmdlet en gång. Den andra körningen bör fungera för att slutföra åtgärden. 

- <!-- 2494144 - IS, ASDK --> När du väljer en VM-storlek för en distribution av virtuella datorer, vissa F-seriens storlekar visas inte som en del av storlek Väljaren när du skapar en virtuell dator. Storlek på följande Virtuella datorer visas inte i Väljaren: *F8s_v2*, *F16s_v2*, *F32s_v2*, och *F64s_v2*.  
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

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

- <!-- TBD - IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  


### <a name="networking"></a>Nätverk  

- <!-- 1766332 - IS ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

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
> <!-- TBD - IS --> När du har uppdaterat till den här versionen av Azure Stack kan du fortsätta att använda SQL- och MySQL resursprovidrar som du tidigare har distribuerat.  Vi rekommenderar att du uppdaterar SQL- och MySQL när en ny version blir tillgänglig. Som Azure Stack gäller uppdateringar SQL och MySQL-resursprovidrar sekventiellt. Till exempel om du använder version 1804 först tillämpa versionen 1805 och uppdatera sedan till 1807.      
>   
> Installationen av uppdateringen påverkar inte den nuvarande användningen av SQL eller MySQL resursprovidrar av användarna.
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
Du kan ladda ned Azure Stack 1807 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
