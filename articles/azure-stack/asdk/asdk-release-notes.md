---
title: Microsoft Azure Stack Development Kit viktig information | Microsoft Docs
description: Förbättringar, korrigeringar och kända problem med Azure Stack Development Kit.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 86ac1f1b5433104faa89e1f107fa36fc1da5f70e
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989902"
---
# <a name="azure-stack-development-kit-release-notes"></a>Viktig information om Azure Stack Development Kit  
Dessa versionsanmärkningar innehåller information om förbättringar och korrigeringar kända problem i Azure Stack Development Kit. Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](.\.\azure-stack-updates.md#determine-the-current-version).

> Håll dig uppdaterad med Nyheter i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Skapa 1.1805.1.47

> [!TIP]  
> Baserat på feedback från kunder, finns det en uppdatering av schemat version som används för Microsoft Azure Stack. Från och med den här uppdateringen kan 1805, representerar det nya schemat bättre den aktuella molnversionen.  
> 
> Version-schemat är nu *Version.YearYearMonthMonth.MinorVersion.BuildNumber* där andra och tredje uppsättningarna tyda på version och utgåva. Till exempel 1805.1 representerar den *versionen till tillverkning* (RTM)-versionen av 1805.  


### <a name="new-features"></a>Nya funktioner 
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack innehåller nu en *Syslog* klienten** som en *förhandsgranskningsfunktion*. Den här klienten gör att vidarebefordring av gransknings- och loggar som är relaterade till Azure Stack-infrastruktur till en Syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara som är extern till Azure Stack. Syslog-klienten stöder för närvarande endast oautentiserade UDP-anslutningar via standardporten 514. Nyttolasten för varje Syslog-meddelande är formaterad i Common Event Format (CEF). 

  Du konfigurerar Syslog-klienten använder den **Set-SyslogServer** cmdleten som visas i den privilegierade slutpunkten. 

  Med den här förhandsversionen kan du se följande tre aviseringar. När den får från Azure Stack, dessa aviseringar innehåller *beskrivningar* och *reparation* vägledning. 
  - Rubrik: Kodintegritet av  
  - Rubrik: Kodintegritet i granskningsläge 
  - Rubrik: Användarkontot som skapade

  Den här funktionen är i förhandsversion, det bör inte förlita sig på detta i produktionsmiljöer.   


### <a name="fixed-issues"></a>Åtgärdade problem
- Vi har åtgärdat problemet som blockerats [att öppna en ny supportbegäran i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från i administrationsportalen. Det här alternativet nu fungerar som avsett. 

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- <!-- 2551834 - IS, ASDK --> När du väljer **översikt** för ett lagringskonto på administratören eller användaren portalerna kan informationen från den *Essentials* fönstret visas inte.  Fönstret information visar information om kontot som dess *resursgrupp*, *plats*, och *prenumerations-ID*.  Andra alternativ för översikt över är tillgängliga, t.ex. *Services* och *övervakning*, samt som alternativ till *öppna i Explorer* eller *ta bort lagringskonto* .  

  Du kan visa informationen inte tillgänglig i [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-cmdlet. 

- <!-- 2551834 - IS, ASDK --> När du väljer **taggar** för ett lagringskonto i administratören eller användaren portaler informationen inte kan läsas in och visas inte.  

  Du kan visa informationen inte tillgänglig i [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-cmdlet.

- <!-- TBD - IS ASDK --> Använd inte de nya administrativa prenumeration typerna av *Avläsning av prenumeration*, och *förbrukning prenumeration*. Dessa nya prenumerationstyper introducerades med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den *Standard Provider* prenumerationstyp.  

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD -  IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.


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

  Båda aviseringarna #1 och #2 kan ignoreras och de stängs automatiskt över tid. 

  Du kan också se följande avisering för *kapacitet*. Procentandelen minne som anges i beskrivningen kan variera för den här aviseringen:  

  Avisera #3:
   - NAMN: Låg minneskapacitet
   - ALLVARLIGHETSGRAD: kritisk
   - KOMPONENT: kapacitet
   - Beskrivning: Regionen har förbrukat fler än 80.00% av det tillgängliga minnet. Skapa virtuella datorer med stora mängder minne kan misslyckas.  

  I den här versionen av Azure Stack, kan den här aviseringen utlöses felaktigt. Om virtuella datorer fortsätter att distribuera har kan ignorera du den här aviseringen. 
  
  Aviseringen #3 stängs inte automatiskt. Om du stänger aviseringen skapar Azure Stack samma avisering inom 15 minuter.  

- <!-- 2368581 - IS ASDK --> Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar. 


#### <a name="compute"></a>Compute
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


- <!-- TBD -  IS ASDK --> Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> När du skapar virtuella datorer på Azure Stack-användarportalen visar ett felaktigt antal datadiskar som kan bifogas en D-serien VM i portalen. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD -  IS ASDK --> När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försöka redownload VM-avbildning som tidigare misslyckats.

- <!-- TBD -  IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 - IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar. 

#### <a name="networking"></a>Nätverk
- <!-- TBD - IS ASDK --> Du kan inte skapa användardefinierade vägar i antingen portalen administratör eller användare. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 2388980 -  IS ASDK --> När en virtuell dator skapas och som är associerade med en offentlig IP-adress, kan du kopplingen mellan den virtuella datorn från IP-adress. Avassociationsåtgärden ser ut att fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn.

  För närvarande, måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till den nya servern.


- <!-- 2292271 - IS ASDK --> Om du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och en Plan som är associerad med en klientprenumeration, tillämpas inte den nya gränsen till den prenumerationen. Den nya gränsen gäller dock för nya prenumerationer som skapas när kvoten ökas. 

  Undvik problemet genom att använda en tilläggsplanen för att öka en kvot för datornätverk när planen är redan associerad med en prenumeration. Läs mer om hur du [tillgängliggöra en tilläggsplanen](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Du kan inte ta bort en prenumeration som har resurser som DNS-zon eller routningstabellen resurser som är associerade med den. Om du vill ta bort prenumerationen har, måste du först radera DNS-zon och routningstabellen resurser från prenumerationen klient. 


- <!-- 1902460 -  IS ASDK --> Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- <!-- 16309153 -  IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.
 
- <!-- TBD -  IS ASDK --> Azure Stack har inte stöd för att lägga till ytterligare nätverksgränssnitt i en VM-instans efter den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste de ha definierats vid tidpunkten för distribution.


#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- <!-- TBD - ASDK --> Databasen som är värd för servrar måste vara reserverad för användning av resurs-providern och användaren arbetsbelastningar. Du kan inte använda en instans som används av någon annan konsument, inklusive App Services.

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** namn när du skapar en SKU för SQL- och MySQL-resursprovider. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

- <!-- TBD - IS ASDK --> För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.  

- <!-- TBD - IS ASDK --> App Service kan bara distribueras till den *standard providerprenumeration* just nu. I en kommande uppdatering distribuerar App Service till den nya *Avläsning av prenumeration* som introducerades i Azure Stack 1804. När Avläsning av programvara stöds för användning, kommer alla befintliga distributioner att migreras till den här nya prenumerationstypen.

#### <a name="usage"></a>Användning  
- <!-- TBD -  IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- #### Identity -->



## <a name="build-201805131"></a>Skapa 20180513.1

### <a name="new-features"></a>Nya funktioner 
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Nya administrativa prenumerationer**. Med 1804 finns det två typer av nya prenumerationer i portalen. Dessa nya typer av prenumerationer är utöver standard providerprenumeration och synliga i nya Azure Stack-installationer från och med version 1804. *Använd inte de här nya typer av prenumerationer med den här versionen av Azure Stack*. Vi meddelar tillgängligheten för att använda dessa typer av prenumerationer in med en kommande uppdatering. 

  Dessa nya typer av prenumerationer är synliga, men en del av en större ändring att skydda standard providerprenumeration och för att göra det enklare att distribuera delade resurser, till exempel som är värd för SQL-servrar. 

  Det finns tre prenumerationstyper som är nu tillgängligt:  
  - Standard providerprenumeration: fortsätta att använda den här prenumerationstypen. 
  - Avläsning av prenumerationen: *inte använder den här prenumerationstypen.*
  - Användning-prenumeration: *inte använder den här prenumerationstypen*

### <a name="fixed-issues"></a>Åtgärdade problem
- <!-- IS, ASDK -->  I administrationsportalen behöver du inte längre uppdatera uppdatera panel innan den visar information. 

- <!-- 2050709 - IS, ASDK -->  Du kan nu använda administrationsportalen för att redigera lagringsmått för Blob service, Table service och kötjänsten.

- <!-- IS, ASDK --> Under **nätverk**när du klickar på **anslutning** att konfigurera en VPN-anslutning **plats-till-plats (IPsec)** nu är det enda tillgängliga alternativet. 

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Ytterligare versioner som taggats med den här uppdateringen  
Följande är nu tillgängliga, men kräver inte Azure Stack-uppdatering 1804.
- **Uppdatera till Microsoft Azure Stack-System Center Operations Manager Övervakningspaket**. En ny version (1.0.3.0) av Microsoft System Center Operations Manager Monitoring Pack för Azure Stack är tillgänglig för [hämta](https://www.microsoft.com/download/details.aspx?id=55184). Med den här versionen kan du använda tjänstens huvudnamn när du lägger till en ansluten Azure Stack-distribution. Den här versionen har också en uppdateringshantering upplevelse som låter dig dra Reparationsåtgärd direkt från Operations Manager. Det finns även nya instrumentpaneler som Visa resursprovidrar, skalningsenheter och skala enhet noder.

- **Nya Azure Stack Admin PowerShell-Version 1.3.0**.  Azure Stack PowerShell 1.3.0 är nu tillgängliga för installation. Den här versionen innehåller kommandon för alla providrar för administratören att hantera Azure Stack.  Den här versionen kan delar av innehållet upphör att gälla från Azure Stack verktyg GitHub [databasen](https://github.com/Azure/AzureStack-Tools). 

   Installationsinformation, följ den [instruktioner](.\.\azure-stack-powershell-install.md) eller [hjälpa](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) innehåll för Azure Stack-modulen 1.3.0. 

- **Första versionen av Azure Rest API-referens Stack**. Den [API-referens för alla Azure Stack-Admin-providrar](https://docs.microsoft.com/rest/api/azure-stack/) har nu publicerats. 

### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- <!-- TBD - IS ASDK --> Möjligheten [att öppna en ny supportbegäran i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - Använd för Azure Stack Development Kit https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD -  IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

-   <!-- TBD -  IS ASDK --> Du kan se en kritisk varning för komponenten Microsoft.Update.Admin i administrationsportalen. Aviseringens namn, beskrivning och åtgärder som alla Visa som:  
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
- <!-- TBD -  IS ASDK --> Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> När du skapar virtuella datorer på Azure Stack-användarportalen visar portalen ett felaktigt antal datadiskar som kan ansluta till virtuella datorer i DS-serien. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD -  IS ASDK --> När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försöka redownload VM-avbildning som tidigare misslyckats.

- <!-- TBD -  IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 - IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar. 

#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS, ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 2388980 -  IS ASDK --> När en virtuell dator skapas och som är associerade med en offentlig IP-adress, kan du kopplingen mellan den virtuella datorn från IP-adress. Avassociationsåtgärden ser ut att fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn.

  För närvarande, måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till den nya servern.

- <!-- 2292271 - IS ASDK --> Om du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och en Plan som är associerad med en klientprenumeration, tillämpas inte den nya gränsen till den prenumerationen. Den nya gränsen gäller dock för nya prenumerationer som skapas när kvoten ökas. 

  Undvik problemet genom att använda en tilläggsplanen för att öka en kvot för datornätverk när planen är redan associerad med en prenumeration. Läs mer om hur du [tillgängliggöra en tilläggsplanen](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Du kan inte ta bort en prenumeration som har resurser som DNS-zon eller routningstabellen resurser som är associerade med den. Om du vill ta bort prenumerationen har, måste du först radera DNS-zon och routningstabellen resurser från prenumerationen klient. 


- <!-- 1902460 -  IS ASDK --> Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- <!-- 16309153 -  IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.
 
- <!-- TBD -  IS ASDK --> Azure Stack har inte stöd för att lägga till ytterligare nätverksgränssnitt i en VM-instans efter den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste de ha definierats vid tidpunkten för distribution.


#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- <!-- TBD - ASDK --> Databasen som är värd för servrar måste vara reserverad för användning av resurs-providern och användaren arbetsbelastningar. Du kan inte använda en instans som används av någon annan konsument, inklusive App Services.

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** namn när du skapar en SKU för SQL- och MySQL-resursprovider. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

- <!-- TBD -  IS ASDK --> För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.
 
#### <a name="usage"></a>Användning  
- <!-- TBD -  IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Ladda ned Azure Stack-verktyg från GitHub
- När du använder den *invoke-webrequest* PowerShell-cmdlet för att ladda ned Azure Stack-verktyg från Github, du får ett felmeddelande:     
    -  *Invoke-webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Det här felet uppstår på grund av en nyligen utfasning för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standard för PowerShell). Mer information finns i [svaga kryptografiska standarder meddelande om borttagning](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Skapa 20180302.1

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Nya funktioner och korrigeringar som släppts för Azure Stack-integrerade system version 1803 gäller för Azure Stack Development Kit. Se den [nya funktioner](.\.\azure-stack-update-1803.md#new-features) och [ett problem har åtgärdats](.\.\azure-stack-update-1803.md#fixed-issues) avsnitt av Azure Stack-1803 uppdatera viktig information.  
> [!IMPORTANT]    
> Några av objekten i den **nya funktioner** och **ett problem har åtgärdats** avsnitt endast är relevanta för integrerade Azure Stack-systemen.

### <a name="changes"></a>Ändringar
- Ett sätt att ändra tillståndet för en nyligen skapade erbjudande från *privata* till *offentliga* eller *inaktiverade* har ändrats. Mer information finns i [skapa ett erbjudande](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- Möjligheten [att öppna en ny supportbegäran i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - Använd för Azure Stack Development Kit https://aka.ms/azurestackforum.    

- <!-- 2050709 --> I administratörsportalen går det inte att redigera lagringsmått för Blob service, Table service eller Queue service. När du går till lagring och sedan markera blob, tabell eller kö service panelen, öppnas ett nytt blad som visar ett diagram med prestandamått för tjänsten. Om du sedan väljer Redigera högst upp på panelen metrics diagrammet öppnas bladet redigera diagram men visas inte alternativen för att redigera mått.  

- Du ser en **aktivering krävs** varning om att registrera din Azure Stack Development Kit. Det här beteendet är förväntat.

- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

- På instrumentpanelen i admin portal, Update-panelen kan inte visa information om uppdateringar. Klicka på ikonen för att uppdatera den för att lösa problemet.

-   Du kan se en kritisk varning för komponenten Microsoft.Update.Admin i administrationsportalen. Aviseringens namn, beskrivning och åtgärder som alla Visa som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

    Den här varningen kan ignoreras. 

- I både administrationsportalen och användarportalen, översiktsbladet inte kan läsas in när du väljer översiktsbladet för storage-konton som har skapats med en äldre API-version (exempel: 2015-06-15). 

  Som en tillfällig lösning använder du PowerShell för att köra den **Start ResourceSynchronization.ps1** skript för att återställa åtkomst till uppgifterna för lagringskontot. [Skriptet finns tillgängliga från GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), och måste köras med administratörsbehörighet för tjänsten på development kit värden om du använder ASDK.  

- Den **Service Health** bladet inte kan läsas in. När du öppnar bladet Tjänstehälsa i administratören eller användaren portal, Azure Stack visar ett fel och läses inte in information. Detta är förväntat. Även om du kan välja och öppna Tjänstehälsa, den här funktionen är inte tillgänglig ännu, men de kommer att implementeras i en framtida version av Azure Stack.


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

- I Azure Stack-administratörsportalen, kan du se en kritisk varning med namnet **väntande externt certifikat upphör att gälla**.  Den här varningen kan ignoreras och påverkar driften av Azure Stack Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa objekt och planer och erbjudanden. De här objekten är icke-funktionella till användare.
 
#### <a name="compute"></a>Compute
- Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- När du skapar virtuella datorer på Azure Stack-användarportalen visar portalen ett felaktigt antal datadiskar som kan ansluta till virtuella datorer i DS-serien. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försöka redownload VM-avbildning som tidigare misslyckats.

-  Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar. 


#### <a name="networking"></a>Nätverk
- Under **nätverk**, om du klickar på **anslutning** att konfigurera en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.

- När en virtuell dator skapas och som är associerade med en offentlig IP-adress, kan du kopplingen mellan den virtuella datorn från IP-adress. Avassociationsåtgärden ser ut att fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn.

  För närvarande, måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungliga virtuella datorn och inte till den nya servern.



- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.
 
- Azure Stack har inte stöd för att lägga till ytterligare nätverksgränssnitt i en VM-instans efter den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste de ha definierats vid tidpunkten för distribution.



#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- Det kan ta upp till en timme innan användarna kan skapa databaser i en ny SQL eller MySQL SKU.

- Databasen som är värd för servrar måste vara reserverad för användning av resurs-providern och användaren arbetsbelastningar. Du kan inte använda en instans som används av någon annan konsument, inklusive App Services.

- <!-- IS, ASDK --> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** eller **nivå** namn när du skapar en SKU för SQL- och MySQL-resursprovider.

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





