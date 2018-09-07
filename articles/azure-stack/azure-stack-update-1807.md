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
ms.date: 09/06/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: b5bcd12d3ead3fa99f80355e02a068246d7bcfa8
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052827"
---
# <a name="azure-stack-1807-update"></a>Uppdatering av Azure Stack 1807

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskriver innehållet i 1807 uppdateringspaketet. Den här uppdateringen innehåller förbättringar, korrigeringar och kända problem för den här versionen av Azure Stack och var du kan hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1807 update build-nummer är **1.1807.0.76**.  

### <a name="new-features"></a>Nya funktioner

Den här uppdateringen innehåller följande förbättringar för Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Starta säkerhetskopieringar på ett fördefinierat schema** – som en enhet, Azure Stack kan nu automatiskt utlösa infrastruktur säkerhetskopieringar med jämna mellanrum för att eliminera mänsklig inblandning. Azure Stack kommer automatiskt att rensa upp den externa resursen för säkerhetskopieringar som är äldre än definierade kvarhållningsperioden. Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Har lagts till dataöverföring tid i den totala tiden för säkerhetskopieringen.** Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **Kapacitet för säkerhetskopiering externa visar nu rätt kapaciteten för den externa resursen.** (Detta var tidigare hårdkoda till 10 GB.) Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

- <!-- 2508488 |  IS   -->  **Utöka kapaciteten** av [att lägga till ytterligare skalning enhet noder](azure-stack-add-scale-node.md).

- <!-- 2753130 |  IS, ASDK   -->  **Azure Resource Manager-mallar stöder nu elementet villkor** – du kan nu distribuera en resurs i en Azure Resource Manager-mall med hjälp av ett villkor. Du kan utforma mallen för att distribuera en resurs baserat på ett villkor som kontrollerar om ett parametervärde finns. Information om hur du använder en mall som ett villkor finns i [villkorsstyrd distribution av en resurs](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) och [Variables-avsnittet av Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) i Azure-dokumentationen. 

   Du kan också använda mallar som [distribuera resurser till mer än en prenumeration eller resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **Versionsstöd för Microsoft.Network-API-resursen har uppdaterats** att inkludera stöd för API-versionen 2017-10-01 från 2015-06-15 för Azure Stack-nätverksresurser.  Stöd för resurs-versioner mellan 2017-10-01 och 2015-06-15 ingår inte i den här versionen, men kommer att inkluderas i en framtida version.  Se [överväganden för nätverk i Azure Stack](user/azure-stack-network-differences.md) för funktionen skillnader.

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack har lagt till stöd för omvänd DNS-sökning för utåtriktade Azure Stack-infrastrukturslutpunkter** (som används för portalen, adminportal, hantering och adminmanagement). På så sätt kan Azure Stack extern slutpunkt som ska matchas mot en IP-adress.

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack har nu stöd för att lägga till ytterligare nätverksgränssnitt i en befintlig virtuell dator.**  Den här funktionen är tillgänglig med hjälp av portalen, PowerShell och CLI. Mer information finns i [Lägg till eller ta bort nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) i Azure-dokumentationen. 

- <!-- 2222444 | IS, ASDK   -->  **Förbättringar i Precision och återhämtning har gjorts till nätverk användning taxor**.  Mätare för användning av nätverk är nu mer exakta och tar i prenumerationer för kontot som har pausats, avbrott punkter och konkurrenstillstånd.

- <!-- 2753080 | IS -->  **Uppdatera tillgängliga meddelande.** Ansluten Azure Stack distributioner nu regelbundet kontrollerar en säker slutpunkt och avgöra om en uppdatering är tillgänglig för ditt moln. Det här meddelandet visas i panelen uppdatering som när du söker efter och importera en ny uppdatering manuellt. Läs mer om [hantering av uppdateringar för Azure Stack](azure-stack-updates.md).

- <!-- 2297790 | IS, ASDK -->  **Förbättringar av Azure Stack syslog-klienten (förhandsgranskningsfunktion)**. Den här klienten gör att vidarebefordran av gransknings- och relaterade till Azure Stack-infrastruktur till en syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara externt till Azure Stack. Syslog-klienten stöder nu TCP-protokollet med oformaterad text eller TLS 1.2-kryptering, den senare är standardkonfigurationen. Du kan konfigurera TLS-anslutningen med endast server eller ömsesidig autentisering.

  Om du vill konfigurera hur syslog-klienten kommunicerar (till exempel protokoll, kryptering och autentisering) med syslog-servern använder den *Set-SyslogServer* cmdlet. Denna cmdlet är tillgängligt från den privilegierade slutpunkten (program).

  Om du vill lägga till certifikatet på klientsidan för syslog klienten TLS 1.2 ömsesidig autentisering, använder du cmdleten Set-SyslogClient i detta program.

  Med den här förhandsversionen kan du se ett mycket större antal granskningar och aviseringar. 

  Eftersom den här funktionen är fortfarande i förhandsversion, inte förlita dig på den i produktionsmiljöer.

  Mer information finns i [Azure Stack syslog-vidarebefordran](azure-stack-integrate-security.md).

<!-- - | ####### | IS, ASDK |  **Azure Resource Manager includes the region name.** With this release, objects retrieved from the Azure Resource Manager will now include the region name attribute. If an existing PowerShell script directly passes the object to another cmdlet, the script may produce an error and fail. This is Azure Resource Manager compliant behavior, and requires the calling client to subtract the region attribute. For more information about the Azure Resource Manager see [Azure Resource Manager Documentation](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

- <!-- TBD | IS, ASDK -->  **Ändringar av funktionerna för delegerade providrar för.** Från och med 1807 delegerad Providers modellen har förenklats för att bättre anpassas till Azure-återförsäljare modellen och delegerade providrar kommer inte att kunna skapa andra delegerade Providers, i stort sett förenkla modellen och gör den delegerade providern funktionen är tillgänglig på en enda nivå. Om du vill aktivera övergången till den nya modellen och hanteringen av prenumerationerna flyttas prenumerationer användare nu mellan ny eller befintlig delegerad providerprenumerationer som hör till samma Directory-klient. Användarprenumerationer som hör till Providerprenumeration standard kan också flyttas till Providerprenumerationer delegerad i samma katalog-klient.  Mer information finns i [delegera erbjudanden i Azure Stack](azure-stack-delegated-provider.md).

- <!-- 2536808 IS ASDK --> **Förbättrad virtuella datorn skapas** för virtuella datorer som skapas med avbildningar som du hämtar från Azure marketplace.

- <!-- TBD | IS, ASDK -->  **Azure Stack Capacity Planner användbarhet**. Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner) som nu erbjuder ett förenklat sätt för att mata in S2D-cache och S2D kapacitet när du definierar lösning SKU: er. VM-gränsen på 1000 har tagits bort.


