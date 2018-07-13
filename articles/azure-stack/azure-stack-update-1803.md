---
title: Azure Stack 1803 Update | Microsoft Docs
description: Lär dig mer om vad som finns i 1803 uppdateringen för Azure Stack integrerade system, kända problem och var du kan hämta uppdateringen.
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
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989579"
---
# <a name="azure-stack-1803-update"></a>Uppdatering av Azure Stack-1803

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskrivs förbättringarna och korrigeringar i 1803 uppdateringspaketet, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]        
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens    
Azure Stack 1803 update build-nummer är **20180329.1**.


## <a name="before-you-begin"></a>Innan du börjar    
> [!IMPORTANT]    
> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Förutsättningar
- Installera Azure Stack [1802 uppdatera](azure-stack-update-1802.md) innan du installerar Azure Stack 1803 uppdateringen.   

- Installera **AzS Hotfix – 1.0.180312.1-Skapa 20180222.2** innan du installerar Azure Stack 1803 uppdateringen. Den här snabbkorrigeringen uppdaterar Windows Defender och är tillgänglig när du laddar ned uppdateringar för Azure Stack.

  Installera snabbkorrigeringen enligt de vanliga procedurerna för [installerar uppdateringar för Azure Stack](azure-stack-apply-updates.md). Namnet på uppdateringen som visas som **AzS Hotfix – 1.0.180312.1**, och innehåller följande filer: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.XML

  När du har överfört filerna till ett lagringskonto och en behållare, kör du installationen från panelen Update admin portal. 
  
  Till skillnad från uppdatering för Azure Stack ändras installerar den här uppdateringen inte versionen av Azure Stack. Kontrollera den här uppdateringen har installerats genom att visa en lista över **installerade uppdateringar**.



### <a name="new-features"></a>Nya funktioner 
Den här uppdateringen innehåller följande förbättringar och korrigeringar för Azure Stack.

