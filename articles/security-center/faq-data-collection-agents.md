---
title: Vanliga frågor och svar om Azure Security Center – datainsamling och agenter
description: Vanliga frågor och svar om datainsamling, agenter och arbetsytor för Azure Security Center, en produkt som hjälper dig att förebygga, identifiera och svara på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436199"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Vanliga frågor – Frågor om datainsamling, agenter och arbetsytor

Security Center samlar in data från dina virtuella Azure-datorer ( Virtual Machine scale sets, IaaS-behållare och icke-Azure-datorer (inklusive lokala datorer) för att övervaka säkerhetsproblem och hot. Data samlas in med log analytics-agenten, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till arbetsytan för analys.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Faktureras jag för Azure Monitor-loggar på de arbetsytor som skapats av Security Center?

Nej. Arbetsytor som skapats av Security Center, medan de är konfigurerade för Azure Monitor-loggar per nodfakturering, medför inte avgifter för Azure Monitor-loggar. Security Center-faktureringen baseras alltid på säkerhetscentrets säkerhetsprincip och lösningarna som är installerade på en arbetsyta:

- **Kostnadsfri nivå** – Security Center aktiverar SecurityCenterFree-lösningen på standardarbetsytan. Du debiteras inte för den kostnadsfria nivån.

- **Standardnivå** – Security Center aktiverar "Säkerhet"-lösningen på standardarbetsytan.

Mer information om priser finns i [Security Center-priser](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Prisnivån för logganalys för arbetsytor som skapats av Security Center påverkar inte Security Center-faktureringen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Vad kvalificerar en virtuell dator för automatisk etablering av Log Analytics-agentinstallationen?

Virtuella datorer med Windows eller Linux IaaS kvalificerar sig om:

- Agenttillägget Log Analytics är inte installerat på den virtuella datorn.
- Den virtuella datorn är i körläge.
- Windows eller Linux [Azure Virtual Machine Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) är installerad.
- Den virtuella datorn används inte som en installation, till exempel brandvägg för webbprogram eller nästa generations brandvägg.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan jag ta bort standardarbetsytorna som skapats av Security Center?

**Det rekommenderas inte att ta bort standardarbetsytan.** Security Center använder standardarbetsytorna för att lagra säkerhetsdata från dina virtuella datorer. Om du tar bort en arbetsyta kan Security Center inte samla in dessa data och vissa säkerhetsrekommendationer och varningar är inte tillgängliga.

Om du vill återställa tar du bort Log Analytics-agenten på de virtuella datorer som är anslutna till den borttagna arbetsytan. Security Center installerar om agenten och skapar nya standardarbetsytor.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hur kan jag använda min befintliga Log Analytics-arbetsyta?

Du kan välja en befintlig Log Analytics-arbetsyta för att lagra data som samlats in av Security Center. Så här använder du din befintliga Log Analytics-arbetsyta:

- Arbetsytan måste associeras med din valda Azure-prenumeration.
- Du måste ha läsbehörighet för att komma åt arbetsytan.

Så här väljer du en befintlig Log Analytics-arbetsyta:

1. Under **Säkerhetsprincip – Datainsamling**väljer du **Använd en annan arbetsyta**.

    ![Använda en annan arbetsyta][4]

1. På rullytan väljer du en arbetsyta för att lagra insamlade data.

    > [!NOTE]
    > På rullytan visas endast arbetsytor som du har åtkomst till och som finns i din Azure-prenumeration.

1. Välj **Spara**. Du kommer att bli tillfrågad om du vill konfigurera om övervakade virtuella datorer.

    - Välj **Nej** om du vill att de nya arbetsyteinställningarna **ska gälla endast för nya virtuella datorer**. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer. nyupptäckta virtuella datorer som inte har Log Analytics-agenten installerad.
    - Välj **Ja** om du vill att de nya arbetsyteinställningarna **ska gälla på alla virtuella datorer**. Dessutom återansluts varje virtuell dator som är ansluten till en säkerhetscenter som skapas till den nya målarbetsytan.

    > [!NOTE]
    > Om du väljer **Ja**ska du inte ta bort några arbetsytor som skapats av Security Center förrän alla virtuella datorer har återanslutits till den nya målarbetsytan. Den här åtgärden misslyckas om en arbetsyta tas bort för tidigt.

    - Om du vill avbryta åtgärden väljer du **Avbryt**.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Vad händer om Log Analytics-agenten redan har installerats som ett tillägg på den virtuella datorn?<a name="mmaextensioninstalled"></a>

När övervakningsagenten installeras som ett tillägg tillåter tilläggskonfigurationen rapportering till endast en enda arbetsyta. Security Center åsidosätter inte befintliga anslutningar till användararbetsytor. Security Center lagrar säkerhetsdata från en virtuell dator på en arbetsyta som redan är ansluten, förutsatt att lösningen "Säkerhet" eller "SecurityCenterFree" har installerats på den. Security Center kan uppgradera tilläggsversionen till den senaste versionen i den här processen.

Mer information finns [i Automatisk etablering vid en befintlig agentinstallation](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Vad händer om en Log Analytics-agent är direkt installerad på datorn men inte som ett tillägg (Direct Agent)?<a name="directagentinstalled"></a>

Om Log Analytics-agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg) installerar Security Center agenttillägget Log Analytics och kan uppgradera Log Analytics-agenten till den senaste versionen.

Agenten som installerats fortsätter att rapportera till sina redan konfigurerade arbetsytor och rapporterar dessutom till arbetsytan som konfigurerats i Security Center (Multi-homing stöds på Windows-datorer).

Om den konfigurerade arbetsytan är en användararbetsyta (inte Säkerhetscenters standardarbetsyta) måste du installera lösningen "Security/"SecurityCenterFree" på den för att Security Center ska kunna börja bearbeta händelser från virtuella datorer och datorer som rapporterar till den arbetsytan.

För Linux-datorer stöds agent multi-homing ännu inte - om en befintlig agentinstallation upptäcks kommer automatisk etablering inte att ske och maskinens konfiguration inte kommer att ändras.

För befintliga datorer på prenumerationer som finns ombord på Security Center före den 17 mars 2019, när en befintlig agent identifieras, installeras inte tillägget Log Analytics-agent och datorn påverkas inte. För dessa datorer, se rekommendationen "Lös hälsoproblem för övervakningsagenten på dina datorer" för att lösa agentinstallationsproblemen på dessa datorer

Mer information finns i nästa avsnitt [Vad händer om en System Center Operations Manager eller OMS direct agent redan är installerad på min virtuella dator?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Vad händer om en System Center Operations Manager-agent redan är installerad på min virtuella dator?<a name="scomomsinstalled"></a>

Säkerhetscenter installerar log analytics-agenttillägget sida vid sida till den befintliga System Center Operations Manager-agenten. Den befintliga agenten fortsätter att rapportera till System Center Operations Manager-servern normalt. Observera att Operations Manager-agenten och Log Analytics-agenten delar vanliga körningsbibliotek, som uppdateras till den senaste versionen under den här processen. Om version 2012 av Operations Manager-agenten är installerad ska du inte aktivera automatisk etablering (hanterbarhetsfunktioner kan gå förlorade när Operations Manager-servern också är version 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Vad är effekten av att ta bort dessa tillägg?

Om du tar bort Microsoft Monitoring Extension kan Security Center inte samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och varningar är inte tillgängliga. Inom 24 timmar bestämmer Security Center att den virtuella datorn saknar tillägget och installerar om tillägget.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hur stoppar jag installation av automatisk agent och skapandet av arbetsytor?

Du kan inaktivera automatisk etablering för dina prenumerationer i säkerhetsprincipen, men detta rekommenderas inte. Om du inaktiverar automatisk etablering begränsas rekommendationer och aviseringar från Security Center. Så här inaktiverar du automatisk etablering:

1. Om din prenumeration är konfigurerad för standardnivån öppnar du säkerhetsprincipen för den prenumerationen och väljer den **kostnadsfria** nivån.

   ![Prisnivå][1]

1. Stäng sedan av automatisk etablering genom att välja **Av** på sidan **Säkerhetsprinciper – Datainsamling.**
   ![Datainsamling][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Ska jag välja bort installation av automatisk agent och skapande av arbetsytor?

> [!NOTE]
> Var noga med att granska avsnitt [Vilka är konsekvenserna av att välja bort?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) [recommended steps when opting out](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)

Du kanske vill välja bort automatisk etablering om följande gäller dig:

- Automatisk agentinstallation av Security Center gäller för hela prenumerationen. Du kan inte använda automatisk installation på en delmängd av virtuella datorer. Om det finns viktiga virtuella datorer som inte kan installeras med Log Analytics-agenten bör du välja bort automatisk etablering.
- Installationen av tillägget Log Analytics-agent uppdaterar agentens version. Detta gäller för en direktagent och en System Center Operations Manager-agent (i den senare delar Operations Manager och Log Analytics-agenten gemensamma körningsbibliotek – som kommer att uppdateras i processen). Om den installerade Operations Manager-agenten är version 2012 och uppgraderas kan hanterbarhetsfunktioner gå förlorade när Operations Manager-servern också är version 2012. Överväg att välja bort automatisk etablering om den installerade Operations Manager-agenten är version 2012.
- Om du har en anpassad arbetsyta utanför prenumerationen (en centraliserad arbetsyta) bör du välja bort automatisk etablering. Du kan installera tillägget Log Analytics-agent manuellt och ansluta den till arbetsytan utan att Säkerhetscenter åsidosätter anslutningen.
- Om du vill undvika att skapa flera arbetsytor per prenumeration och du har en egen anpassad arbetsyta i prenumerationen har du två alternativ:

   1. Du kan välja bort automatisk etablering. Efter migreringen ställer du in standardinställningarna för arbetsytan enligt beskrivningen i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Du kan också tillåta att migreringen slutförs, log analytics-agenten installeras på de virtuella datorerna och de virtuella datorer som är anslutna till den skapade arbetsytan. Välj sedan en egen anpassad arbetsyta genom att ange standardinställningen för arbetsytan med att välja att konfigurera om de redan installerade agenterna. Mer information finns i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Vilka är konsekvenserna av att välja bort automatisk etablering?

När migreringen är klar kan Security Center inte samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och aviseringar är inte tillgängliga. Om du avanmäler dig installerar du Log Analytics-agenten manuellt. Se [rekommenderade steg när du väljer bort](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Vilka är de rekommenderade stegen när du väljer bort automatisk etablering?

Installera tillägget Log Analytics-agenten manuellt så att Security Center kan samla in säkerhetsdata från dina virtuella datorer och ge rekommendationer och aviseringar. Mer information om installation finns i [agentinstallation för Windows VM](../virtual-machines/extensions/oms-windows.md) eller [agentinstallation för Virtuella Linux-datorer.](../virtual-machines/extensions/oms-linux.md)

Du kan ansluta agenten till en befintlig anpassad arbetsyta eller Security Center skapad arbetsyta. Om en anpassad arbetsyta inte har lösningarna "Säkerhet" eller "SecurityCenterFree" aktiverat måste du använda en lösning. Om du vill använda väljer du den anpassade arbetsytan eller prenumerationen och tillämpar en prisnivå via sidan **Säkerhetsprincip – Prisnivå.**

   ![Prisnivå][1]

Security Center aktiverar rätt lösning på arbetsytan baserat på den valda prisnivån.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hur tar jag bort OMS-tillägg som installerats av Security Center?<a name="remove-oms"></a>

Du kan ta bort Log Analytics-agenten manuellt. Detta rekommenderas inte eftersom det begränsar rekommendationer och aviseringar i Security Center.

> [!NOTE]
> Om datainsamling är aktiverat installerar Security Center om agenten när du har tagit bort den.  Du måste inaktivera datainsamling innan agenten tas bort manuellt. Se Hur stoppar jag installation av automatisk agent och skapandet av arbetsytor? instruktioner om hur du inaktiverar datainsamling.

Så här tar du bort agenten manuellt:

1.    Öppna **Log Analytics**i portalen .

1.    Välj en arbetsyta på sidan Logganalys:

1.    Markera de virtuella datorer som du inte vill övervaka och välj **Koppla från**.

   ![Ta bort agenten][3]

> [!NOTE]
> Om en Linux-VM redan har en OMS-agent som inte är till förlängning tar du bort tillägget och du måste installera om den.


## <a name="how-do-i-disable-data-collection"></a>Hur inaktiverar jag datainsamling?

Automatisk etablering är inaktiverad som standard. Du kan inaktivera automatisk etablering från resurser när som helst genom att inaktivera den här inställningen i säkerhetsprincipen. Automatisk etablering rekommenderas starkt för att få säkerhetsaviseringar och rekommendationer om systemuppdateringar, os-sårbarheter och slutpunktsskydd.

Om du vill inaktivera datainsamling [loggar du in på Azure-portalen](https://portal.azure.com), väljer **Bläddra,** väljer **Säkerhetscenter**och väljer **Välj princip**. Välj den prenumeration du vill avaktivera automatisk etablering för. När du väljer en **säkerhetsprinciper för prenumerationen öppnas datainsamlingen.** Under **Automatisk etablering**väljer du **Av**.


## <a name="how-do-i-enable-data-collection"></a>Hur aktiverar jag datainsamling?

Du kan aktivera datainsamling för din Azure-prenumeration i säkerhetsprincipen. Så här aktiverar du datainsamling. [Logga in på Azure-portalen,](https://portal.azure.com)välj **Bläddra,** välj **Säkerhetscenter**och välj **Säkerhetsprincip**. Välj den prenumeration som du vill aktivera automatisk etablering. När du väljer en **säkerhetsprinciper för prenumerationen öppnas datainsamlingen.** Under **Automatisk etablering**väljer du **På**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när datainsamling är aktiverat?

När automatisk etablering är aktiverad etablerar Security Center Log Analytics-agenten på alla azure-virtuella datorer som stöds och alla nya som skapas. Automatisk etablering rekommenderas men manuell agentinstallation är också tillgänglig. [Läs om hur du installerar tillägget Log Analytics agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agenten aktiverar processskapande händelse 4688 och *CommandLine-fältet* i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av EventLog och övervakas av Security Centers identifieringstjänster. Mer information om information om information som registrerats för varje ny process finns [i beskrivningsfält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar också in de 4688 händelser som skapats på den virtuella datorn och lagrar dem i sökning.

Agenten möjliggör också datainsamling för [adaptiva programkontroller](security-center-adaptive-application.md), Security Center konfigurerar en lokal AppLocker-princip i granskningsläge så att alla program tillåts. Den här principen kommer att orsaka AppLocker att generera händelser, som sedan samlas in och utnyttjas av Security Center. Det är viktigt att notera att den här principen inte kommer att konfigureras på alla datorer där det redan finns en konfigurerad AppLocker-princip. 

När Security Center upptäcker misstänkt aktivitet på den virtuella datorn meddelas kunden via e-post om [säkerhetskontaktinformation](security-center-provide-security-contact-details.md) har angetts. En avisering visas också på instrumentpanelen för säkerhetsvarningar i Security Center.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Fungerar Security Center med en OMS-gateway?

Ja. Azure Security Center använder Azure Monitor för att samla in data från virtuella Azure-datorer och -servrar med hjälp av Log Analytics-agenten.
För att samla in data måste varje virtuell dator och server ansluta till Internet med HTTPS. Anslutningen kan vara direkt, med hjälp av en proxy eller via [OMS Gateway](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Påverkar övervakningsagenten mina servrars prestanda?

Agenten förbrukar en nominell mängd systemresurser och bör ha liten inverkan på prestanda. Mer information om prestandapåverkan och agenten och tillägget finns i [planerings- och verksamhetsguiden](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Var lagras mina data?

Data som samlas in från den här agenten lagras antingen på en befintlig Log Analytics-arbetsyta som är associerad med din prenumeration eller en ny arbetsyta. Mer information finns i [Datasäkerhet](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