### <a name="fixed-issues"></a>Åtgärdade problem

- <!-- TBD | ASDK, IS --> Olika förbättringar har gjorts till uppdateringsprocessen så att de blir mer tillförlitlig. Dessutom har korrigeringar gjorts till underliggande infrastruktur som minimera eventuella driftstopp för arbetsbelastningar under uppdateringen.

- <!--2292271 | ASDK, IS --> Vi ett problem har åtgärdats där en ändrad kvotgränsen inte tillämpas för befintliga prenumerationer. Nu när du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och Plan som är associerade med en användarprenumeration, gäller den nya gränsen för befintliga prenumerationer, samt nya prenumerationer.

- <!-- 448955 | IS ASDK --> Du kan nu har fråga aktivitetsloggar för system som har distribuerats i en UTC + N tidszon.    

- <!-- 2319627 |  ASDK, IS --> Förkontroll för konfiguration av säkerhetskopiering parametrar (sökväg/användarnamn/lösenord/krypteringsnyckeln) anger inte längre felaktiga inställningar till konfigurationen för säkerhetskopiering. (Tidigare felaktiga inställningar har angetts i säkerhetskopieringen och säkerhetskopiering skulle misslyckas när den utlöses.)

- <!-- 2215948 |  ASDK, IS --> Säkerhetskopieringen i listan uppdaterar nu när du manuellt ta bort säkerhetskopian från den externa resursen.

- <!-- 2332636 | IS -->  Uppdatera till den här versionen återställs inte längre standardägaren av providern Standardprenumeration till inbyggt **CloudAdmin** användare när de distribueras med AD FS.

