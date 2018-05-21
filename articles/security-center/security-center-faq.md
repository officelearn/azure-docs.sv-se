---
title: Vanliga frågor (FAQ) för Azure Security Center | Microsoft Docs
description: Det här avsnittet får du svar frågor om Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: terrylan
ms.openlocfilehash: d5a9f2ba68574ba8cb99b01ce426ec77a5eecd3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Security Center
Det här avsnittet får du svar frågor om Azure Security Center, en tjänst som hjälper dig att förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för din Microsoft Azure-resurser.

> [!NOTE]
> Från och med tidig juni 2017 använder Security Center Microsoft Monitoring Agent för att samla in och lagra data. Läs mer i [Azure Security Center-plattformen migrering](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Med hjälp av Azure Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

### <a name="how-do-i-get-azure-security-center"></a>Hur skaffar jag Azure Security Center?
Azure Security Center har aktiverats med Microsoft Azure-prenumerationen och nås från den [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). ([Logga in på portalen](https://portal.azure.com)väljer **Bläddra**, och bläddra till **Security Center**).  

## <a name="billing"></a>Fakturering
### <a name="how-does-billing-work-for-azure-security-center"></a>Hur fungerar fakturering arbete för Azure Security Center?
Security Center erbjuds i två nivåer:

Den **kostnadsfria nivån** ger insyn i säkerhetsläget för Azure-resurser, grundläggande säkerhetsprinciper, säkerhetsrekommendationer och integrering med säkerhetsprodukter och tjänster från partner.

Den **standardnivån** lägger till avancerade threat detection funktioner, inklusive hot intelligence, beteendeanalys, avvikelseidentifiering, säkerhetsincidenter och hot tillskrivningar rapporter. Standardnivån är kostnadsfri de första 60 dagarna. Om du väljer att fortsätta att använda tjänsten efter 60 dagar, starta vi automatiskt debitera för tjänsten.  Om du vill uppgradera, Välj [prisnivån](https://docs.microsoft.com/azure/security-center/security-center-pricing) i säkerhetsprincipen.

## <a name="permissions"></a>Behörigheter
Azure Security Center använder [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md), vilket ger [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs tillhör.

Se [behörigheter i Azure Security Center](security-center-permissions.md) för mer information om roller och tillåtna åtgärder i Security Center.

## <a name="data-collection"></a>Datainsamling
Security Center samlar in data från dina virtuella Azure-datorer (VM) och Azure-datorer att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys.

### <a name="how-do-i-disable-data-collection"></a>Hur inaktiverar insamling av data?
Automatisk etablering är inaktiverat som standard. Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. Automatisk etablering rekommenderas för att få säkerhetsaviseringar och rekommendationer om systemuppdateringar, OS säkerhetsrisker och endpoint protection.

Inaktivera datainsamling, [logga in på Azure portal](https://portal.azure.com)väljer **Bläddra**väljer **Security Center**, och välj **väljer principen för**. Välj den prenumeration du vill avaktivera automatisk etablering för. När du väljer en prenumeration **säkerhetsprincip - datainsamling** öppnas. Under **Automatisk etablering**väljer **av**.

### <a name="how-do-i-enable-data-collection"></a>Hur aktiverar insamling av data?
Du kan aktivera datainsamling för din Azure-prenumeration i säkerhetsprincipen. Att aktivera insamling av data. [Logga in på Azure portal](https://portal.azure.com)väljer **Bläddra**väljer **Security Center**, och välj **säkerhetsprincip**. Välj den prenumeration som du vill aktivera automatisk etablering. När du väljer en prenumeration **säkerhetsprincip - datainsamling** öppnas. Under **Automatisk etablering**väljer **på**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när datainsamling har aktiverats?
När automatisk etablering är aktiverat, stöds Security Center tillhandahåller Microsoft Monitoring Agent på alla virtuella datorer i Azure och nya filer som skapas. Automatisk etablering rekommenderas men manuell agentinstallation är också tillgänglig. [Lär dig hur du installerar tillägget för Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

Agenten kan processen att skapa händelsen 4688 och *CommandLine* fältet inuti 4688-händelse. Nya processer som skapats på den virtuella datorn registreras av händelseloggen och övervakas av Security Center identifieringstjänster. Mer information om de information som registrerats för varje ny process finns [beskrivningsfält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar in 4688 händelser som skapats på den virtuella datorn också och lagrar dem i sökningen.

När Security Center upptäcker misstänkt aktivitet på den virtuella datorn, meddelas kunden via e-post om [säkerhet kontaktinformation](security-center-provide-security-contact-details.md) har angetts. En avisering visas i instrumentpanelen för Security Center säkerhet aviseringar.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent påverka prestandan för Mina servrar?
Agenten förbrukar en nominell mängd systemresurser och bör har liten inverkan på prestanda. Mer information om inverkan på prestanda och agent och tillägg finns i [planerings-och operations](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Var lagras mina data?
Data som samlas in från den här agenten lagras i en befintlig logganalys-arbetsyta som är associerade med din prenumeration eller en ny arbetsyta. Mer information finns i [datasäkerhet](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Med hjälp av Azure Security Center
### <a name="what-is-a-security-policy"></a>Vad är en säkerhetsprincip?
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom den angivna prenumerationen. I Azure Security Center Definiera principer för dina Azure-prenumerationer enligt ditt företags säkerhetskrav och typ av program eller datakänslighetsnivå i varje prenumeration.

Säkerhetsprinciperna i Azure Security Center enhet säkerhetsrekommendationer och övervakning. Mer information om säkerhetsprinciper finns [övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Vem som kan ändra en säkerhetsprincip?
Om du vill ändra en säkerhetsprincip måste du vara en administratör eller ägare eller deltagare i den aktuella prenumerationen.

Information om hur du konfigurerar en säkerhetsprincip finns [ställa in säkerhetsprinciper i Azure Security Center](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Vad är en säkerhetsrekommendation?
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När eventuella säkerhetsproblem identifieras skapas rekommendationer. Rekommendationerna som leder dig genom processen att konfigurera nödvändig kontroll. Exempel är:

* Etablering av skadlig kod för att identifiera och ta bort skadlig programvara
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) och regler för trafiken till virtuella datorer
* Etablering av en brandvägg för webbaserade program skydda mot attacker målobjekt för webbaserade program
* genomföra alla systemuppdateringar som fattas
* se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Rekommendationer som är aktiverade i säkerhetsprinciper som visas här.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hur kan jag se det aktuella säkerhetstillståndet för min Azure-resurser?
Den **Center Säkerhetsöversikt** bladet visar den övergripande säkerhetsläget i miljön uppdelat efter beräkning, nätverk, lagring och data och program. Varje resurs har en indikator som visar om alla potentiella säkerhetsproblem har identifierats. På varje sida visar en lista över säkerhetsfrågor som identifieras av Security Center, tillsammans med en förteckning över resurser i din prenumeration.

### <a name="what-triggers-a-security-alert"></a>Vad utlöser en säkerhetsvarning?
Azure Security Center automatiskt samlar in, analyseras och Smältsäkringar loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

* angripna virtuella datorer som kommunicerar med IP-adresser som man vet är skadliga
* Avancerad skadlig kod har identifierats med hjälp av Windows Felrapportering
* nyckelsökningsangrepp mot virtuella datorer
* Säkerhetsaviseringar från integrerade säkerhet partnerlösningar, till exempel mot skadlig kod eller Web Application brandväggar

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Vad är skillnaden mellan hot upptäckts och ett meddelande om av Microsoft Security Response Center jämfört med Azure Security Center?
Microsoft Security Response Center (MSRC) utför väljer säkerhetsövervakning av Azure-nätverk och infrastruktur och tar emot hot intelligence och missbruk klagomål från tredje part. När MSRC blir medveten om att kundinformation som har använts av en olagligt eller obehöriga part eller att kundens användning av Azure inte uppfyller villkoren för godkända använder en incident säkerhetshanteraren meddelar kunden. Meddelande normalt genom att skicka ett e-postmeddelande till säkerhet kontakter som anges i Azure Security Center eller ägaren till Azure-prenumeration om en säkerhet kontakt inte har angetts.

Security Center är en Azure-tjänst som kontinuerligt övervakar kundens Azure-miljön och tillämpar analytics för att automatiskt identifiera en mängd eventuellt skadlig aktivitet. Dessa identifieringar är anslutna som säkerhetsaviseringar i instrumentpanelen i Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Vilka Azure-resurser övervakas av Azure Security Center?
Azure Security Center övervakar följande Azure-resurser:

* Virtuella datorer (VM) (inklusive [molntjänster](../cloud-services/cloud-services-choose-me.md))
* Azure Virtual Networks
* Azure SQL-tjänsten
* Azure-lagringskonto
* Azure-Webbappar (i [Apptjänstmiljö](../app-service/environment/intro.md))
* Partnerlösningar som är integrerad med din Azure-prenumeration, till exempel en brandvägg för webbaserade program på virtuella datorer samt på Apptjänst-miljö

## <a name="virtual-machines"></a>Virtuella datorer
### <a name="what-types-of-virtual-machines-are-supported"></a>Vilka typer av virtuella datorer stöds?
Övervakning och rekommendationer är tillgängliga för virtuella datorer (VM) som skapats med hjälp av både den [klassisk och Resource Manager distributionsmodellerna](../azure-classic-rm.md).

Se [plattformar som stöds i Azure Security Center](security-center-os-coverage.md) en lista över plattformar som stöds.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Varför inte Azure Security Center kan identifiera det program mot skadlig kod som körs på den virtuella Azure-datorn?
Azure Security Center har insyn i program mot skadlig kod installeras via Azure-tillägg. Security Center är till exempel inte kunna upptäcka skadlig kod som har förinstallerats på en bild som du har angett eller om du har installerat program mot skadlig kod på dina virtuella datorer med egna processer (till exempel konfigurationshanteringssystem).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Varför visas meddelandet ”Genomsök Data som saknas” för den virtuella datorn?
Det här meddelandet visas när det finns inga genomsökningsdata för en virtuell dator. Det kan ta lite tid (mindre än en timme) för genomsökningsdata fylls i när datainsamling har aktiverats i Azure Security Center. Efter första befolkningen i genomsökningsdata får detta felmeddelande eftersom det finns inga sökningsdata på alla eller inga senaste genomsökning data. Inte att fylla sökningar för en virtuell dator i ett stoppat tillstånd. Det här meddelandet kan också visas om genomsökningsdata inte har fyllts i nyligen (i enlighet med bevarandeprincipen för Windows-agent som har ett standardvärde av 30 dagar).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hur ofta Security Center igenom för säkerhetsrisker i operativsystemet, uppdateringar och endpoint protection-problem?
Fördröjning i Security Center söker efter säkerhetsrisker, uppdateringar, och problem är:

- Operativsystemet säkerhetskonfigurationer – data uppdateras inom 48 timmar
- Systemuppdateringar – data uppdateras inom 24 timmar
- Endpoint Protection skickar – uppdatera data i 8 timmar

Security Center vanligtvis söker efter nya data varje timme. Värdena svarstiden är ett värsta scenario där det inte senaste skanningen eller en sökning misslyckades.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Varför visas meddelandet ”VM-agenten är saknas”?
VM-agenten måste installeras på virtuella datorer om du vill aktivera datainsamling. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Information om hur du installerar den Virtuella Datoragenten på andra virtuella datorer finns i bloggposten [VM-Agent och tillägg för](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