- **Uppdatera Azure Stack-hemligheter** – (konton och certifikat). Läs mer om hur du hanterar hemligheter [rotera hemligheter i Azure Stack](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatisk omdirigering till HTTPS** när du använder HTTP för att få åtkomst till administratörs- och portaler. Den här förbättringen gjordes utifrån [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback om Azure Stack. 

- <!-- 2202621  --> **Komma åt Marketplace** – nu kan du öppna Azure Stack Marketplace genom att använda den [+ ny](https://ms.portal.azure.com/#create/hub) alternativet från inom administratörs- och portaler på samma sätt som du gör i Azure-portaler.
 
- <!-- 2202621 --> **Azure Monitor** -lägger till Azure Stack [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) till administratörs- och portaler. Detta inkluderar nya Utforskare för mått och aktivitet. För att komma åt den här Azure Monitor från externa nätverk port **13012** måste vara öppna i brandväggskonfigurationer. Mer information om portar som krävs av Azure Stack finns i [datacenter-integrering med Azure Stack - publicera slutpunkter](azure-stack-integrate-endpoints.md).

   Även som en del av detta ändrar, under **fler tjänster**, *granskningsloggar* visas nu som *aktivitetsloggar*. Funktionerna är nu konsekvent med Azure-portalen. 

- <!-- 1664791 --> **Sparse-filer** – när du lägger till en ny avbildning till Azure Stack eller lägga till en bild via marketplace syndikering avbildningen konverteras till en sparse-fil. Bilder som har lagts till innan du använder Azure Stack-version 1803 kan inte konverteras. I stället måste du använda marketplace syndikering för att skicka dessa avbildningar för att dra nytta av den här funktionen. 
 
   Sparse-filer är en effektiv format används för att minska användningen av utrymme för lagring och förbättra i/o.  Mer information finns i [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) för Windows Server. 

### <a name="fixed-issues"></a>Åtgärdade problem

- <!-- 1739988 --> Intern belastningsutjämning (ILB) nu hanterar korrekt MAC-adresser för backend-virtuella datorer, vilket gör interna Belastningsutjämnaren för att ta bort paket till backend-nätverket när du använder Linux-instanser på backend-nätverket. ILB fungerar bra med Windows-instanser i backend-nätverket. 

- <!-- 1805496 --> Ett problem där VPN-anslutningar mellan Azure Stack skulle bli frånkopplad på grund av Azure Stack med olika inställningar för IKE-principer än Azure. Värdena för SALifetime (tid) och SALiftetime (byte) inte är kompatibla med Azure och har ändrats i 1803 att matcha inställningar med Azure. Värdet för SALifetime (sekunder) innan 1803 var 14 400 och nu ändringar till 27 000 i 1803. Värdet för SALifetime (byte) före 1803 var 819,200 och ändringar av 33,553,408 i 1803.

- <!-- 2209262 --> IP-problem där VPN-anslutningar tidigare fanns i portalen. men har att aktivera eller ändrar IP-vidarebefordran ingen effekt. Funktionen är aktiverad som standard och möjligheten att ändra detta stöds inte än.  Kontrollen har tagits bort från portalen. 

- <!-- 1766332 --> Azure Stack stöder inte principen baserat VPN-Gateways, även om alternativet visas i portalen.  Alternativet har tagits bort från portalen. 

- <!-- 1868283 --> Azure Stack förhindrar nu storleksändring av en virtuell dator som har skapats med dynamiska diskar. 

- <!-- 1756324 --> Användningsdata för virtuella datorer är nu uppdelad i timmen. Det här är konsekvent med Azure. 

- <!--  2253274 --> Problemet där i portalerna administratörs- och bladet inställningar för virtuellt nätverk undernät inte kan läsas in. Som en lösning kan du använda PowerShell och [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet för att visa och hantera den här informationen.

- När du skapar en virtuell dator, meddelandet *det går inte att visa prissättning* visas inte längre när du väljer en storlek för virtuella datorstorlek.

- Olika korrigeringar för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack.


### <a name="changes"></a>Ändringar
- Ett sätt att ändra tillståndet för en nyligen skapade erbjudande från *privata* till *offentliga* eller *inaktiverade* har ändrats. Mer information finns i [skapa ett erbjudande](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen    
<!-- 2328416 --> Under installationen av uppdateringen 1803 bör finnas det driftstopp för blob- och interna tjänster som använder blobtjänsten. Detta omfattar vissa åtgärder för virtuell dator. Detta stillestånd kan orsaka fel-klient åtgärder eller aviseringar från tjänster som inte kan komma åt data. Det här problemet löser sig själv när uppdateringen är klar med installationen. 



### <a name="post-update-steps"></a>Steg efter uppdateringen
- När installationen av 1803, installerar du eventuella tillämpliga snabbkorrigeringar. Visa mer information i följande artiklar i kunskapsbasen, samt våra [Servicing princip](azure-stack-servicing-policy.md).

  - [KB 4344115 – Azure Stack-snabbkorrigeringen 1.0.180427.15](https://support.microsoft.com/help/4344115).

- När du har installerat den här uppdateringen, granskar du brandväggskonfigurationen av för att säkerställa [nödvändiga portar](azure-stack-integrate-endpoints.md) är öppna. Exempelvis kan den här uppdateringen innehåller *Azure Monitor* som innehåller en ändring av granskningsloggar på aktivitetsloggar. Med den här ändringen port 13012 används nu och måste också vara öppet.  


### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)
Följande är efter installation kända problem för version **20180323.2**.

#### <a name="portal"></a>Portalen
- <!-- 2332636 - IS -->  När du använder AD FS för Azure Stack-identitetssystemet och uppdatera till den här versionen av Azure Stack standardägaren av providern Standardprenumeration återställs till inbyggt **CloudAdmin** användare.  
  Lösning: Lös problemet när du har installerat uppdateringen med steg 3 i den [utlösa automatisering för att konfigurera anspråk providern förtroende i Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) proceduren för att återställa standard-providern Prenumerationens ägare.   

- Möjligheten [att öppna en ny supportbegäran i listrutan](azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - För Azure Stack integrerade system använder https://aka.ms/newsupportrequest.

- <!-- 2050709 --> I administratörsportalen går det inte att redigera lagringsmått för Blob service, Table service eller Queue service. När du går till lagring och sedan markera blob, tabell eller kö service panelen, öppnas ett nytt blad som visar ett diagram med prestandamått för tjänsten. Om du sedan väljer Redigera högst upp på panelen metrics diagrammet öppnas bladet redigera diagram men visas inte alternativen för att redigera mått.

- Det kanske inte går att visa resurser för beräkning eller lagring i administratörsportalen. Orsaken till problemet är ett fel under installationen av uppdateringen som gör att uppdateringen ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du kugghjulsikonen i det övre högra hörnet i portalen och välj sedan **Återställ standardinställningarna**.

- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

- På instrumentpanelen i admin portal, Update-panelen kan inte visa information om uppdateringar. Klicka på ikonen för att uppdatera den för att lösa problemet.

- I admin portal kan du se en kritisk varning i *Microsoft.Update.Admin* komponent. Aviseringens namn, beskrivning och åtgärder som alla Visa som:  
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


#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa objekt och planer och erbjudanden. De här objekten är icke-funktionella till användare.



#### <a name="compute"></a>Compute
- Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- När du skapar en tillgänglighetsuppsättning i portalen genom att gå till **New** > **Compute** > **tillgänglighetsuppsättning**, du kan bara skapa en tillgänglighetsuppsättning med en feldomän och uppdateringsdomän 1. Som en lösning, när du skapar en ny virtuell dator, skapa tillgänglighetsuppsättning med hjälp av PowerShell, CLI, eller från portalen.

- När du skapar virtuella datorer på Azure Stack-användarportalen visar ett felaktigt antal datadiskar som kan kopplas till D-serien VM i portalen. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försöka redownload VM-avbildning som tidigare misslyckats.

-  Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  


#### <a name="networking"></a>Nätverk
- När en virtuell dator skapas och som är associerade med en offentlig IP-adress, kan du kopplingen mellan den virtuella datorn från IP-adress. Avassociationsåtgärden ser ut att fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn.

  För närvarande, måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till den nya servern.



- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

- Azure Stack har inte stöd för att lägga till ytterligare nätverksgränssnitt i en VM-instans efter den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste de ha definierats vid tidpunkten för distribution.

- <!-- 2096388 --> Du kan inte använda administrationsportalen för att uppdatera regler för en grupp. 

    Lösning för App Service: Om du behöver fjärrskrivbord till Controller-instanser kan du ändra säkerhetsregler i nätverkssäkerhetsgrupper med PowerShell.  Följande är exempel på hur du *Tillåt*, och sedan återställa konfigurationen av *neka*:  
    
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
- Innan du fortsätter bör du granska den viktiga anmärkningen i [innan du börjar](#before-you-begin) nära början av dessa viktig information.

- Det kan ta upp till en timme innan användarna kan skapa databaser i en ny SQL eller MySQL-distribution.

- Endast resursprovidern stöds för att skapa objekt på servrar som värd SQL eller MySQL. Objekt som har skapats på en värdserver som inte skapats med resursprovidern kan resultera i ett felaktigt tillstånd.  

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** namn när du skapar en SKU för SQL- och MySQL-resursprovider.

> [!NOTE]  
> När du har uppdaterat till Azure Stack 1803 kan du fortsätta att använda SQL- och MySQL resursprovidrar som du tidigare har distribuerat.  Vi rekommenderar att du uppdaterar SQL- och MySQL när en ny version blir tillgänglig. Som Azure Stack gäller uppdateringar SQL och MySQL-resursprovidrar sekventiellt.  Till exempel om du använder version 1711 först tillämpa versionen 1712 och sedan 1802 och uppdatera sedan till 1803.      
>   
> Installationen av uppdateringen 1803 påverkar inte den nuvarande användningen av SQL eller MySQL resursprovidrar av användarna.
> Oavsett vilken version av resursprovidrar som du använder dina data för användare i sina databaser är inte vidröras och är fortfarande tillgängliga.    



#### <a name="app-service"></a>App Service
- Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

- För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.


#### <a name="usage"></a>Användning  
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Ladda ned Azure Stack-verktyg från GitHub
- När du använder den *invoke-webrequest* PowerShell-cmdlet för att ladda ned Azure Stack-verktyg från Github, du får ett felmeddelande:     
    -  *Invoke-webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Det här felet uppstår på grund av en nyligen utfasning för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standard för PowerShell). Mer information finns i [svaga kryptografiska standarder meddelande om borttagning](https://githubengineering.com/crypto-removal-notice/).

  Lös problemet genom att lägga till `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` längst upp i skript för att tvinga PowerShell-konsolen att använda TLSv1.2 när du laddar ned från GitHub-lagringsplatser.


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned Azure Stack 1803 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Se också
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
