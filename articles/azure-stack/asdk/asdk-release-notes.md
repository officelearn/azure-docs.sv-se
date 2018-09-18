---
title: Microsoft Azure Stack Development Kit viktig information | Microsoft Docs
description: Förbättringar, korrigeringar och kända problem med Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
git ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: 7770f4c45d974e8ce5750dbd3aee0d8393281151
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984449"
---
# <a name="azure-stack-development-kit-release-notes"></a>Viktig information om Azure Stack Development Kit  
Den här artikeln innehåller information om förbättringar, korrigeringar och kända problem i Azure Stack Development Kit. Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](.\.\azure-stack-updates.md#determine-the-current-version).

> Håll dig uppdaterad med Nyheter i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11808097"></a>Skapa 1.1808.0.97

### <a name="new-features"></a>Nya funktioner
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

- <!-- 2682594   | ASDK  -->   **Alla miljöer i Azure Stack kan nu använda formatet tidszon Coordinated Universal Time (UTC).**  Alla logga data nu relaterad information visas i UTC-format. 

- <!-- 2437250  | IS  ASDK --> **Hanterade diskar stöds.** Du kan nu använda Managed Disks i Azure Stack virtuella datorer och VM-skalningsuppsättningar. Mer information finns i [Azure Stack Managed Disks: skillnader och överväganden](/azure/azure-stack/user/azure-stack-managed-disk-considerations).
 
- <!-- 2563799  | IS  ASDK -->  **Azure Monitor**. Som Azure Monitor på Azure ger Azure Monitor på Azure Stack beroende på infrastruktur-mått och loggar för de flesta tjänster. Mer information finns i [Azure Monitor på Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

- <!-- ASDK --> **Galleriobjekt för Virtual Machine Scale Sets är nu inbyggda**.  Virtual Machine Scale Sets galleriobjekt är nu tillgänglig i användar- och analytikerportaler utan att behöva hämta dem. 

- <!-- IS, ASDK --> **Virtual Machine Scale Sets skalning**.  Du kan använda portalen för att [skala en VM-Skalningsuppsättning](/azure/azure-stack/azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Stöd för anpassade konfigurationer för IPSec/IKE-princip** för [VPN-gatewayer i Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Marketplace-objekt för Kubernetes**. Du kan nu distribuera Kubernetes-kluster med hjälp av den [Kubernetes Marketplace-objekt](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Användare kan det Kubernetes-objekt och fylla i några parametrar för att distribuera ett Kubernetes-kluster i Azure Stack. Syftet med mallarna är att göra det enkelt för användarna att installationsprogrammet utveckling/testning i Kubernetes-distribution med några få steg.

- <!-- | IS ASDK--> **Blockchain mallar**. Du kan nu köra [Ethereum consortium distributioner](/azure/azure-stack/azure-stack-ethereum) på Azure Stack. Du kan hitta tre nya mallar i den [Azure Stack Snabbstartsmallarna](https://github.com/Azure/AzureStack-QuickStart-Templates). De används att distribuera och konfigurera ett flera medlem Ethereum konsortienätverk med minimal kunskap om Azure och Ethereum. Syftet med mallarna är att göra det enkelt för användarna att installationsprogrammet utveckling/testning Blockchain distributioner med några få steg.

- <!-- | IS ASDK--> **API-version profilen 2017-03-09-profilen har uppdaterats till 2018-03-01-hybrid**. API-profiler ange Azure-resursprovidern och API-version för Azure REST-slutpunkter. Mer information om profiler finns i [hantera API-versionsprofiler i Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles).


### <a name="fixed-issues"></a>Åtgärdade problem
- <!-- IS ASDK--> Vi har åtgärdat problemet för att skapa en tillgänglighetsuppsättning i portalen som resulterade i gruppen med en feldomän och uppdateringsdomän 1.

- <!-- IS ASDK --> Inställningar för att skala VM-skalningsuppsättningar är nu tillgängliga i portalen.  

- <!-- 2494144- IS, ASDK --> Problem som gjorde att vissa F-serien-storlekar för virtuella datorer från att visas när du väljer en VM-storlek för distribution är löst. 

- <!-- IS, ASDK --> Förbättringar av prestanda när du skapar virtuella datorer med mera optimerad användning av underliggande lagring.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack


### <a name="changes"></a>Ändringar
- <!-- 1697698  | IS, ASDK --> *Snabbstartsguider* i användaren portalens instrumentpanel nu länken till artiklarna i onlinedokumentationen för Azure Stack.

- <!-- 2515955   | IS ,ASDK--> *Alla tjänster* ersätter *fler tjänster* i Azure Stack administratörs- och portaler. Du kan nu använda *alla tjänster* som ett alternativ till att navigera i Azure Stack-portaler på samma sätt som du gör i Azure-portaler.

- <!-- TBD | IS, ASDK --> *+ Skapa en resurs* ersätter *+ ny* i Azure Stack administratörs- och portaler.  Du kan nu använda *+ skapa en resurs* som ett alternativ till att navigera i Azure Stack-portaler på samma sätt som du gör i Azure-portaler. 

- <!--  TBD – IS, ASDK --> *Basic A* storlekar för virtuella datorer är inte längre tillgängligt för [skapar VM-skalningsuppsättningar](.\.\azure-stack-compute-add-scalesets.md) (VMSS) via portalen. Använd PowerShell eller en mall för att skapa en VMSS med den här storleken. 

### <a name="known-issues"></a>Kända problem

#### <a name="portal"></a>Portalen  
- <!-- 2967387 – IS, ASDK --> Det konto som används för att logga in på Azure Stack-administratör eller användare portalen visas som **Oidentifierad användare**. Detta inträffar när kontot inte har antingen en *första* eller *senaste* namnen. Undvik problemet genom att redigera användarkontot om du vill använda den första eller sista. Du måste sedan logga ut och logga sedan in igen på portalen. 

-  <!--  2873083 - IS ASDK --> När du använder portalen för att skapa en virtuell datorskalning ange (VMSS), den *instansstorlek* listrutan inte in korrekt när du använder Internet Explorer. Undvik problemet genom att använda en annan webbläsare när du använder portalen för att skapa en VMSS.  

- <!-- TBD  ASDK --> Standardtidszon för alla Azure Stack-distributioner är nu ställa in att Coordinated Universal Time (UTC). Du kan välja en tidszon när du installerar Azure Stack, men det automatiskt med UTC som standard under installationen.

- <!-- 2931230 – IS  ASDK --> Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

- <!--2760466 – IS  ASDK --> När du installerar en ny Azure Stack-miljö med den här versionen, aviseringen-värde som anger *aktivering krävs* kanske inte visas. [Aktivering](.\.\azure-stack-registration.md) krävs innan du kan använda marketplace syndikering. 

- <!-- TBD - IS ASDK --> Två administrativa prenumerationstyper som var [introducerades i version 1804](.\.\azure-stack-update-1804.md#new-features) bör inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **standard providerprenumeration** typen.

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

  Båda aviseringarna kan ignoreras och stängs automatiskt över tid.  

- <!-- 2368581 - IS. ASDK --> Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.


#### <a name="compute"></a>Compute  
- <!-- 2869209 – IS, ASDK --> När du använder den [ **Lägg till AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan cmdleten misslyckas med följande fel: *tidskrävande åtgärden misslyckades med statusen ”misslyckades”*. 

- <!--  2966665 – IS, ASDK --> Koppla datadiskar som SSD till premium storlek managed disk virtuella datorer (DS, DSv2, Fs, Fs_V2) misslyckas med felmeddelandet: *gick inte att uppdatera diskar för den virtuella datorn 'vmname' fel: begärda åtgärden inte kan utföras eftersom lagringskontot typ ' Premium_LRS' stöds inte för VM-storleken ”Standard_DS/Ds_V2/FS/Fs_v2)*

   Undvik problemet genom att använda *Standard_LRS* datadiskar i stället för *Premium_LRS diskar*. Användning av *Standard_LRS* datadiskar ändras inte IOPs eller fakturering kostnaden.  

- <!--  2795678 – IS, ASDK --> När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. 

   Du kan ignorera varningen där det står: *du har valt att använda en standardisk med en storlek som har stöd för premiumdiskar. Detta kan påverka operativsystemets prestanda och rekommenderas inte. Överväg att använda premium storage (SSD) i stället.*

- <!-- 2967447 - IS, ASDK --> Virtuella datorns skalningsuppsättning (VMSS) skapa upplevelse ger 7.2 CentOS-baserade som ett alternativ för distribution. Välj en annan OS för din distribution eller använder en ARM-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace av operatorn eftersom avbildningen inte är tillgänglig på Azure Stack.

- <!-- TBD -  IS ASDK --> Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> När du skapar virtuella datorer på Azure Stack-användarportalen visar ett felaktigt antal datadiskar som kan bifogas en D-serien VM i portalen. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- TBD -  IS ASDK --> När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

- <!-- TBD -  IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 - IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.

- <!-- 2724961- IS ASDK --> När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverad, kan inte visa måttdata i diagrammet CPU-procent på översiktssidan för virtuell dator.
 
  För att hitta diagrammet CPU-procent för den virtuella datorn, gå till den **mått** gästen mått bladet och visa alla Windows-VM som stöds.

 

#### <a name="networking"></a>Nätverk
- <!-- 1766332 - IS, ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 1902460 -  IS ASDK --> Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- <!-- 16309153 -  IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

- <!-- 2702741 -  IS ASDK --> Offentliga IP-adresser som distribueras med hjälp av dynamisk fördelning garanteras att bevaras när en frigörandet har utfärdats.

- <!-- 2529607 - IS ASDK --> Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

-   <!-- 2664148 - IS ASDK --> De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

- <!-- TBD - IS ASDK --> För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.  


#### <a name="usage"></a>Användning  
- <!-- TBD -  IS ASDK --> Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- #### Identity -->




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
- <!-- 2931230 – IS  ASDK --> Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

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

- <!-- TBD - IS. ASDK --> När du kör cmdleten Test-AzureStack på behörighet slutpunkt (program) kan felmeddelande testet ett WARN/MISSLYCKAS för ERCS VM. Du kan fortsätta att använda ASDK.

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

- <!-- 2724961- IS ASDK --> När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverat, översiktssidan VM inte visar mätvärden. 

   Om du vill hitta mätvärden som CPU-procent diagrammet för den virtuella datorn går du till den **mått** gästen mått bladet och visa alla Windows-VM som stöds.

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

  Du konfigurerar Syslog-klienten använder den **Set-SyslogServer** cmdlet på privilegierad slutpunkt.

  Med den här förhandsversionen kan du se följande tre aviseringar. När den får från Azure Stack, dessa aviseringar innehåller *beskrivningar* och *reparation* vägledning.
  - Rubrik: Kodintegritet av  
  - Rubrik: Kodintegritet i granskningsläge
  - Rubrik: Användarkontot som skapade

  Den här funktionen är i förhandsversion, det bör inte förlita sig på i produktionsmiljöer.   


### <a name="fixed-issues"></a>Åtgärdade problem
- Vi har åtgärdat problemet som blockerats [att öppna en ny supportbegäran i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från i administrationsportalen. Det här alternativet nu fungerar som avsett.

- <!--  TBD ASDK --> Den virtuella dator som värd för behörighet slutpunkten (program) har ökat till 4GB. I ASDK heter den här virtuella datorn AzS-ERCS01.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack


<!-- ### Changes  -->


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Kända problem

#### <a name="portal"></a>Portalen
- <!-- 2931230 – IS  ASDK --> Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

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
  
  Inte automatiskt att Stäng avisering #3. Om du stänger aviseringen skapar Azure Stack samma avisering inom 15 minuter.  

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

