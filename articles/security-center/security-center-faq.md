---
title: Vanliga frågor (och svar FAQ) för Azure Security Center | Microsoft Docs
description: Den här vanliga frågor och svar innehåller frågor och svar om Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2019
ms.author: monhaber
ms.openlocfilehash: 5fd885acc4294ae9f370d3becd0ad8cfb7935193
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992370"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Security Center
Den här vanliga frågor och svar innehåller frågor och svar om Azure Security Center, en tjänst som hjälper dig att förhindra, upptäcka och svara på hot med ökad insyn i och kontroll över säkerheten hos dina Microsoft Azure-resurser.

> [!NOTE]
> Från och med tidig juni 2017 använder Security Center Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns i [Plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Med hjälp av Azure Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

### <a name="how-do-i-get-azure-security-center"></a>Hur får jag Azure Security Center?
Azure Security Center aktiveras med Microsoft Azure-prenumerationen och nås från den [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). ([Logga in på portalen](https://portal.azure.com)väljer **Bläddra**, och bläddra till **Security Center**).  

## <a name="billing"></a>Fakturering
### <a name="how-does-billing-work-for-azure-security-center"></a>Hur fungerar faktureringen för Azure Security Center?
Security Center finns två nivåer:

Den **kostnadsfria nivån** ger insyn i säkerhetsläget för din Azure-resurser, grundläggande säkerhetsprinciper, säkerhetsrekommendationer och integrering med säkerhetsprodukter och tjänster från partner.

Den **standardnivån** lägger till Avancerat funktioner, inklusive hot intelligens, beteendeanalys, avvikelseidentifiering, säkerhetsincidenter och hotidentifiering attribution rapporter. Du kan starta en Standard tierfree-utvärderingsversion. Om du vill uppgradera, Välj [prisnivå](https://docs.microsoft.com/azure/security-center/security-center-pricing) i säkerhetsprincipen. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="permissions"></a>Behörigheter
I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

Se [behörigheter i Azure Security Center](security-center-permissions.md) mer information om roller och tillåtna åtgärder i Security Center.

## <a name="data-collection-agents-and-workspaces"></a>Insamling av data, agenter och arbetsytor
Security Center samlar in data från dina virtuella Azure-datorer (VM) och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys.

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Debiteras jag för Log Analytics på arbetsytor som skapats av Security Center?
Nej. Arbetsytor som skapats av Security Center, medan konfigurerats för Log Analytics per nod fakturering, avgifter Log Analytics. Security Center fakturering baseras alltid på din säkerhetsprincip i Security Center och de lösningar som är installerad på en arbetsyta:

- **Kostnadsfri nivå** – Security Center aktiveras 'SecurityCenterFree' lösningen på standardarbetsytan. Du debiteras inte för den kostnadsfria nivån.
- **Standard-nivån** – Security Center aktiveras ”säkerhet” lösningen på standardarbetsytan.

Mer information om priser finns i [Security Center-prissättning](https://azure.microsoft.com/pricing/details/security-center/). Sidan med priser adresser ändringar av lagring av säkerhetsdata och beräknad fakturering börjar i juni 2017.

> [!NOTE]
> Log Analytics prisnivån för arbetsytor som skapats av Security Center påverkar inte Security Center fakturering.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Vad innebär en virtuell dator för automatisk etablering av installationen av Microsoft Monitoring Agent?
Kvalificera dig om Windows eller Linux IaaS-datorer:

- Tillägget Microsoft Monitoring Agent är inte installerat på den virtuella datorn.
- Den virtuella datorn är i körningstillstånd.
- Windows eller Linux VM-agenten är installerad.
- Den virtuella datorn används inte som en enhet, till exempel Brandvägg för webbaserade program eller nästa generations brandvägg.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan jag ta bort Standardarbetsytor som skapats av Security Center?
**Det rekommenderas inte att ta bort standardarbetsytan.** Security Center använder Standardarbetsytor för att lagra säkerhetsdata från dina virtuella datorer.  Om du tar bort en arbetsyta Security Center kan inte samla in dessa data och vissa säkerhetsrekommendationer och aviseringar är inte tillgängliga.

Ta bort Microsoft Monitoring Agent på de virtuella datorerna är anslutna till den borttagna arbetsytan om du vill återställa. Security Center installerar om agenten och skapar nya Standardarbetsytor.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hur kan jag använda min befintliga Log Analytics-arbetsyta?

Du kan välja en befintlig Log Analytics-arbetsyta för att lagra data som samlas in av Security Center. Använda din befintliga Log Analytics-arbetsyta:

- Arbetsytan måste vara associerad med din valda Azure-prenumeration.
- Som ett minimum måste du ha läsbehörighet till arbetsytan.

Att välja en befintlig Log Analytics-arbetsyta:

1. Under **säkerhetsprincip – datainsamling**väljer **använder en annan arbetsyta**.

   ![Använd en annan arbetsyta][5]

2. Välj en arbetsyta för att lagra insamlade data från den nedrullningsbara menyn.

   > [!NOTE]
   > I nedrullningsbara menyn visas endast arbetsytor som du har åtkomst till och finns i din Azure-prenumeration.
   >
   >

3. Välj **Spara**.
4. När du har valt **spara**, tillfrågas du om du vill konfigurera om övervakade virtuella datorer.

   - Välj **nr** om du vill att de nya arbetsyteinställningarna ska **tillämpa på nya virtuella datorer bara**. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer; Nyligen identifierade virtuella datorer som inte har Microsoft Monitoring Agent installerad.
   - Välj **Ja** om du vill att de nya arbetsyteinställningarna ska **tillämpas på alla virtuella datorer**. Dessutom kan återansluta varje virtuell dator som är anslutna till en Security Center som skapat arbetsyta till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja måste du inte ta bort arbetsytor som skapats av Security Center tills alla virtuella datorer har återanslutit till den nya målarbetsytan. Den här åtgärden misslyckas om en arbetsyta har tagits bort för tidigt.
   >
   >

   - Välj **Avbryt** att avbryta åtgärden.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Vad händer om Microsoft Monitoring Agent installerades som ett tillägg på den virtuella datorn?
Security Center åsidosätts inte befintliga anslutningar till arbetsytor som användaren. Security Center lagrar säkerhetsdata från den virtuella datorn i arbetsytan som redan är ansluten. Security Center uppdaterar tilläggsversion om du vill inkludera Azure resurs-ID för den virtuella datorn för användning av Security Center.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Vad händer om jag hade en Microsoft Monitoring Agent installerad på datorn men inte som ett tillägg?
Om Microsoft Monitoring Agent är installerad direkt på den virtuella datorn (inte som en utökning av Azure), Security Center installera inte Microsoft Monitoring Agent och virtuella datorer är begränsad.

Mer information finns i nästa avsnitt [vad händer om en SCOM eller OMS dirigera agenten är redan installerad på den virtuella datorn?](#scomomsinstalled)

### Vad händer om en SCOM eller OMS direktagent är redan installerad på den virtuella datorn?<a name="scomomsinstalled"></a>
Security Center kan inte identifiera i förväg att en agent är installerad.  Security Center försöker att installera Microsoft Monitoring Agent-tillägget och misslyckas på grund av den befintliga installerade agenten.  Det här felet förhindrar att åsidosätta agentens anslutningsinställningar till sin arbetsyta och inte nödvändigt att skapa flera värdar.

> [!NOTE]
> Agenten har uppdaterats till den senaste versionen av OMS-agenten.  Detta gäller för SCOM-användare också.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Vad är effekt vid borttagning av dessa tillägg?
Om du tar bort tillägget Microsoft Monitoring Security Center kan inte samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och aviseringar är inte tillgängliga. Security Center anger att den virtuella datorn saknar tillägget och installerar om tillägget inom 24 timmar.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hur förhindrar jag att agenten för automatisk installation och arbetsytan skapa?
Du kan inaktivera automatisk etablering för dina prenumerationer i säkerhetsprincipen men detta rekommenderas inte. Stänga av Automatisk etablering gränser Security Center-rekommendationer och aviseringar. Inaktivera automatisk etablering:

1. Om din prenumeration har konfigurerats för Standard-nivån, öppna säkerhetsprincipen för prenumerationen och välj den **kostnadsfri** nivå.

   ![Prisnivå][1]

2. Därefter inaktivera automatisk etablering genom att välja **av** på den **säkerhetsprincip – datainsamling** bladet.
   ![Datainsamling][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Ska jag välja bort automatiska agentinstallation och arbetsytan skapas?

> [!NOTE]
> Se till att granska avsnitt [vad är effekterna av att avanmäla dig?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) och [rekommenderade steg när avanmäla](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) om du vill välja bort automatisk etablering.
>
>

Du kanske vill välja bort automatisk etablering om följande gäller för dig:

- Agenten för automatisk installation av Security Center gäller för hela prenumerationen.  Du kan inte använda automatisk installation till en delmängd av virtuella datorer. Om det finns kritiska virtuella datorer som inte kan installeras med Microsoft Monitoring Agent, bör du välja bort automatisk etablering.
- Installationen av tillägget Microsoft Monitoring Agent uppdateras agentens version. Detta gäller för en direkt-agent och en SCOM-agent. Om den installerade SCOM-agenten är version 2012 och uppgraderas, att hanterbarhet funktioner gå förlorade när SCOM-servern är också version 2012. Bör du väljer bort automatisk etablering om den installerade SCOM-agenten är version 2012.
- Om du har en anpassad arbetsyta som är externa för prenumerationen (en centraliserade arbetsytor) bör du välja bort automatisk etablering. Du kan manuellt installera Microsoft Monitoring Agent-tillägget och ansluta den arbetsytan utan att Security Center åsidosätta anslutningen.
- Om du vill undvika att skapa flera arbetsytor per prenumeration och du har en egen anpassad arbetsyta inom prenumerationen, har du två alternativ:

   1. Du kan välja bort automatisk etablering. Efter migreringen, ange standarden arbetsyteinställningar enligt beskrivningen i [hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Eller så kan du tillåta migreringen slutföras, Microsoft Monitoring Agent installeras på de virtuella datorerna, och de virtuella datorerna är anslutna till arbetsytan skapades. Välj din egen anpassade arbetsyta genom att ange standardinställningen för arbetsytan med valde omkonfigurera redan installerade agenter. Mer information finns i [hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Vilka är effekterna av väljer bort automatisk etablering?
När migreringen är klar kan Security Center kan inte samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och aviseringar är inte tillgänglig. Om du avböjer bör du installera Microsoft Monitoring Agent manuellt. Se [rekommenderade steg när avanmäla](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Vilka är de rekommenderade stegen när du väljer bort automatisk etablering?

Du bör installera tillägget Microsoft Monitoring Agent manuellt så att Security Center kan samla in säkerhetsdata från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar. Se [agentinstallation för Windows VM](../virtual-machines/extensions/oms-windows.md) eller [agentinstallation för Linux VM](../virtual-machines/extensions/oms-linux.md) anvisningar för installation.

Du kan ansluta agenten till en befintlig anpassad arbetsyta eller Security Center skapat arbetsyta. Om en anpassad arbetsyta inte har de ”säkerhet” eller ”SecurityCenterFree” lösningarna som är aktiverad, du måste använda en lösning. Om du vill använda, väljer du anpassad arbetsyta eller en prenumeration och tillämpa en prisnivå via den **säkerhetsprincip – prisnivå** bladet.

   ![Prisnivå][1]

Security Center aktiverar till rätt lösning i arbetsytan baserat på den valda prisnivån.

### Hur tar jag bort OMS-tillägg installeras av Security Center?<a name="remove-oms"></a>
Du kan manuellt ta bort Microsoft Monitoring Agent. Detta rekommenderas inte eftersom det begränsar Security Center-rekommendationer och aviseringar.

> [!NOTE]
> Om datainsamling har aktiverats Security Center kommer att installera om agenten efter att du tar bort den.  Du måste inaktivera datainsamling innan du tar bort agenten manuellt. Se hur förhindrar jag att agenten för automatisk installation och arbetsytan skapa? Mer information om hur du inaktiverar insamling av data.
>
>

Ta manuellt bort agenten:

1.  Öppna i portalen, **Log Analytics**.
2.  Välj en arbetsyta på Log Analytics-bladet:
3.  Välj varje virtuell dator som du inte vill övervaka och välj **Disconnect**.

   ![Ta bort agenten][3]

> [!NOTE]
> Om en Linux VM redan har en icke-extension OMS-agenten, tar bort tillägget tar bort agenten samt och kunden har installera det på nytt.
>
>
### <a name="how-do-i-disable-data-collection"></a>Hur jag för att inaktivera insamling av data?
Automatisk etablering är inaktiverat som standard. Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. Automatisk försörjning rekommenderas starkt för att få säkerhetsaviseringar och rekommendationer om systemuppdateringar, OS-säkerhetsproblem och endpoint protection.

Inaktivera datainsamling, [logga in på Azure-portalen](https://portal.azure.com)väljer **Bläddra**väljer **Security Center**, och välj **Välj princip**. Välj den prenumeration du vill avaktivera automatisk etablering för. När du väljer en prenumeration **säkerhetsprincip – datainsamling** öppnas. Under **Automatisk etablering**väljer **av**.

### <a name="how-do-i-enable-data-collection"></a>Hur gör jag för att aktivera insamling av data?
Du kan aktivera datainsamling för din Azure-prenumeration i säkerhetsprincipen. Att aktivera insamling av data. [Logga in på Azure-portalen](https://portal.azure.com)väljer **Bläddra**väljer **Security Center**, och välj **säkerhetsprincip**. Välj den prenumeration som du vill aktivera automatisk etablering. När du väljer en prenumeration **säkerhetsprincip – datainsamling** öppnas. Under **Automatisk etablering**väljer **på**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när datainsamling har aktiverats?
När automatisk etablering är aktiverat, stöds etablerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer och alla nya som skapas. Automatisk försörjning rekommenderas starkt men manuell agentinstallation är också tillgängliga. [Lär dig hur du installerar tillägget Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agenten kan skapa processhändelse 4688 och *CommandLine* fältet i händelsen 4688. Nya processer som skapats på den virtuella datorn registreras av händelseloggen och övervakas av Security Centers identifieringstjänster. Information om de information som registrerades för varje ny process i [beskrivningsfält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar in 4688 händelser som skapats på den virtuella datorn också och lagrar dem i sökningen.

Agenten gör det också möjligt för insamling av data för [anpassningsbara programkontroller](security-center-adaptive-application.md), konfigurerar en lokal AppLocker-princip i granskningsläge så att alla program i Security Center. Detta innebär att AppLocker att generera händelser som sedan kan samlas in och används av Security Center. Det är viktigt att Observera att den här principen inte konfigureras på alla datorer där det finns redan en konfigurerade AppLocker-principen. 

När Security Center identifierar misstänkt aktivitet på den virtuella datorn, kunden meddelas via e-post om [security kontaktinformation](security-center-provide-security-contact-details.md) har angetts. En avisering visas också i instrumentpanelen för Security Center security-aviseringar.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Påverkar Övervakningsagenttjänsten prestanda Mina servrar?
Agenten använder en liten mängd systemresurser och bör ha liten inverkan på prestanda. Mer information om prestandapåverkan och agenten och tillägg finns i den [planerings- och bruksanvisning](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Var lagras mina data?
Data som samlas in från agenten lagras i en befintlig Log Analytics-arbetsyta som är associerad med din prenumeration eller en ny arbetsyta. Mer information finns i [datasäkerhet](security-center-data-security.md).

## Befintliga Log Analytics-kunder<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center åsidosätter alla befintliga anslutningar mellan virtuella datorer och arbetsytor?
Om en virtuell dator redan har Microsoft Monitoring Agent installerad som en utökning av Azure, åsidosätts inte den befintliga arbetsyta-anslutningen i Security Center. Security Center använder i stället den befintliga arbetsytan.

En lösning för Security Center är installerat på arbetsytan om det inte finns redan och lösningen tillämpas endast på de relevanta virtuella datorerna. När du lägger till en lösning distribueras den automatiskt som standard att alla Windows- och Linux-agenter är anslutna till Log Analytics-arbetsytan. [Mål för lösning](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du använda ett omfång för dina lösningar.

Om Microsoft Monitoring Agent är installerad direkt på den virtuella datorn (inte som en utökning av Azure), Security Center installera inte Microsoft Monitoring Agent och virtuella datorer är begränsad.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center kan installeras lösningar i min befintliga Log Analytics-arbetsytor? Vad är fakturering effekterna?
När Security Center identifierar att en virtuell dator är redan ansluten till en arbetsyta som du skapade, kan Security Center-lösningar i den här arbetsytan enligt din prisnivå. Lösningarna används endast för den relevanta virtuella Azure-datorer [lösningsmål](../operations-management-suite/operations-management-suite-solution-targeting.md), så att faktureringen förblir densamma.

- **Kostnadsfri nivå** – Security Center installerar 'SecurityCenterFree'-lösning på arbetsytan. Du debiteras inte för den kostnadsfria nivån.
- **Standard-nivån** – Security Center installerar säkerhetslösningen för arbetsytan.

   ![Lösningar på standardarbetsytan][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Jag har redan arbetsytor i Min miljö, kan jag använda dem för att samla in säkerhetsdata?
Om en virtuell dator har redan Microsoft Monitoring Agent installerad som en utökning av Azure kan använder Security Center den befintliga anslutna arbetsytan. En lösning för Security Center är installerat på arbetsytan om det inte finns redan och lösningen tillämpas endast på de relevanta virtuella datorerna via [lösningsmål](../operations-management-suite/operations-management-suite-solution-targeting.md).

När Security Center installeras Microsoft Monitoring Agent på virtuella datorer använder standard-arbetsytor som skapats av Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Jag har redan säkerhetslösning på Mina arbetsytor. Vad är fakturering effekterna?
Säkerhet och granskning lösningen används för att aktivera Security Center Standard-nivån funktioner för virtuella Azure-datorer. Om lösningen för säkerhet och granskning är redan installerad på en arbetsyta kan använder Security Center den befintliga lösningen. Det finns ingen ändring i fakturering.

## <a name="using-azure-security-center"></a>Använda Azure Security Center
### <a name="what-is-a-security-policy"></a>Vad är en säkerhetsprincip för?
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom den angivna prenumerationen. I Azure Security Center ställer in du principer för dina Azure-prenumerationer utifrån företagets säkerhetskrav och typ av program eller efter Känslighetsnivån på datauppgifterna i respektive prenumeration.

Säkerhetsprinciperna i Azure Security Center enhet säkerhetsrekommendationer och övervakning. Mer information om säkerhetsprinciper finns [övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Vem som kan ändra en säkerhetsprincip för?
Om du vill ändra en säkerhetsprincip måste du vara en administratör eller ägare eller deltagare i den aktuella prenumerationen.

Läs hur du konfigurerar en säkerhetsprincip i [ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Vad är en säkerhetsrekommendation?
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När eventuella säkerhetsproblem identifieras visas skapas rekommendationer. Via rekommendationerna får du genom processen att konfigurera nödvändig kontroll. Några exempel:

* Etablering av program mot skadlig kod för att identifiera och ta bort skadlig programvara
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) och regler för trafiken till virtuella datorer
* Etablering av en brandvägg för webbaserade program för att skydda mot hot mot dina webbprogram
* genomföra alla systemuppdateringar som fattas
* se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Här visas bara de rekommendationer som är aktiverade i säkerhetsprinciper.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hur kan jag se det aktuella säkerhetstillståndet för Mina Azure-resurser?
Den **översikten över Security Center** bladet visar övergripande säkerhetstillståndet för miljön uppdelat efter beräkning, nätverk, lagring och data och program. Varje resurstyp har en indikator som visar om eventuella säkerhetsrisker som har identifierats. Om du klickar på varje panel visas en lista över säkerhetsproblem som identifieras av Security Center tillsammans med en förteckning över resurser i din prenumeration.

### <a name="what-triggers-a-security-alert"></a>Vad utlöser en säkerhetsvarning?
Azure Security Center automatiskt samlar in, analyserar och Smältsäkringar loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

* angripna virtuella datorer som kommunicerar med IP-adresser som man vet är skadliga
* Avancerad skadlig kod har identifierats med hjälp av Windows Felrapportering
* nyckelsökningsangrepp mot virtuella datorer
* Säkerhetsaviseringar från integrerade partnerlösningar säkerhetslösningar som skadlig eller brandväggar för webbprogram

### Varför säker poäng ändras? <a name="secure-score-faq"></a>
Från och med februari 2019 justeras poängen för några rekommendationer för att bättre passa för deras allvarlighetsgrad i Security Center. Till följd av den här justering, det kan finnas ändringar i övergripande skyddar score värden.  Läs mer om säker poäng [skydda poängberäkningen](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Vad är skillnaden mellan hot identifieras och rapporteras på av Microsoft Security Response Center jämfört med Azure Security Center?
Microsoft Security Response Center (MSRC) utför väljer säkerhetsövervakning för Azure-nätverk och infrastruktur och tar emot threat intelligence och missbruk klagomål från tredje part. När MSRC blir medveten om att kunddata har använts av en obehörig part eller att kundens användning av Azure inte uppfyller villkoren för godkända använder en incident säkerhetshanteraren meddelar kunden. -Meddelande sker vanligtvis genom att skicka ett e-postmeddelande till säkerhetskontakter som angetts i Azure Security Center eller Azure-prenumerationsägare om en säkerhetskontakt inte har angetts.

Security Center är en Azure-tjänst som ständigt övervakar kundens Azure-miljön och gäller analys för att automatiskt identifiera en mängd eventuellt skadlig aktivitet. Dessa identifieringar är anslutna som säkerhetsaviseringar i Security Center-instrumentpanelen.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Vilka Azure-resurser övervakas av Azure Security Center?
Azure Security Center övervakar följande Azure-resurser:

* Virtuella datorer (VM) (inklusive [molntjänster](../cloud-services/cloud-services-choose-me.md))
*  Azure Virtual Networks
* Azure SQL-tjänst
* Azure-lagringskonto
* Azure Web Apps (i [Apptjänstmiljö](../app-service/environment/intro.md))
* Partnerlösningar integreras med din Azure-prenumeration, till exempel en brandvägg för webbaserade program på virtuella datorer och på App Service Environment

## <a name="virtual-machines"></a>Virtuella datorer
### <a name="what-types-of-virtual-machines-are-supported"></a>Vilka typer av virtuella datorer stöds?
Övervakning och rekommendationer är tillgängliga för virtuella datorer (VM) som skapats genom att använda både den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md).

Se [plattformar som stöds i Azure Security Center](security-center-os-coverage.md) en lista över plattformar som stöds.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Varför inte Azure Security Center kan identifiera det program mot skadlig kod som körs på min Azure-dator?
Azure Security Center har insyn i program mot skadlig kod installeras via Azure-tillägg. Security Center är till exempel inte kunna identifiera skadlig kod som har förinstallerats på en avbildning eller om du har installerat program mot skadlig kod på dina virtuella datorer med dina egna processer (till exempel konfigurationshanteringssystem).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Varför visas meddelandet ”Genomsök Data som saknas” för den virtuella datorn?
Det här meddelandet visas när det finns inga genomsökningsdata för en virtuell dator. Det kan ta lite tid (mindre än en timme) för genomsökningsdata fylls i när datainsamling har aktiverats i Azure Security Center. Efter den första populationen av genomsökningsdata får du det här meddelandet eftersom det finns inga genomsökningsdata alls eller det finns inga genomsökningsdata för senaste. Genomsökningar inte fylla i för en virtuell dator i ett stoppat tillstånd. Det här meddelandet kan också visas om genomsökningsdata inte har fyllts i nyligen (i enlighet med policyn för datalagring i för Windows-agenten, som har ett standardvärde på 30 dagar).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hur ofta skannar Security Center för säkerhetsproblem med operativsystem, systemuppdateringar och problem med endpoint protection?
Fördröjning i Security Center söker efter sårbarheter, uppdateringar, och problem är:

- Operativsystemet säkerhetskonfigurationer – data uppdateras inom 48 timmar
- Systemuppdateringar – data uppdateras inom 24 timmar
- Endpoint Protection utfärdar – data uppdateras inom 8 timmar

Security Center kan du vanligtvis söker efter nya data varje timme, och uppdaterar rekommendationer i enlighet med detta. 

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Varför visas meddelandet ”VM-agenten är saknas”?
VM-agenten måste installeras på virtuella datorer för att aktivera insamling av Data. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Information om hur du installerar VM-agenten på andra virtuella datorer finns i bloggposten [VM-agenten och tillägg](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
