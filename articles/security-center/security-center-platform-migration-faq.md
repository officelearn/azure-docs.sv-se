---
title: Security Center-plattformen migrering vanliga frågor och svar | Microsoft Docs
description: Dessa vanliga frågor svar på frågor om Azure Security Center-plattformen migreringen.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: terrylan
ms.openlocfilehash: 6a88fbadd8fbf05a4942e42b535770f6f068af28
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="security-center-platform-migration-faq"></a>Security Center-plattformen migrering vanliga frågor och svar
I tidig juni 2017 började Azure Security Center med hjälp av Microsoft Monitoring Agent att samla in och lagra data. Läs mer i [Azure Security Center-plattformen migrering](security-center-platform-migration.md). Dessa vanliga frågor svar på frågor om migreringen plattform.

## <a name="data-collection-agents-and-workspaces"></a>Insamling av data, agenter och arbetsytor

### <a name="how-is-data-collected"></a>Hur data samlas in?
Security Center använder Microsoft Monitoring Agent för att samla in säkerhetsdata från dina virtuella datorer. Säkerhetsdata innehåller information om:

- säkerhetskonfigurationer – används för att identifiera säkerhetsrisker
- säkerhetshändelser - används för att identifiera hot

Data som samlas in av agenten lagras i en befintlig logganalys-arbetsyta som är ansluten till den virtuella datorn eller en ny arbetsyta som skapats av Security Center. När Security Center skapar en ny arbetsyta, beaktas geolokalisering av den virtuella datorn.

> [!NOTE]
> Microsoft Monitoring Agent är samma agent används av logganalys-tjänsten och System Center Operations Manager (SCOM).
>
>

När automatisk etablering (tidigare namngivits Logginsamling) är aktiverat eller när dina prenumerationer har migrerats, kontrollerar Security Center om Microsoft Monitoring Agent är installerad som en Azure-tillägget på var och en av dina virtuella datorer. Om Microsoft Monitoring Agent inte är installerat sedan som standard Security Center kommer att:

- Installera Microsoft Monitoring Agent-tillägget på den virtuella datorn.

   - Om en arbetsyta som skapats av Security Center redan finns i samma geolokalisering som den virtuella datorn, är den kopplad till den här arbetsytan.
   - Om en arbetsyta inte finns, skapar en ny resurs grupp och standard arbetsyta i den geolokalisering Security Center och ansluter agenten till arbetsytan. Namngivningskonventionen för gruppen arbetsytan och resursen är:

       Arbetsytan: DefaultWorkspace-[prenumerations-ID]-[geo]

       Resursgrupp: DefaultResouceGroup-[geo]

- Aktivera en Security Center lösning på arbetsytan per VM Tjänstadministratörens prisnivån i Security Center. Mer information om priser finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).
- Security Center för migrerade prenumerationer, tar även bort tidigare Azure Monitoring Agent.

> [!NOTE]
> Du kan åsidosätta automatisk installation av Microsoft Monitoring Agent och använder egna arbetsytan.  Se [hur du stoppar agenten för automatisk installation och arbetsytan skapa](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) och [hur du använder en befintlig arbetsyta](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

Platsen för arbetsytan baseras på platsen för den virtuella datorn. Läs mer i [datasäkerhet](security-center-data-security.md). Om en prenumeration innehåller virtuella datorer från flera geolocations, skapar flera arbetsytor med Security Center. Flera arbetsytor skapas om du vill behålla data sekretess regler.

> [!NOTE]
> Security Center samlas in säkerhetsdata från dina virtuella datorer med hjälp av Azure Monitoring Agent före plattform migreringen och data lagras i ditt lagringskonto. Efter migreringen plattform använder Security Center arbetsytan och Microsoft Monitoring Agent för att samla in och lagra samma data. Lagringskontot kan tas bort efter migreringen.  Security Center tar också bort tidigare installerade Azure övervakning agenter efter migreringen för plattformen.
>
>

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Kan jag debiteras för Log Analytics på de arbetsytor som skapats av Security Center?
Nej. Arbetsytor som skapats av Security Center, samtidigt som konfigurerats för Log Analytics per nod fakturering, inte avgifter logganalys. Security Center fakturering är alltid baserat på din säkerhetsprincip Security Center och lösningar som installerats på en arbetsyta:

- **Kostnadsfri nivå** – Security Center kan 'SecurityCenterFree' lösningen på standardarbetsytan. Du debiteras inte för den kostnadsfria nivån.
- **Standardnivån** – Security Center kan ' säkerhetslösning ' på standardarbetsytan.

Mer information om priser finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/). Prissättningssidan adresser ändringar av säkerhet för datalagring och proportionellt fördelad fakturering med början i juni 2017.

