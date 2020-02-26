---
title: Azure Security Center FAQ – insamling och agenter för data
description: Vanliga frågor om data insamling, agenter och arbets ytor för Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot
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
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599436"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Vanliga frågor och svar om data insamling, agenter och arbets ytor

Security Center samlar in data från dina virtuella Azure-datorer (VM), skalnings uppsättningar för virtuella datorer, IaaS behållare och icke-Azure-datorer (inklusive lokala datorer) för att övervaka säkerhets problem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Faktureras jag för Azure Monitor loggar på arbets ytorna som skapats av Security Center?

Nej. Arbets ytor som skapats av Security Center, och som kon figurer ATS för Azure Monitor loggar per nod fakturering, debiteras inte Azure Monitor loggar. Security Center fakturering baseras alltid på din säkerhetsprincip i Security Center och de lösningar som är installerad på en arbetsyta:

- **Kostnads fri nivå** – Security Center aktiverar lösningen "SecurityCenterFree" på standard arbets ytan. Du debiteras inte för den kostnads fria nivån.

- **Standard-nivå** – Security Center aktiverar säkerhets lösningen på standard arbets ytan.

Mer information om priser finns i [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Pris nivån för Log Analytics för arbets ytor som skapats av Security Center påverkar inte Security Center fakturering.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Vad innebär en virtuell dator för automatisk etablering av installationen av Microsoft Monitoring Agent?

Kvalificera dig om Windows eller Linux IaaS-datorer:

- Tillägget Microsoft Monitoring Agent är inte installerat på den virtuella datorn.
- Den virtuella datorn är i körningstillstånd.
- Windows eller Linux [Azure Virtual Machine agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) är installerad.
- Den virtuella datorn används inte som en enhet, till exempel Brandvägg för webbaserade program eller nästa generations brandvägg.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan jag ta bort Standardarbetsytor som skapats av Security Center?

**Det rekommenderas inte att du tar bort standard arbets ytan.** Security Center använder Standardarbetsytor för att lagra säkerhetsdata från dina virtuella datorer. Om du tar bort en arbetsyta Security Center kan inte samla in dessa data och vissa säkerhetsrekommendationer och aviseringar är inte tillgängliga.

Ta bort Microsoft Monitoring Agent på de virtuella datorerna är anslutna till den borttagna arbetsytan om du vill återställa. Security Center installerar om agenten och skapar nya Standardarbetsytor.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hur kan jag använda min befintliga Log Analytics-arbetsyta?

Du kan välja en befintlig Log Analytics-arbetsyta för att lagra data som samlas in av Security Center. Använda din befintliga Log Analytics-arbetsyta:

- Arbetsytan måste vara associerad med din valda Azure-prenumeration.
- Som ett minimum måste du ha läsbehörighet till arbetsytan.

Att välja en befintlig Log Analytics-arbetsyta:

1. Under **säkerhets princip – data insamling**väljer du **Använd en annan arbets yta**.

    ![Använd en annan arbetsyta][4]

1. Välj en arbetsyta för att lagra insamlade data från den nedrullningsbara menyn.

    > [!NOTE]
    > I nedrullningsbara menyn visas endast arbetsytor som du har åtkomst till och finns i din Azure-prenumeration.

1. Välj **Spara**. Du får en fråga om du vill konfigurera om övervakade virtuella datorer.

    - Välj **Nej** om du vill att de nya inställningarna för arbets ytan **bara ska gälla för nya virtuella datorer**. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer; Nyligen identifierade virtuella datorer som inte har Microsoft Monitoring Agent installerad.
    - Välj **Ja** om du vill att de nya inställningarna för arbets ytan ska **tillämpas på alla virtuella datorer**. Dessutom kan återansluta varje virtuell dator som är anslutna till en Security Center som skapat arbetsyta till den nya målarbetsytan.

    > [!NOTE]
    > Om du väljer **Ja**tar du inte bort några arbets ytor som skapats av Security Center tills alla virtuella datorer har återanslutits till den nya mål arbets ytan. Den här åtgärden misslyckas om en arbetsyta har tagits bort för tidigt.

    - Välj **Avbryt**om du vill avbryta åtgärden.

## Vad händer om Microsoft Monitoring Agent redan har installerats som ett tillägg på den virtuella datorn?<a name="mmaextensioninstalled"></a>

När övervaknings agenten installeras som ett tillägg tillåter tilläggs konfigurationen rapportering till endast en enda arbets yta. Security Center åsidosätts inte befintliga anslutningar till arbetsytor som användaren. Security Center kommer att lagra säkerhets data från en virtuell dator i en arbets yta som redan är ansluten, förutsatt att lösningen "säkerhet" eller "SecurityCenterFree" har installerats på den. Security Center kan uppgradera tilläggs versionen till den senaste versionen i den här processen.

Mer information finns i [Automatisk etablering i fall av en redan befintlig agent installation](security-center-enable-data-collection.md#preexisting).



## Vad händer om en Microsoft Monitoring Agent installeras direkt på datorn men inte som ett tillägg (direkt agent)?<a name="directagentinstalled"></a>

Om Microsoft Monitoring Agent installeras direkt på den virtuella datorn (inte som ett Azure-tillägg) kommer Security Center att installera Microsoft Monitoring Agent-tillägget och kan uppgradera Microsoft Monitoring Agent till den senaste versionen.

Den installerade agenten fortsätter att rapportera till sina redan konfigurerade arbets ytor och rapporterar dessutom till arbets ytan som kon figurer ATS i Security Center (Multi-värdar stöds på Windows-datorer).

Om den konfigurerade arbets ytan är en användar arbets yta (inte Security Center standard arbets ytan) måste du installera lösningen "Security/" SecurityCenterFree på den för Security Center för att börja bearbeta händelser från virtuella datorer och datorer som rapporterar till arbets ytan.

För Linux-datorer stöds inte agent multi-värdar ännu, vilket innebär att om en befintlig agent installation identifieras sker ingen automatisk etablering och datorns konfiguration ändras inte.

För befintliga datorer på prenumerationer som har registrerats på Security Center före den 17 2019 mars, kommer Microsoft Monitoring Agent-tillägget inte att installeras, och datorn kommer inte att påverkas om en befintlig agent identifieras. De här datorerna finns i rekommendationen "lösa övervaknings agent hälso problem på dina datorer" för att lösa problem med Agent installationen på dessa datorer

Mer information finns i nästa avsnitt [Vad händer om en System Center Operations Manager-eller OMS Direct-agent redan är installerad på den virtuella datorn?](#scomomsinstalled)

## Vad händer om en System Center Operations Manager agent redan är installerad på den virtuella datorn?<a name="scomomsinstalled"></a>

Security Center kommer att installera Microsoft Monitoring Agent-tillägget sida vid sida till den befintliga System Center Operations Manager-agenten. Den befintliga agenten fortsätter att rapportera till System Center Operations Manager servern på vanligt sätt. Observera att Operations Manager agent och Microsoft Monitoring Agent delar gemensamma kör tids bibliotek, som kommer att uppdateras till den senaste versionen under den här processen. OBS! om version 2012 av Operations Manager agenten är installerad ska du inte aktivera automatisk etablering (hanterings funktioner kan gå förlorade när Operations Manager-servern också är version 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Vad är effekt vid borttagning av dessa tillägg?

Om du tar bort tillägget Microsoft Monitoring Security Center kan inte samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och aviseringar är inte tillgängliga. Security Center anger att den virtuella datorn saknar tillägget och installerar om tillägget inom 24 timmar.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hur förhindrar jag att agenten för automatisk installation och arbetsytan skapa?

Du kan inaktivera automatisk etablering för dina prenumerationer i säkerhetsprincipen men detta rekommenderas inte. Stänga av Automatisk etablering gränser Security Center-rekommendationer och aviseringar. Inaktivera automatisk etablering:

1. Om din prenumeration har kon figurer ATS för standard nivån öppnar du säkerhets principen för den prenumerationen och väljer den **kostnads fria** nivån.

   ![Prisnivå][1]

1. Stäng sedan av automatisk etablering genom att välja **av** på sidan **säkerhets princip – data insamling** .
   ![Datainsamling][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Ska jag välja bort automatiska agentinstallation och arbetsytan skapas?

> [!NOTE]
> Var noga med att granska avsnitten [vilka är konsekvenserna av väljer out?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) och [rekommenderade steg när du väljer ut](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) om du väljer att inte välja automatisk etablering.

Du kanske vill välja bort automatisk etablering om följande gäller för dig:

- Agenten för automatisk installation av Security Center gäller för hela prenumerationen. Du kan inte använda automatisk installation till en delmängd av virtuella datorer. Om det finns kritiska virtuella datorer som inte kan installeras med Microsoft Monitoring Agent, bör du välja bort automatisk etablering.
- Installationen av tillägget Microsoft Monitoring Agent (MMA) uppdaterar agentens version. Detta gäller för en direkt agent och en System Center Operations Manager-agent (i den senare, Operations Manager och MMA delar vanliga körnings bibliotek, som kommer att uppdateras i processen). Om den installerade Operations Manager agenten är version 2012 och uppgraderas kan hanterings funktioner gå förlorade när Operations Manager-servern också är version 2012. Överväg väljer från automatisk etablering om den installerade Operations Manager-agenten är version 2012.
- Om du har en anpassad arbets yta utanför prenumerationen (en centraliserad arbets yta) bör du inte välja automatisk etablering. Du kan manuellt installera Microsoft Monitoring Agent-tillägget och ansluta den arbetsytan utan att Security Center åsidosätta anslutningen.
- Om du vill undvika att skapa flera arbetsytor per prenumeration och du har en egen anpassad arbetsyta inom prenumerationen, har du två alternativ:

   1. Du kan välja bort automatisk etablering. När du har migrerat anger du inställningarna för standard arbets ytan enligt beskrivningen i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Eller så kan du tillåta migreringen slutföras, Microsoft Monitoring Agent installeras på de virtuella datorerna, och de virtuella datorerna är anslutna till arbetsytan skapades. Välj din egen anpassade arbetsyta genom att ange standardinställningen för arbetsytan med valde omkonfigurera redan installerade agenter. Mer information finns i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Vilka är effekterna av väljer bort automatisk etablering?

När migreringen är klar kan Security Center inte samla in säkerhets data från den virtuella datorn och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga. Om du avanmäler dig installerar du Microsoft Monitoring Agent manuellt. Se [rekommenderade steg när du väljer ut](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Vilka är de rekommenderade stegen när du väljer bort automatisk etablering?

Installera Microsoft Monitoring Agent-tillägget manuellt så Security Center kan samla in säkerhets data från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar. Se [agent installation för virtuell Windows-dator](../virtual-machines/extensions/oms-windows.md) eller [agent installation för virtuell Linux-dator](../virtual-machines/extensions/oms-linux.md) för att få hjälp med installationen.

Du kan ansluta agenten till en befintlig anpassad arbetsyta eller Security Center skapat arbetsyta. Om en anpassad arbetsyta inte har de ”säkerhet” eller ”SecurityCenterFree” lösningarna som är aktiverad, du måste använda en lösning. Om du vill använda väljer du den anpassade arbets ytan eller prenumerationen och använder en pris nivå via sidan **säkerhets policy – pris nivå** .

   ![Prisnivå][1]

Security Center aktiverar till rätt lösning i arbetsytan baserat på den valda prisnivån.


## Hur gör jag för att ta bort OMS-tillägg som installerats av Security Center?<a name="remove-oms"></a>

Du kan manuellt ta bort Microsoft Monitoring Agent. Detta rekommenderas inte eftersom det begränsar Security Center-rekommendationer och aviseringar.

> [!NOTE]
> Om datainsamling har aktiverats Security Center kommer att installera om agenten efter att du tar bort den.  Du måste inaktivera datainsamling innan du tar bort agenten manuellt. Se Hur gör jag för att stoppa den automatiska Agent installationen och arbets ytan? instruktioner om inaktive ring av data insamling.

Ta manuellt bort agenten:

1.  Öppna **Log Analytics**på portalen.

1.  På sidan Log Analytics väljer du en arbets yta:

1.  Välj de virtuella datorer som du inte vill övervaka och välj **Koppla från**.

   ![Ta bort agenten][3]

> [!NOTE]
> Om en virtuell Linux-dator redan har en icke-tillägg OMS-agent tar borttagning av tillägget bort agenten även om du måste installera om den.


## <a name="how-do-i-disable-data-collection"></a>Hur jag för att inaktivera insamling av data?

Automatisk etablering är inaktiverat som standard. Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. Automatisk etablering rekommenderas för att få säkerhets aviseringar och rekommendationer om system uppdateringar, sårbarheter för operativ system och Endpoint Protection.

Om du vill inaktivera data insamling [loggar du in på Azure Portal](https://portal.azure.com), väljer **bläddra**, väljer **Security Center**och väljer **Välj princip**. Välj den prenumeration du vill avaktivera automatisk etablering för. När du väljer en prenumerations **säkerhets princip öppnas data insamling** . Under **Automatisk etablering**, väljer du **av**.


## <a name="how-do-i-enable-data-collection"></a>Hur gör jag för att aktivera insamling av data?

Du kan aktivera datainsamling för din Azure-prenumeration i säkerhetsprincipen. Att aktivera insamling av data. [Logga](https://portal.azure.com)in på Azure Portal, Välj **bläddra**, Välj **Security Center**och välj **säkerhets princip**. Välj den prenumeration som du vill aktivera automatisk etablering. När du väljer en prenumerations **säkerhets princip öppnas data insamling** . Under **Automatisk etablering**väljer du **på**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när datainsamling har aktiverats?

När automatisk etablering är aktiverat, stöds etablerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer och alla nya som skapas. Automatisk etablering rekommenderas, men det finns även manuell agent installation. [Lär dig att installera tillägget Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agenten gör det möjligt för processen att skapa händelse 4688 och fältet *kommandorad* i händelse 4688. Nya processer som skapats på den virtuella datorn registreras av händelseloggen och övervakas av Security Centers identifieringstjänster. Mer information om de uppgifter som registrerats för varje ny process finns [i beskrivnings fält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar in 4688 händelser som skapats på den virtuella datorn också och lagrar dem i sökningen.

Agenten aktiverar även insamling av data för [anpassningsbara program kontroller](security-center-adaptive-application.md), Security Center konfigurerar en lokal AppLocker-princip i gransknings läge så att alla program tillåts. Den här principen gör att AppLocker genererar händelser som sedan samlas in och utnyttjas av Security Center. Det är viktigt att Observera att den här principen inte konfigureras på alla datorer där det finns redan en konfigurerade AppLocker-principen. 

När Security Center identifierar misstänkt aktivitet på den virtuella datorn, meddelas kunden via e-post om [säkerhets kontakt information](security-center-provide-security-contact-details.md) har tillhandahållits. En avisering visas också i instrumentpanelen för Security Center security-aviseringar.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Kommer Security Center fungera med en OMS-Gateway?

Ja. Azure Security Center utnyttjar Azure Monitor för att samla in data från virtuella Azure-datorer och-servrar med hjälp av Microsoft Monitoring Agent.
För att samla in data måste varje virtuell dator och Server ansluta till Internet via HTTPS. Anslutningen kan dirigeras via en proxy eller via [OMS-gatewayen](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Påverkar Övervakningsagenttjänsten prestanda Mina servrar?

Agenten använder en liten mängd systemresurser och bör ha liten inverkan på prestanda. Mer information om prestanda påverkan och agenten och tillägget finns i [planerings-och drift guide](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Var lagras mina data?

Data som samlas in från agenten lagras i en befintlig Log Analytics-arbetsyta som är associerad med din prenumeration eller en ny arbetsyta. Mer information finns i [data säkerhet](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png