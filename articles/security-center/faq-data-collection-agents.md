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
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436199"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Vanliga frågor och svar om data insamling, agenter och arbets ytor

Security Center samlar in data från dina virtuella Azure-datorer (VM), skalnings uppsättningar för virtuella datorer, IaaS behållare och icke-Azure-datorer (inklusive lokala datorer) för att övervaka säkerhets problem och hot. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Faktureras jag för Azure Monitor loggar på arbets ytorna som skapats av Security Center?

Nej. Arbets ytor som skapats av Security Center, och som kon figurer ATS för Azure Monitor loggar per nod fakturering, debiteras inte Azure Monitor loggar. Security Center faktureringen baseras alltid på din Security Center säkerhets princip och de lösningar som är installerade på en arbets yta:

- **Kostnads fri nivå** – Security Center aktiverar lösningen "SecurityCenterFree" på standard arbets ytan. Du debiteras inte för den kostnads fria nivån.

- **Standard-nivå** – Security Center aktiverar säkerhets lösningen på standard arbets ytan.

Mer information om priser finns i [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Pris nivån för Log Analytics för arbets ytor som skapats av Security Center påverkar inte Security Center fakturering.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Vad är kvalificerat för en virtuell dator för automatisk etablering av den Log Analytics Agent installationen?

Virtuella Windows-eller Linux IaaS-datorer är kvalificerade om:

- Tillägget för Log Analytics agent är inte installerat på den virtuella datorn.
- Den virtuella datorn körs.
- Windows eller Linux [Azure Virtual Machine agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) är installerad.
- Den virtuella datorn används inte som en installation av en brand vägg för webbaserade program eller nästa generations brand vägg.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan jag ta bort standard arbets ytor som skapats av Security Center?

**Det rekommenderas inte att du tar bort standard arbets ytan.** Security Center använder standard arbets ytorna för att lagra säkerhets data från dina virtuella datorer. Om du tar bort en arbets yta kan Security Center inte samla in dessa data och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga.

Ta bort Log Analytics agenten på de virtuella datorer som är anslutna till den borttagna arbets ytan för att återställa. Security Center installerar om agenten och skapar nya standard arbets ytor.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hur kan jag använda min befintliga Log Analytics-arbetsyta?

Du kan välja en befintlig Log Analytics-arbetsyta för att lagra data som samlas in av Security Center. Så här använder du din befintliga Log Analytics-arbetsyta:

- Arbets ytan måste vara kopplad till den valda Azure-prenumerationen.
- Du måste minst ha Läs behörighet för att få åtkomst till arbets ytan.

Så här väljer du en befintlig Log Analytics arbets yta:

1. Under **säkerhets princip – data insamling**väljer du **Använd en annan arbets yta**.

    ![Använd en annan arbets yta][4]

1. I den nedrullningsbara menyn väljer du en arbets yta där insamlade data ska lagras.

    > [!NOTE]
    > I den nedrullningsbara menyn visas endast arbets ytor som du har åtkomst till och som finns i din Azure-prenumeration.

1. Välj **Spara**. Du får en fråga om du vill konfigurera om övervakade virtuella datorer.

    - Välj **Nej** om du vill att de nya inställningarna för arbets ytan **bara ska gälla för nya virtuella datorer**. De nya inställningarna för arbets ytan gäller endast nya agent installationer. nyligen identifierade virtuella datorer som inte har Log Analytics-agenten installerad.
    - Välj **Ja** om du vill att de nya inställningarna för arbets ytan ska **tillämpas på alla virtuella datorer**. Dessutom återansluts varje virtuell dator som är ansluten till en Security Center skapad arbets yta till den nya mål arbets ytan.

    > [!NOTE]
    > Om du väljer **Ja**tar du inte bort några arbets ytor som skapats av Security Center tills alla virtuella datorer har återanslutits till den nya mål arbets ytan. Den här åtgärden Miss lyckas om en arbets yta tas bort för tidigt.

    - Välj **Avbryt**om du vill avbryta åtgärden.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Vad händer om Log Analytics agent redan har installerats som ett tillägg på den virtuella datorn?<a name="mmaextensioninstalled"></a>

När övervaknings agenten installeras som ett tillägg tillåter tilläggs konfigurationen rapportering till endast en enda arbets yta. Security Center åsidosätter inte befintliga anslutningar till användar arbets ytor. Security Center kommer att lagra säkerhets data från en virtuell dator i en arbets yta som redan är ansluten, förutsatt att lösningen "säkerhet" eller "SecurityCenterFree" har installerats på den. Security Center kan uppgradera tilläggs versionen till den senaste versionen i den här processen.

Mer information finns i [Automatisk etablering i fall av en redan befintlig agent installation](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Vad händer om en Log Analytics Agent installeras direkt på datorn men inte som ett tillägg (direkt agent)?<a name="directagentinstalled"></a>

Om Log Analytics-agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg), kommer Security Center att installera Log Analytics agent tillägget, och kan uppgradera Log Analytics agent till den senaste versionen.

Den installerade agenten fortsätter att rapportera till sina redan konfigurerade arbets ytor och rapporterar dessutom till arbets ytan som kon figurer ATS i Security Center (Multi-värdar stöds på Windows-datorer).

Om den konfigurerade arbets ytan är en användar arbets yta (inte Security Center standard arbets ytan) måste du installera lösningen "Security/" SecurityCenterFree på den för Security Center för att börja bearbeta händelser från virtuella datorer och datorer som rapporterar till arbets ytan.

För Linux-datorer stöds inte agent multi-värdar ännu, vilket innebär att om en befintlig agent installation identifieras sker ingen automatisk etablering och datorns konfiguration ändras inte.

För befintliga datorer på prenumerationer som har registrerats på Security Center före mars 17 2019, installeras inte Log Analytics agent-tillägget och datorn påverkas inte om en befintlig agent identifieras. De här datorerna finns i rekommendationen "lösa övervaknings agent hälso problem på dina datorer" för att lösa problem med Agent installationen på dessa datorer

Mer information finns i nästa avsnitt [Vad händer om en System Center Operations Manager-eller OMS Direct-agent redan är installerad på den virtuella datorn?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Vad händer om en System Center Operations Manager agent redan är installerad på den virtuella datorn?<a name="scomomsinstalled"></a>

Security Center kommer att installera Log Analytics agent-tillägget sida vid sida till den befintliga System Center Operations Manager agenten. Den befintliga agenten fortsätter att rapportera till System Center Operations Manager servern på vanligt sätt. Observera att Operations Manager agent och Log Analytics agent delar gemensamma kör tids bibliotek, som kommer att uppdateras till den senaste versionen under den här processen. OBS! om version 2012 av Operations Manager agenten är installerad ska du inte aktivera automatisk etablering (hanterings funktioner kan gå förlorade när Operations Manager-servern också är version 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Vad är effekten av att ta bort de här tilläggen?

Om du tar bort Microsoft Monitoring Extension kan Security Center inte samla in säkerhets data från den virtuella datorn och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga. Inom 24 timmar fastställer Security Center att den virtuella datorn saknar tillägget och installerar tillägget igen.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Vill du Hur gör jag för att stoppa den automatiska Agent installationen och arbets ytan?

Du kan inaktivera automatisk etablering för dina prenumerationer i säkerhets principen, men detta rekommenderas inte. Inaktivera automatiska etablerings gränser Security Center rekommendationer och aviseringar. Så här inaktiverar du automatisk etablering:

1. Om din prenumeration har kon figurer ATS för standard nivån öppnar du säkerhets principen för den prenumerationen och väljer den **kostnads fria** nivån.

   ![Prisnivå][1]

1. Stäng sedan av automatisk etablering genom att välja **av** på sidan **säkerhets princip – data insamling** .
   ![Datainsamling][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Bör jag inte välja att skapa en automatisk agent installation och skapa arbets ytor?

> [!NOTE]
> Var noga med att granska avsnitten [vilka är konsekvenserna av väljer out?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) och [rekommenderade steg när du väljer ut](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) om du väljer att inte välja automatisk etablering.

Du kanske vill inaktivera automatisk etablering om följande gäller för dig:

- Automatisk agent installation av Security Center gäller för hela prenumerationen. Du kan inte använda automatisk installation för en delmängd av virtuella datorer. Om det finns kritiska virtuella datorer som inte kan installeras med Log Analytics-agenten bör du inte välja automatisk etablering.
- Installationen av Log Analytics agent-tillägget uppdaterar agentens version. Detta gäller för en direkt agent och en System Center Operations Manager agent (i den senare Operations Manager och Log Analytics agent delar vanliga körnings bibliotek, som kommer att uppdateras i processen). Om den installerade Operations Manager agenten är version 2012 och uppgraderas kan hanterings funktioner gå förlorade när Operations Manager-servern också är version 2012. Överväg väljer från automatisk etablering om den installerade Operations Manager-agenten är version 2012.
- Om du har en anpassad arbets yta utanför prenumerationen (en centraliserad arbets yta) bör du inte välja automatisk etablering. Du kan installera Log Analytics agent-tillägget manuellt och ansluta det till arbets ytan utan att Security Center åsidosätta anslutningen.
- Om du vill undvika att skapa flera arbets ytor per prenumeration och du har en egen anpassad arbets yta i prenumerationen har du två alternativ:

   1. Du kan välja att inte använda automatisk etablering. När du har migrerat anger du inställningarna för standard arbets ytan enligt beskrivningen i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Du kan också låta migreringen slutföras, Log Analytics agenten ska installeras på de virtuella datorerna och de virtuella datorer som är anslutna till den skapade arbets ytan. Välj sedan din egen anpassade arbets yta genom att ange inställningen standard arbets yta med väljer i för att konfigurera om de redan installerade agenterna. Mer information finns i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Vilka är konsekvenserna av väljer out of automatisk etablering?

När migreringen är klar kan Security Center inte samla in säkerhets data från den virtuella datorn och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga. Om du avinstallerar installerar du Log Analytics-agenten manuellt. Se [rekommenderade steg när du väljer ut](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Vilka är de rekommenderade stegen vid väljer av automatisk etablering?

Installera Log Analytics agent-tillägget manuellt så Security Center kan samla in säkerhets data från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar. Se [agent installation för virtuell Windows-dator](../virtual-machines/extensions/oms-windows.md) eller [agent installation för virtuell Linux-dator](../virtual-machines/extensions/oms-linux.md) för att få hjälp med installationen.

Du kan ansluta agenten till en befintlig anpassad arbets yta eller Security Center skapade arbets ytan. Om en anpassad arbets yta inte har lösningarna "säkerhet" eller "SecurityCenterFree" aktiverade måste du tillämpa en lösning. Om du vill använda väljer du den anpassade arbets ytan eller prenumerationen och använder en pris nivå via sidan **säkerhets policy – pris nivå** .

   ![Prisnivå][1]

Security Center aktiverar rätt lösning på arbets ytan baserat på den valda pris nivån.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hur gör jag för att ta bort OMS-tillägg som installerats av Security Center?<a name="remove-oms"></a>

Du kan ta bort den Log Analytics agenten manuellt. Detta rekommenderas inte eftersom det begränsar Security Center rekommendationer och aviseringar.

> [!NOTE]
> Om data insamling har Aktiver ATS kommer Security Center att installera om agenten efter att du har tagit bort den.  Du måste inaktivera data insamlingen innan du tar bort agenten manuellt. Se Hur gör jag för att stoppa den automatiska Agent installationen och arbets ytan? instruktioner om inaktive ring av data insamling.

Ta bort agenten manuellt:

1.    Öppna **Log Analytics**på portalen.

1.    På sidan Log Analytics väljer du en arbets yta:

1.    Välj de virtuella datorer som du inte vill övervaka och välj **Koppla från**.

   ![Ta bort agenten][3]

> [!NOTE]
> Om en virtuell Linux-dator redan har en icke-tillägg OMS-agent tar borttagning av tillägget bort agenten även om du måste installera om den.


## <a name="how-do-i-disable-data-collection"></a>Vill du Hur gör jag för att inaktivera data insamling?

Automatisk etablering är inaktive rad som standard. Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhets principen. Automatisk etablering rekommenderas för att få säkerhets aviseringar och rekommendationer om system uppdateringar, sårbarheter för operativ system och Endpoint Protection.

Om du vill inaktivera data insamling [loggar du in på Azure Portal](https://portal.azure.com), väljer **bläddra**, väljer **Security Center**och väljer **Välj princip**. Välj den prenumeration du vill avaktivera automatisk etablering för. När du väljer en prenumerations **säkerhets princip öppnas data insamling** . Under **Automatisk etablering**, väljer du **av**.


## <a name="how-do-i-enable-data-collection"></a>Hur gör jag för att aktivera data insamling?

Du kan aktivera data insamling för din Azure-prenumeration i säkerhets principen. För att aktivera data insamling. [Logga](https://portal.azure.com)in på Azure Portal, Välj **bläddra**, Välj **Security Center**och välj **säkerhets princip**. Välj den prenumeration som du vill aktivera automatisk etablering för. När du väljer en prenumerations **säkerhets princip öppnas data insamling** . Under **Automatisk etablering**väljer du **på**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när data insamlingen är aktive rad?

När automatisk etablering har Aktiver ATS etablerar Security Center Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Automatisk etablering rekommenderas, men det finns även manuell agent installation. [Lär dig hur du installerar Log Analytics agent-tillägget](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agenten gör det möjligt för processen att skapa händelse 4688 och fältet *kommandorad* i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av EventLog och övervakas av Security Centers identifierings tjänster. Mer information om de uppgifter som registrerats för varje ny process finns [i beskrivnings fält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar även in 4688-händelser som skapats på den virtuella datorn och lagrar dem i sökningen.

Agenten aktiverar även insamling av data för [anpassningsbara program kontroller](security-center-adaptive-application.md), Security Center konfigurerar en lokal AppLocker-princip i gransknings läge så att alla program tillåts. Den här principen gör att AppLocker genererar händelser som sedan samlas in och utnyttjas av Security Center. Det är viktigt att Observera att den här principen inte kommer att konfigureras på datorer där det redan finns en konfigurerad AppLocker-princip. 

När Security Center identifierar misstänkt aktivitet på den virtuella datorn, meddelas kunden via e-post om [säkerhets kontakt information](security-center-provide-security-contact-details.md) har tillhandahållits. En avisering visas också i Security Center säkerhets aviserings instrument panel.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Kommer Security Center fungera med en OMS-Gateway?

Ja. Azure Security Center utnyttjar Azure Monitor för att samla in data från virtuella Azure-datorer och-servrar med hjälp av Log Analytics agenten.
För att samla in data måste varje virtuell dator och Server ansluta till Internet via HTTPS. Anslutningen kan dirigeras via en proxy eller via [OMS-gatewayen](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Påverkar övervaknings agenten prestanda för mina servrar?

Agenten förbrukar en nominell mängd system resurser och påverkar inte prestandan. Mer information om prestanda påverkan och agenten och tillägget finns i [planerings-och drift guide](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Var lagras mina data?

Data som samlas in från den här agenten lagras antingen i en befintlig Log Analytics arbets yta som är associerad med din prenumeration eller en ny arbets yta. Mer information finns i [data säkerhet](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