> [!NOTE]
> Logganalys prisnivån arbetsytor som skapats av Security Center påverkar inte Security Center fakturering.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Vad räknas en virtuell dator för automatisk etablering av Microsoft Monitoring Agent-installationen?
Windows- eller Linux IaaS-VM kvalificera om:

- Tillägget för Microsoft Monitoring Agent är inte installerad på den virtuella datorn.
- Den virtuella datorn är i körningstillstånd.
- I Windows eller Linux VM-agenten är installerad.
- Den virtuella datorn används inte som en enhet, till exempel Brandvägg för webbaserade program eller nästa generations brandvägg.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan jag ta bort Standardarbetsytor som skapats av Security Center?
**Det rekommenderas inte att ta bort standardarbetsytan.** Security Center använder Standardarbetsytor för att lagra säkerhetsdata från dina virtuella datorer.  Om du tar bort en arbetsyta Security Center är det gick inte att samla in dessa data och vissa säkerhetsrekommendationer och aviseringar är inte tillgänglig.

Ansluten till arbetsytan tagits bort för att återställa eller ta bort Microsoft Monitoring Agent på virtuella datorer. Security Center installerar om agenten och skapar nya Standardarbetsytor.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hur kan jag använda min befintliga logganalys-arbetsytan?

Du kan välja en befintlig logganalys-arbetsyta för att lagra data som samlas in av Security Center. Använda din befintliga logganalys-arbetsyta:

- Arbetsytan måste vara kopplad till din valda Azure-prenumeration.
- Åtminstone, måste du ha läsbehörighet till arbetsytan.

Att välja en befintlig logganalys-arbetsyta:

1. Under **säkerhetsprincip – datainsamling**väljer **använder en annan arbetsyta**.

   ![Använd en annan arbetsyta][5]

2. Välj en arbetsyta för att spara insamlade data i den nedrullningsbara menyn.

   > [!NOTE]
   > I nedrullningsbara menyn visas arbetsytor som du har åtkomst till och finns i din Azure-prenumeration.
   >
   >

3. Välj **Spara**.
4. När du har valt **spara**, tillfrågas du om du vill konfigurera om övervakas virtuella datorer.

   - Välj **nr** om du vill att de nya arbetsyteinställningarna för **tillämpa på nya virtuella datorer bara**. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer; Nyligen identifierade virtuella datorer som inte har Microsoft Monitoring Agent installerad.
   - Välj **Ja** om du vill att de nya arbetsyteinställningarna för **tillämpas på alla virtuella datorer**. Dessutom återansluta var ansluten till en Security Center skapade arbetsytan VM till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja kan du inte ta bort arbetsytor som skapats av Security Center tills alla virtuella datorer har återanslutit till målarbetsytan. Den här åtgärden misslyckas om en arbetsyta tas bort för tidigt.
   >
   >

   - Välj **Avbryt** avbryta åtgärden.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Vad händer om Microsoft Monitoring Agent har redan installerats som ett tillägg på den virtuella datorn?
Security Center åsidosätts inte befintliga anslutningar till användaren arbetsytor. Security Center lagrar säkerhetsdata från den virtuella datorn i arbetsytan redan ansluten. Security Center uppdateras tilläggsversionen om du vill inkludera Azure resurs-ID för den virtuella datorn för att stödja användning av Security Center.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Vad händer om jag hade en Microsoft Monitoring Agent installerad på datorn men inte som ett tillägg?
Om Microsoft Monitoring Agent installeras direkt på den virtuella datorn (inte som en Azure-tillägget), Security Center installera inte Microsoft Monitoring Agent och säkerhetsövervakning är begränsad.

