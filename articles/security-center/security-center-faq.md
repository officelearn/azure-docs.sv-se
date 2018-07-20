---
title: Vanliga frågor (och svar FAQ) för Azure Security Center | Microsoft Docs
description: Den här vanliga frågor och svar innehåller frågor och svar om Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: 320c7c483e865c85948d32ee2b5b70a92181920f
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160077"
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

Den **standardnivån** lägger till Avancerat funktioner, inklusive hot intelligens, beteendeanalys, avvikelseidentifiering, säkerhetsincidenter och hotidentifiering attribution rapporter. Standardnivån är kostnadsfri de första 60 dagarna. Om du väljer att fortsätta att använda tjänsten efter 60 dagar, börjar vi automatiskt att debitera för tjänsten.  Om du vill uppgradera, Välj [prisnivå](https://docs.microsoft.com/azure/security-center/security-center-pricing) i säkerhetsprincipen.

## <a name="permissions"></a>Behörigheter
Azure Security Center använder [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md), som tillhandahåller [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

Se [behörigheter i Azure Security Center](security-center-permissions.md) mer information om roller och tillåtna åtgärder i Security Center.

## <a name="data-collection"></a>Datainsamling
Security Center samlar in data från dina virtuella Azure-datorer (VM) och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys.

### <a name="how-do-i-disable-data-collection"></a>Hur jag för att inaktivera insamling av data?
Automatisk etablering är inaktiverat som standard. Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. Automatisk försörjning rekommenderas starkt för att få säkerhetsaviseringar och rekommendationer om systemuppdateringar, OS-säkerhetsproblem och endpoint protection.

Inaktivera datainsamling, [logga in på Azure-portalen](https://portal.azure.com)väljer **Bläddra**väljer **Security Center**, och välj **Välj princip**. Välj den prenumeration du vill avaktivera automatisk etablering för. När du väljer en prenumeration **säkerhetsprincip – datainsamling** öppnas. Under **Automatisk etablering**väljer **av**.

### <a name="how-do-i-enable-data-collection"></a>Hur gör jag för att aktivera insamling av data?
Du kan aktivera datainsamling för din Azure-prenumeration i säkerhetsprincipen. Att aktivera insamling av data. [Logga in på Azure-portalen](https://portal.azure.com)väljer **Bläddra**väljer **Security Center**, och välj **säkerhetsprincip**. Välj den prenumeration som du vill aktivera automatisk etablering. När du väljer en prenumeration **säkerhetsprincip – datainsamling** öppnas. Under **Automatisk etablering**väljer **på**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när datainsamling har aktiverats?
När automatisk etablering är aktiverat, stöds etablerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer och alla nya som skapas. Automatisk försörjning rekommenderas starkt men manuell agentinstallation är också tillgängliga. [Lär dig hur du installerar tillägget Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agenten kan skapa processhändelse 4688 och *CommandLine* fältet i händelsen 4688. Nya processer som skapats på den virtuella datorn registreras av händelseloggen och övervakas av Security Centers identifieringstjänster. Information om de information som registrerades för varje ny process i [beskrivningsfält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar in 4688 händelser som skapats på den virtuella datorn också och lagrar dem i sökningen.

När Security Center identifierar misstänkt aktivitet på den virtuella datorn, kunden meddelas via e-post om [security kontaktinformation](security-center-provide-security-contact-details.md) har angetts. En avisering visas också i instrumentpanelen för Security Center security-aviseringar.

> [!NOTE]
> - Aktivera datainsamling för [anpassningsbara programkontroller](security-center-adaptive-application.md), konfigurerar en lokal AppLocker-princip i granskningsläge så att alla program i Security Center. Detta innebär att AppLocker att generera händelser som sedan kan samlas in och används av Security Center. Det är viktigt att Observera att den här principen inte konfigureras på alla datorer där det finns redan en konfigurerade AppLocker-principen. 

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Påverkar Övervakningsagenttjänsten prestanda Mina servrar?
Agenten använder en liten mängd systemresurser och bör ha liten inverkan på prestanda. Mer information om prestandapåverkan och agenten och tillägg finns i den [planerings- och bruksanvisning](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Var lagras mina data?
Data som samlas in från agenten lagras i en befintlig Log Analytics-arbetsyta som är associerad med din prenumeration eller en ny arbetsyta. Mer information finns i [datasäkerhet](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Använda Azure Security Center
### <a name="what-is-a-security-policy"></a>Vad är en säkerhetsprincip för?
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom den angivna prenumerationen. I Azure Security Center ställer in du principer för dina Azure-prenumerationer utifrån företagets säkerhetskrav och typ av program eller efter Känslighetsnivån på datauppgifterna i respektive prenumeration.

Säkerhetsprinciperna i Azure Security Center enhet säkerhetsrekommendationer och övervakning. Mer information om säkerhetsprinciper finns [övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Vem som kan ändra en säkerhetsprincip för?
Om du vill ändra en säkerhetsprincip måste du vara en administratör eller ägare eller deltagare i den aktuella prenumerationen.

Läs hur du konfigurerar en säkerhetsprincip i [ange säkerhetsprinciper i Azure Security Center](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Vad är en säkerhetsrekommendation?
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När eventuella säkerhetsproblem identifieras visas skapas rekommendationer. Via rekommendationerna får du genom processen att konfigurera nödvändig kontroll. Exempel är:

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

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Vad är skillnaden mellan hot identifieras och rapporteras på av Microsoft Security Response Center jämfört med Azure Security Center?
Microsoft Security Response Center (MSRC) utför väljer säkerhetsövervakning för Azure-nätverk och infrastruktur och tar emot threat intelligence och missbruk klagomål från tredje part. När MSRC blir medveten om att kunddata har använts av en obehörig part eller att kundens användning av Azure inte uppfyller villkoren för godkända använder en incident säkerhetshanteraren meddelar kunden. -Meddelande sker vanligtvis genom att skicka ett e-postmeddelande till säkerhetskontakter som angetts i Azure Security Center eller Azure-prenumerationsägare om en säkerhetskontakt inte har angetts.

Security Center är en Azure-tjänst som ständigt övervakar kundens Azure-miljön och gäller analys för att automatiskt identifiera en mängd eventuellt skadlig aktivitet. Dessa identifieringar är anslutna som säkerhetsaviseringar i Security Center-instrumentpanelen.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Vilka Azure-resurser övervakas av Azure Security Center?
Azure Security Center övervakar följande Azure-resurser:

* Virtuella datorer (VM) (inklusive [molntjänster](../cloud-services/cloud-services-choose-me.md))
* Azure Virtual Networks
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

Security Center vanligtvis söker efter nya data varje timme. Svarstid värdena ovan är ett värsta scenario där det finns inte en senaste genomsökning eller inte det gick att utföra en genomsökning.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Varför visas meddelandet ”VM-agenten är saknas”?
VM-agenten måste installeras på virtuella datorer för att aktivera insamling av Data. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Information om hur du installerar VM-agenten på andra virtuella datorer finns i bloggposten [VM-agenten och tillägg](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
