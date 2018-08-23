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
ms.date: 08/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: b77772ce69afbc32bfe8a6826fdf8420076074d9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42058256"
---
# <a name="azure-stack-development-kit-release-notes"></a>Viktig information om Azure Stack Development Kit  
Dessa versionsanmärkningar innehåller information om förbättringar och korrigeringar kända problem i Azure Stack Development Kit. Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](.\.\azure-stack-updates.md#determine-the-current-version).

> Håll dig uppdaterad med Nyheter i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).


## <a name="build-11807076"></a>Skapa 1.1807.0.76

### <a name="new-features"></a>Nya funktioner
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

- <!-- 1658937 | ASDK, IS --> **Starta säkerhetskopieringar på ett fördefinierat schema** – som en enhet, Azure Stack kan nu automatiskt utlösa infrastruktur säkerhetskopieringar med jämna mellanrum för att eliminera mänsklig inblandning. Azure Stack kommer automatiskt att rensa upp den externa resursen för säkerhetskopieringar som är äldre än definierade kvarhållningsperioden. Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Har lagts till dataöverföring tid i den totala tiden för säkerhetskopieringen.** Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **Kapacitet för säkerhetskopiering externa visar nu rätt kapaciteten för den externa resursen.** (Detta var tidigare hårdkoda till 10 GB.) Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
- <!-- 2753130 |  IS, ASDK   -->  **Azure Resource Manager-mallar stöder nu elementet villkor** – du kan nu distribuera en resurs i en Azure Resource Manager-mall med hjälp av ett villkor. Du kan utforma mallen för att distribuera en resurs baserat på ett villkor som kontrollerar om ett parametervärde finns. Information om hur du använder en mall som ett villkor finns i [villkorsstyrd distribution av en resurs](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) och [Variables-avsnittet av Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) i Azure-dokumentationen. 

   Du kan också använda mallar som [distribuera resurser till mer än en prenumeration eller resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **Versionsstöd för Microsoft.Network-API-resursen har uppdaterats** att inkludera stöd för API-versionen 2017-10-01 från 2015-06-15 för Azure Stack-nätverksresurser.  Stöd för resurs-versioner mellan 2017-10-01 och 2015-06-15 ingår inte i den här versionen, men kommer att inkluderas i en framtida version.  Se [överväganden för nätverk i Azure Stack](.\.\user\azure-stack-network-differences.md) för funktionen skillnader.

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack har lagt till stöd för omvänd DNS-sökning för utåtriktade Azure Stack-infrastrukturslutpunkter** (som används för portalen, adminportal, hantering och adminmanagement). På så sätt kan Azure Stack extern slutpunkt som ska matchas mot en IP-adress.

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack har nu stöd för att lägga till ytterligare nätverksgränssnitt i en befintlig virtuell dator.**  Den här funktionen är tillgänglig med hjälp av portalen, PowerShell och CLI. Mer information finns i [Lägg till eller ta bort nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) i Azure-dokumentationen. 

- <!-- 2222444 | IS, ASDK   -->  **Förbättringar i Precision och återhämtning har gjorts till nätverk användning taxor**. Mätare för användning av nätverk är nu mer exakta och tar i prenumerationer för kontot som har pausats, avbrott punkter och konkurrenstillstånd.

- <!-- 2297790 | IS, ASDK -->  **Förbättringar av Azure Stack syslog-klienten (förhandsgranskningsfunktion)**. Den här klienten gör att vidarebefordran av gransknings- och relaterade till Azure Stack-infrastruktur till en syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara externt till Azure Stack. Syslog-klienten stöder nu TCP-protokollet med oformaterad text eller TLS 1.2-kryptering, den senare är standardkonfigurationen. Du kan konfigurera TLS-anslutningen med endast server eller ömsesidig autentisering.

  Använd cmdleten Set-SyslogServer om du vill konfigurera hur syslog-klienten kommunicerar (till exempel protokoll, kryptering och autentisering) med syslog-servern. Denna cmdlet är tillgängligt från den privilegierade slutpunkten (program). 

  Om du vill lägga till certifikatet på klientsidan för syslog klienten TLS 1.2 ömsesidig autentisering, använder du cmdleten Set-SyslogClient i detta program.

  Med den här förhandsversionen kan du se ett mycket större antal granskningar och aviseringar. 

  Eftersom den här funktionen är fortfarande i förhandsversion, inte du förlita dig på den i produktionsmiljöer.

  Mer information finns i [Azure Stack syslog-vidarebefordran](.\.\azure-stack-integrate-security.md).

- <!-- ####### | IS, ASDK -->  **Azure Resource Manager innehåller namnet på regionen.** Med den här versionen innehåller objekt som hämtas från Azure Resource Manager nu namnattributet region. Om ett befintligt PowerShell.skript direkt skickar objektet till en annan cmdlet, kan skriptet producera ett fel och misslyckas. Detta är kompatibla Azure Resource Manager-beteende och kräver den anropande klienten att subtrahera attributet region. För mer information om Azure Resource Manager finns [Azure Resource Manager-dokumentation](https://docs.microsoft.com/azure/azure-resource-manager/).

- <!-- TBD | IS, ASDK -->  **Flytta prenumerationer mellan delegerade providrar.** Du kan nu flytta prenumerationer mellan ny eller befintlig delegerad Provider-prenumerationer som hör till samma Directory-klient. Prenumerationer som hör till Providerprenumeration standard kan också flyttas till Providerprenumerationer delegerad i samma katalog-klient. Mer information finns i [delegera erbjudanden i Azure Stack](.\.\azure-stack-delegated-provider.md).
 
- <!-- 2536808 IS ASDK --> **Förbättrad virtuella datorn skapas** för virtuella datorer som skapas med avbildningar som du hämtar från Azure marketplace.

### <a name="fixed-issues"></a>Åtgärdade problem

- <!-- TBD | ASDK, IS --> Olika förbättringar har gjorts till uppdateringsprocessen så att de blir mer tillförlitlig. Dessutom gjorts korrigeringar i underliggande infrastruktur, vilket förbättrar Töm nod, vilket minimerar potentiella driftstopp för arbetsbelastningar under uppdateringen.

-   <!--2292271 | ASDK, IS --> Vi ett problem har åtgärdats där en ändrad kvotgränsen inte tillämpas för befintliga prenumerationer.  Nu när du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och Plan som är associerade med en användarprenumeration, gäller den nya gränsen för befintliga prenumerationer, samt nya prenumerationer.

- <!-- 2448955 | IS ASDK --> Du kan nu har fråga aktivitetsloggar för system som har distribuerats i en UTC + N tidszon.    

- <!-- 2319627 |  ASDK, IS --> Förkontroll för konfiguration av säkerhetskopiering parametrar (sökväg/användarnamn/lösenord/krypteringsnyckeln) anger inte längre felaktiga inställningar till konfigurationen för säkerhetskopiering. (Tidigare felaktiga inställningar har angetts i säkerhetskopieringen och säkerhetskopiering skulle misslyckas när den utlöses.)

- <!-- 2215948 |  ASDK, IS --> Säkerhetskopieringen i listan uppdaterar nu när du manuellt ta bort säkerhetskopian från den externa resursen.

- <!-- 2360715 |  ASDK, IS -->  När du har konfigurerat datacenter-integrering kan du inte längre komma åt AD FS-metadatafilen från en resurs. Mer information finns i [om du konfigurerar AD FS-integrationen genom att tillhandahålla federation metadatafil](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Vi har åtgärdat ett problem att förhindras användare från som har tilldelats en befintlig offentlig IP-adress som hade tidigare tilldelats en nätverksgränssnitt eller belastningsutjämnaren till en ny nätverksgränssnitt eller belastningsutjämnare.  

- <!-- 2551834 - IS, ASDK --> När du väljer *översikt* för ett lagringskonto på administratören eller användaren portaler, den *Essentials* rutan visar nu den förväntade informationen korrekt. 

- <!-- 2551834 - IS, ASDK --> När du väljer *taggar* för ett lagringskonto i administratören eller användaren portaler, visas information om nu korrekt.

- <!-- TBD - IS ASDK --> Den här versionen av Azure Stack åtgärdar problem som gjorde att tillämpningen av drivrutinsuppdateringar från OEM-tilläggspaket.

-   <!-- 2055809- IS ASDK --> Vi har åtgärdat ett problem som inte får du ta bort virtuella datorer från bladet beräkning när den virtuella datorn inte kunde skapas.  

- <!--  2643962 IS ASDK -->  Avisering för *låg minneskapacitet* inte längre visas inte korrekt.

- <!--  TBD ASDK --> Den virtuella dator som värd för behörighet slutpunkten (program) har ökat till 4GB. I ASDK heter den här virtuella datorn AzS-ERCS01.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Kända problem

#### <a name="portal"></a>Portalen  
- <!--2760466 – IS  ASDK --> När du installerar en ny Azure Stack-miljö med den här versionen, aviseringen-värde som anger *aktivering krävs* kanske inte visas. [Aktivering](.\.\azure-stack-registration.md) krävs innan du kan använda marketplace syndikering. 

- <!-- TBD - IS ASDK --> Två administrativa prenumerationstyper som var [introducerades i version 1804](.\.\azure-stack-update-1804.md#new-features) bör inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **standard providerprenumeration** typen.

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD -  IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

- <!--  TBD | ASDK -->  Standardtidszon för din Azure Stack-distribution ställs nu till UTC. Du kan välja en tidszon när du installerar Azure Stack, men det återgår automatiskt till UTC som standard under installationen.

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

  Båda aviseringarna kan ignoreras och stängs automatiskt över tid.  

- <!-- 2368581 - IS. ASDK --> Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.

- <!-- TBD - IS. ASDK --> När du kör cmdleten Test-AzureStack på behörighet slutpunkt (program) kan felmeddelande ett Varna för ERCS VM. Du kan fortsätta att använda ASDK. 

#### <a name="compute"></a>Compute
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


- <!-- TBD -  IS ASDK --> Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> När du skapar virtuella datorer på Azure Stack-användarportalen visar ett felaktigt antal datadiskar som kan bifogas en D-serien VM i portalen. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD -  IS ASDK --> När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

- <!-- TBD -  IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 - IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.

- <!-- 2724961- IS ASDK --> När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverad, kan inte visa måttdata i diagrammet CPU-procent på översiktssidan för virtuell dator. För att hitta diagrammet CPU-procent för den virtuella datorn, gå till den **mått** gästen mått bladet och visa alla Windows-VM som stöds.

#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS, ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 1902460 -  IS ASDK --> Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- <!-- 16309153 -  IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

- <!-- 2702741 -  IS ASDK --> Offentliga IP-adresser som distribueras med hjälp av dynamisk fördelning garanteras att bevaras när en frigörandet har utfärdats.

- <!-- 2529607 - IS ASDK --> Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

-   <!-- 2664148 - IS ASDK --> De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 


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

- <!--  TBD ASDK --> Den virtuella dator som värd för behörighet slutpunkten (program) har ökat till 4GB. I ASDK heter den här virtuella datorn AzS-ERCS01.

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
- <!-- TBD - IS ASDK --> Du kan inte använda drivrutinsuppdateringar med hjälp av en OEM-tillägg-paketet med den här versionen av Azure Stack.  Det finns ingen lösning på problemet.
 
- <!-- TBD - IS ASDK --> Möjligheten [att öppna en ny supportbegäran i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - Använd för Azure Stack Development Kit https://aka.ms/azurestackforum.    

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

- <!-- 2368581 - IS. ASDK --> Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.

- <!-- TBD - IS. ASDK --> När du kör cmdleten Test-AzureStack på behörighet slutpunkt (program) kan felmeddelande ett Varna för ERCS VM. Du kan fortsätta att använda ASDK. 


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

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

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

- <!-- TBD - IS ASDK --> App Service kan bara distribueras till den *standard providerprenumeration* just nu. <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>Användning  
- <!-- TBD -  IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- #### Identity -->


## <a name="build-201805131"></a>Skapa 20180513.1

### <a name="new-features"></a>Nya funktioner
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Nya administrativa prenumerationer**. Med 1804 finns det två typer av nya prenumerationer i portalen. Dessa nya typer av prenumerationer är utöver standard providerprenumeration och synliga i nya Azure Stack-installationer från och med version 1804. *Använd inte de här nya typer av prenumerationer med den här versionen av Azure Stack*. <!-- We will announce the availability to use these subscription types in with a future update. -->

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

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

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