Mer information finns i nästa avsnitt [vad händer om en SCOM eller OMS direkt agenten redan är installerad på den virtuella datorn?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>Vad händer om en SCOM eller OMS styr agent är installerad på den virtuella datorn?
Security Center kan inte identifiera i förväg att en agent har installerats.  Security Center försöker installera tillägg för Microsoft Monitoring Agent och misslyckas på grund av den befintliga installerade agenten.  Det här felet förhindrar åsidosätter agenten anslutningsinställningar till arbetsytan och undviker att skapa multihoming.

> [!NOTE]
> Agentversionen har uppdaterats till den senaste versionen av OMS-agenten.  Detta gäller för SCOM användare också.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Vad är effekt vid borttagning av dessa tillägg?
Om du tar bort tillägget för övervakning av Microsoft Security Center är inte kunna samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och aviseringar är inte tillgänglig. Security Center anger att den virtuella datorn saknar tillägget och installerar om tillägget inom 24 timmar.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hur förhindrar att agenten för automatisk installation och arbetsytan skapa?
Du kan inaktivera automatisk etablering för dina prenumerationer i säkerhetsprincipen men detta rekommenderas inte. Inaktivera automatisk etablering gränser Security Center-rekommendationerna och aviseringar. Automatisk etablering krävs för prenumerationer på standarden prisnivån. Inaktivera automatisk etablering:

1. Om din prenumeration har konfigurerats för standardnivån, öppna säkerhetsprincip för den prenumerationen och väljer den **lediga** nivå.

   ![Prisnivå][1]

2. Därefter inaktivera automatisk etablering genom att välja **av** på den **säkerhetsprincip – datainsamling** bladet.
   ![Datainsamling][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Ska jag välja bort agenten för automatisk installation och skapa arbetsyta?

> [!NOTE]
> Se till att granska avsnitt [vad är effekterna av att välja?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) och [rekommenderas steg när du väljer](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) om du vill välja att inaktivera automatisk etablering.
>
>

Du kanske vill välja att inaktivera automatisk etablering om följande gäller för dig:

- Agenten för automatisk installation av Security Center gäller för hela prenumerationen.  Du kan inte använda automatisk installation till en delmängd av virtuella datorer. Om det finns viktiga virtuella datorer som inte kan installeras med Microsoft Monitoring Agent, bör du välja Automatisk etablering out-of.
- Installationen av Microsoft Monitoring Agent-tillägget uppdateras agentens version. Detta gäller för en direkt agent och en SCOM-agent. Om den installerade SCOM-agenten är version 2012 och uppgraderas, kan hanterbarhet funktioner gå förlorade när SCOM serverversionen är också 2012. Bör du väljer bort automatisk etablering om installerade SCOM-agent är version 2012.
- Om du har en anpassad arbetsyta externa till prenumerationen (centraliserade arbetsytor) bör du välja bort automatisk etablering. Du kan manuellt installera tillägg för Microsoft Monitoring Agent och ansluta den arbetsytan utan Security Center åsidosätter anslutningen.
- Om du vill undvika att skapa flera arbetsytor per prenumeration och du har en egen anpassad arbetsyta i prenumerationen, har du två alternativ:

   1. Du kan välja bort automatisk etablering. Efter migreringen standardvärdet arbetsyteinställningarna enligt beskrivningen i [hur kan jag använda min befintliga logganalys-arbetsytan?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Eller, kan du tillåta migrering för att slutföra Microsoft Monitoring Agent installeras på de virtuella datorerna och de virtuella datorerna som är ansluten till arbetsytan skapas. Markera din egen anpassade arbetsytan genom att ange standardinställningen för arbetsytan med valde omkonfigurera redan installerade agenter. Mer information finns i [hur kan jag använda min befintliga logganalys-arbetsytan?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Vilka är effekterna av väljer bort automatisk etablering?
När migreringen är klar, Security Center är inte kunna samla in säkerhetsdata från den virtuella datorn och vissa säkerhetsrekommendationer och aviseringar är inte tillgänglig. Om du avböjer bör du installera Microsoft Monitoring Agent manuellt. Se [rekommenderas steg när du väljer](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Vilka är rekommenderade steg när du väljer bort automatisk etablering?
Du bör installera Microsoft Monitoring Agent manuellt så Security Center kan samla in säkerhetsdata från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar. Se [ansluta Windows-datorer i Azure logganalys-tjänsten](../log-analytics/log-analytics-windows-agent.md) vägledning om installationen.

Du kan ansluta agenten till en befintlig anpassad arbetsyta eller Security Center skapa arbetsytan. Om en anpassad arbetsyta saknar 'Säkerhet' eller 'SecurityCenterFree' lösningar som aktiverad, måste gälla en lösning. Om du vill använda, väljer du anpassade arbetsytan eller en prenumeration och tillämpa en prisnivå via den **säkerhetsprincip – prisnivå** bladet.

   ![Prisnivå][1]

Security Center aktiveras den rätta lösningen på arbetsytan baserat på den valda prisnivån.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hur tar jag bort OMS-tillägg installeras av Security Center?
Du kan manuellt ta bort Microsoft Monitoring Agent. Detta rekommenderas inte eftersom det begränsar Security Center rekommendationer och aviseringar.

> [!NOTE]
> Om datainsamling har aktiverats Security Center kommer installera om agenten när du tar bort den.  Du måste inaktivera datainsamling innan du tar bort agenten manuellt. Se [hur stoppa agenten för automatisk installation och arbetsyta skapas?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) anvisningar om hur du inaktiverar datainsamling.
>
>

Ta bort agenten manuellt:

1.  Öppna i portalen, **logganalys**.
2.  Välj en arbetsyta i bladet logganalys:
3.  Markera varje virtuell dator som du inte vill övervaka och välj **frånkoppling**.

   ![Ta bort agenten][3]

> [!NOTE]
> Om en Linux VM redan har en icke-tillägget OMS-agent, tar bort tillägget tas bort agenten samt och kunden har installera det på nytt.
>
>

## <a name="existing-log-analytics-customers"></a>Befintliga logganalys-kunder

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center åsidosätta alla befintliga anslutningar mellan virtuella datorer och arbetsytor?
Om en virtuell dator har redan Microsoft Monitoring Agent installeras som en Azure-tillägget, åsidosätter inte den befintliga anslutningen i arbetsytan i Security Center. Security Center används i stället befintlig arbetsyta.

En lösning för Security Center är installerat på arbetsytan om det inte finns redan och lösningen tillämpas endast på de relevanta virtuella datorerna. När du lägger till en lösning distribueras den automatiskt som standard till alla Windows- och Linux-agenter ansluten till logganalys-arbetsytan. [Lösning målinriktning](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du använda ett scope till dina lösningar.

Om Microsoft Monitoring Agent installeras direkt på den virtuella datorn (inte som en Azure-tillägget), Security Center installera inte Microsoft Monitoring Agent och säkerhetsövervakning är begränsad.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Vad gör jag om jag misstänker att migrering av data platform bröt mot anslutningen mellan en av Mina virtuella datorer och Min arbetsyta?
Det borde inte ske. Om det sker, sedan [skapa en supportförfrågan för Azure](../azure-supportability/how-to-create-azure-support-request.md) och innehåller följande information:

- Azure-resurs-ID för påverkas VM
- Azure-resurs-ID för arbetsytan som konfigurerats på tillägget innan anslutningen har brutits
- Agenten och version som tidigare har installerats

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Installeras Security Center lösningar på Mina befintliga logganalys arbetsytor? Vad är fakturering effekterna?
När Security Center identifierar att en virtuell dator är redan ansluten till en arbetsyta som du skapade, kan Security Center lösningar på den här arbetsytan enligt prisnivå. Lösningarna som endast används för den relevanta virtuella Azure-datorer [lösning riktad](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), så faktureringen är densamma.

- **Kostnadsfri nivå** – Security Center installerar 'SecurityCenterFree' lösningen på arbetsytan. Du debiteras inte för den kostnadsfria nivån.
- **Standardnivån** – Security Center installerar säkerhetslösningen på arbetsytan.

   ![Lösningar i standardarbetsytan][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Jag har redan arbetsytor i Min miljö, kan jag använda dem för att samla in säkerhetsdata om?
Om en virtuell dator har redan Microsoft Monitoring Agent installeras som en Azure-tillägget, använder befintliga anslutna arbetsytan i Security Center. En lösning för Security Center är installerat på arbetsytan om det inte finns redan och lösningen tillämpas endast på de relevanta virtuella datorerna via [lösning riktad](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

När Security Center installerar Microsoft Monitoring Agent på virtuella datorer, använder standard-arbetsytor som skapats av Security Center. Kunder kommer snart att kunna konfigurera vilka arbetsytor används.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Jag har redan säkerhetslösning på Mina arbetsytor. Vad är fakturering effekterna?
Säkerhets- och granska lösningen används för att aktivera Security Center Standard nivå funktioner för virtuella Azure-datorer. Om säkerhets- och granska lösningen är redan installerad på en arbetsyta, använder den befintliga lösningen Security Center. Det finns ingen ändring i fakturering.

## <a name="next-steps"></a>Nästa steg
Mer information om Security Center-plattformen migrering finns

- [Azure Security Center-plattformen migrering](security-center-platform-migration.md)
- [Azure Security Center Troubleshooting Guide](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