- <!-- 2360715 |  ASDK, IS -->  När du har konfigurerat datacenter-integrering kan du inte längre komma åt AD FS-metadatafilen från en Azure-filresurs. Mer information finns i [om du konfigurerar AD FS-integrationen genom att tillhandahålla federation metadatafil](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Vi har åtgärdat ett problem att förhindras användare från som har tilldelats en befintlig offentlig IP-adress som hade tidigare tilldelats en nätverksgränssnitt eller belastningsutjämnaren till en ny nätverksgränssnitt eller belastningsutjämnare.  

- <!-- 2551834 - IS, ASDK --> När du väljer *översikt* för ett lagringskonto på administratören eller användaren portaler, den *Essentials* rutan visar nu den förväntade informationen korrekt. 

- <!-- 2551834 - IS, ASDK --> När du väljer *taggar* för ett lagringskonto i administratören eller användaren portaler, visas information om nu korrekt.

- <!-- TBD - IS ASDK --> Den här versionen av Azure Stack åtgärdar problem som gjorde att tillämpningen av drivrutinsuppdateringar från OEM-tilläggspaket.

-   <!-- 2055809- IS ASDK --> Vi har åtgärdat ett problem som inte får du ta bort virtuella datorer från bladet beräkning när den virtuella datorn inte kunde skapas.  

- <!--  2643962 IS ASDK -->  Avisering för *låg minneskapacitet* inte längre visas inte korrekt.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures
Azure Stack använder Server Core-installationer av Windows Server 2016 till värd nyckelinfrastruktur. Den här versionen installerar följande Windows Server 2016-uppdateringar på infrastrukturservrar för Azure Stack: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Klicka på föregående länkarna för mer information om dessa säkerhetsrisker eller finns i Microsoft Knowledge Base-artiklar [4338814](https://support.microsoft.com/help/4338814) och [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Förutsättningar

- Installera Azure Stack [1805 uppdatera](azure-stack-update-1805.md) innan du installerar Azure Stack 1807 uppdateringen.  Det fanns ingen uppdatering 1806.  

- Installera den senaste tillgängliga [uppdatering eller snabbkorrigering för version 1805](azure-stack-update-1805.md#post-update-steps).  
  > [!TIP]  
  > Prenumerera på följande *RRS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittas, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd.

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- <!-- 2468613 - IS --> Under installationen av den här uppdateringen kan du se aviseringar med rubriken *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.

- <!-- 2489559 - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).

- <!-- 2830461 - IS --> I vissa fall när en uppdatering kräver uppmärksamhet, kan motsvarande avisering inte skapas. Felaktig status visas fortfarande i portalen och påverkas inte.

### <a name="post-update-steps"></a>Steg efter uppdateringen

- <!-- 2933866 – IS --> **Förbättrad status för misslyckad uppdateringsinstallationer.** Den här versionen innehåller två nya tillstånd kategorier för att ge mer information om misslyckade uppdateringsinstallationer för operatörer. De två kategorierna är *PreparationFailed*, och *InstallationFailed*. När du har installerat den här versionen kan du se information för föregående update-installationsfel som utgångspunkt för att återspegla dessa nya kategorier. 

<!-- *There are no post-update steps for update 1807.* -->

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Här följer efter installation kända problem för den här build-versionen.

### <a name="portal"></a>Portalen
- <!-- 2931230 – IS  ASDK --> Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

- <!--2760466 – IS  ASDK --> När du installerar en ny Azure Stack-miljö med den här versionen, aviseringen-värde som anger *aktivering krävs* kanske inte visas. [Aktivering](azure-stack-registration.md) krävs innan du kan använda marketplace syndikering.  

- <!-- TBD - IS ASDK --> Två administrativa prenumerationstyper som var [introducerades i version 1804](azure-stack-update-1804.md#new-features) bör inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **Standard Provider** prenumerationstyp.

- <!-- 2403291 - IS ASDK --> Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.

  ![Brödsmula](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Det kanske inte går att visa resurser för beräkning eller lagring i administratörsportalen. Orsaken till problemet är ett fel under installationen av uppdateringen som gör att uppdateringen ska rapporteras felaktigt som slutförd. Om det här problemet inträffar kan du kontakta Microsofts kundsupport om du behöver hjälp.

- <!-- TBD - IS --> Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du kugghjulsikonen i det övre högra hörnet i portalen och välj sedan **Återställ standardinställningarna**.

- <!-- TBD - IS ASDK --> Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

- <!-- TBD - IS ASDK --> Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.


### <a name="health-and-monitoring"></a>Hälsa och övervakning
- <!-- 1264761 - IS ASDK -->  Du kan se aviseringar för den **hälsotillstånd controller** komponent som har följande information:  

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


- <!-- 2812138 | IS --> Du kan se en avisering för **Storage** komponent som har följande information:

   - NAMN: Internt kommunikationsfel vid lagring  
   - ALLVARLIGHETSGRAD: kritisk  
   - KOMPONENT: Storage  
   - Beskrivning: Storage internt kommunikationsfel inträffade när begäranden skickas till följande noder.  

    Aviseringen kan ignoreras, men du måste stänga aviseringen manuellt.

- <!-- 2368581 - IS. ASDK --> Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en **fel vid skapande av Fabric VM**, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.


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

- <!-- TBD - IS ASDK --> Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 IS ASDK --> Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  

- <!-- 2724961- IS ASDK --> När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverat, översiktssidan VM inte visar mätvärden. 

   Om du vill hitta mätvärden som CPU-procent diagrammet för den virtuella datorn går du till den **mått** gästen mått bladet och visa alla Windows-VM som stöds.

### <a name="networking"></a>Nätverk  

- <!-- 1766332 - IS ASDK --> Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

- <!-- 1902460 - IS ASDK --> Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

- <!-- 16309153 - IS ASDK --> I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

- <!-- 2702741 -  IS ASDK --> Offentliga IP-adresser som distribueras med hjälp av dynamisk fördelning garanteras att bevaras när en frigörandet har utfärdats.

- <!-- 2529607 - IS ASDK --> Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

-   <!-- 2664148 - IS ASDK --> De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 


### <a name="sql-and-mysql"></a>SQL- och MySQL



- <!-- No Number - IS, ASDK -->  Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** namn när du skapar en SKU för SQL- och MySQL-resursprovider. 

- <!-- TBD - IS --> Endast resursprovidern stöds för att skapa objekt på servrar som värd SQL eller MySQL. Objekt som har skapats på en värdserver som inte skapats med resursprovidern kan resultera i ett felaktigt tillstånd.  

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


## <a name="next-steps"></a>Nästa steg
- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).  
