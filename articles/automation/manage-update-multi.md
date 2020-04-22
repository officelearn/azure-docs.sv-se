---
title: Hantera uppdateringar för flera virtuella Azure-datorer
description: I den här artikeln beskrivs hur du hanterar uppdateringar för virtuella Azure- och icke-Azure-datorer.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 5376562d9df35539a33f6746b387a1ff7083b8f1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676449"
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Hantera uppdateringar för flera virtuella Azure-datorer

Du kan använda Azure Automation Update Management för att hantera uppdateringar och korrigeringar för dina virtuella Windows- och Linux-datorer. Från [Azure Automation](automation-offering-get-started.md)-kontot kan du:

- Publicera virtuella datorer.
- Bedöma statusen för tillgängliga uppdateringar.
- Schemalägga installation av obligatoriska uppdateringar.
- Granska distributionsresultat för att kontrollera att uppdateringar har tillämpats på alla virtuella datorer som uppdateringshantering är aktiverat för.

Mer information om systemkraven för uppdateringshantering finns i [Uppdateringshanteringsklientkrav](automation-update-management.md#clients).

## <a name="prerequisites"></a>Krav

* En virtuell dator eller en dator med ett operativsystem som stöds installerat.
* Åtkomst till en uppdateringsdatabas för virtuella Linux-datorer som finns med på uppdateringshantering.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivera uppdateringshantering för virtuella Azure-datorer

Öppna ditt Automation-konto i Azure-portalen och välj sedan **Uppdateringshantering**.

Välj **Lägg till virtuella Azure-datorer**.

![Fliken Lägg till virtuell Azure-dator](./media/manage-update-multi/update-onboard-vm.png)

Välj en virtuell dator som du vill publicera.

Under **Aktivera uppdateringshantering**väljer du **Aktivera** för att gå in på den virtuella datorn.

![Dialogrutan Aktivera hantering av uppdateringar](./media/manage-update-multi/update-enable.png)

När introduktionen är klar aktiveras uppdateringshantering för den virtuella datorn.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Aktivera uppdateringshantering för virtuella datorer och datorer som inte är Azure

Log Analytics-agenten för Windows och Linux måste installeras på de virtuella datorer som körs i företagets nätverk eller annan molnmiljö för att aktivera dem med Uppdateringshantering. Information om systemkrav och metoder som stöds för att distribuera agenten till datorer som finns utanför Azure finns [i Översikt över Log Analytics-agenten](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visa datorer som är anslutna till ditt Automation-konto

När du har aktiverat Uppdateringshantering för dina datorer kan du visa maskininformation genom att välja **Datorer**. Du kan se information om datornamn, efterlevnadsstatus, miljö, OS-typ, viktiga uppdateringar och säkerhetsuppdateringar installerade, andra installerade uppdateringar och uppdateringsagentberedskap för dina datorer.

  ![Visa fliken för datorer](./media/manage-update-multi/update-computers-tab.png)

Datorer som nyligen har aktiverats för uppdateringshantering kanske inte har utvärderats ännu. Efterlevnadstillståndet för `Not assessed`dessa datorer är . Här är en lista över möjliga värden för efterlevnadstillstånd:

- `Compliant`: Datorer som inte saknar kritiska uppdateringar eller säkerhetsuppdateringar.
- `Non-compliant`: Datorer som saknar minst en kritisk eller säkerhetsuppdatering.
- `Not assessed`: Uppdateringsutvärderingsdata har inte tagits emot från datorn inom den förväntade tidsramen. För Linux-datorer är den förväntade tidsramen den sista timmen. För Windows-datorer är den förväntade tidsramen de senaste 12 timmarna.

Om du vill visa agentens status väljer du länken i kolumnen **För beredskap för uppdateringsagenten.** Om du väljer det här alternativet öppnas fönstret Hybridarbetare och hybridarbetarens status visas. Följande bild visar ett exempel på en agent som inte har varit ansluten till Uppdateringshantering under en längre tid:

![Visa fliken för datorer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När uppdateringshantering har aktiverats öppnas fönstret Uppdateringshantering. Du kan se en lista med uppdateringar som saknas på fliken ** Uppdateringar som saknas**.

## <a name="collect-data"></a>Samla in data

Agenter som är installerade på virtuella datorer och datorer samlar in data om uppdateringar. Agenterna skickar data till Azure Update Management.

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Uppdateringshantering samlar in information om systemuppdateringar från Windows-agenter och initierar sedan installation av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Update Management samlar in information om systemuppdateringar från Linux-agenter och initierar sedan installation av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Uppdateringshantering samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp. |
| Azure Storage-konto |Inga |Azure Storage innehåller inte information om systemuppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

När en dator har slutfört en sökning efter uppdateringsefterlevnad vidarebefordrar agenten informationen i bulk till Azure Monitor-loggar. På en Windows-dator körs regelefterlevnaden var 12:e timme som standard.

Förutom skanningsschemat initieras genomsökningen efter uppdateringsefterlevnad inom 15 minuter efter att MMA har startats om, före installationen av uppdateringen och efter installationen av uppdateringen.

För en Linux-dator utförs regelefterlevnaden varje timme som standard. Om MMA-agenten startas om initieras en kompatibilitetssökning inom 15 minuter.

Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data för de datorer som hanteras.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

Om du vill installera uppdateringar schemalägger du en distribution som stämmer överens med ditt versionsschema och tjänstfönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>När du schemalägger en uppdateringsdistribution skapas en [schemaresurs](shared-resources/schedules.md) som är kopplad till **runbooken Patch-MicrosoftOMSComputers** som hanterar uppdateringsdistributionen på måldatorerna. Om du tar bort schemaresursen från Azure-portalen eller använder PowerShell efter att du har skapat distributionen, bryter den schemalagda uppdateringsdistributionen och ett fel visas när du försöker konfigurera om den från portalen. Du kan bara ta bort schemaresursen genom att ta bort motsvarande distributionsschema.
>

Om du vill schemalägga en ny uppdateringsdistribution för en eller flera virtuella datorer väljer du **Schemalägg uppdateringsdistribution**under **Uppdateringshantering**.

Ange följande information i **distributionsfönstret Ny uppdatering:**

- **Namn**: Ange ett unikt namn för att identifiera uppdateringsdistributionen.
- **Operativsystem:** Välj **Windows** eller **Linux**.
- **Grupper som ska uppdateras:** Definiera en fråga baserat på en kombination av prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp av virtuella Azure-datorer som ska inkluderas i distributionen. För virtuella datorer som inte är Azure används sparade sökningar för att skapa en dynamisk grupp som ska inkluderas i distributionen. Mer information finns i [Dynamiska grupper](automation-update-management-groups.md).
- **Maskiner som ska uppdateras:** Välj en sparad sökning, importerad grupp eller välj Datorer för att välja de datorer som du vill uppdatera.

   >[!NOTE]
   >Om du väljer alternativet Sparad sökning returneras inte maskinidentiteter, bara deras namn. Om du har flera virtuella datorer med samma namn i flera resursgrupper returneras de i resultatet. Om du vill använda alternativet **Grupper för att uppdatera** rekommenderas du för att se till att du inkluderar unika virtuella datorer som matchar dina villkor.

   Om du väljer **Maskiner**visas maskinens beredskap i kolumnen **För beredskap för uppdateringsagenten.** Du kan se datorns hälsotillstånd innan du schemalägger uppdateringsdistributionen. Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md)

  ![Distributionsfönstret för ny uppdatering](./media/manage-update-multi/update-select-computers.png)

- **Uppdateringsklassificering**: Välj vilka typer av programvara som ska inkluderas i uppdateringsdistributionen. En beskrivning av klassificeringstyperna finns i [Uppdatera klassificeringar](automation-view-update-assessments.md#update-classifications). Klassificeringstyper:
  - Kritiska uppdateringar
  - Säkerhetsuppdateringar
  - Samlade uppdateringar
  - Funktionspaket
  - Service pack
  - Definitionsuppdateringar
  - Verktyg
  - Uppdateringar

- **Uppdateringar att inkludera/exkludera** – detta öppnar sidan Inkludera/exkludera. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik. Mer information om hur inkludering hanteras finns i [Schemalägga en uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Det är viktigt att veta att undantag åsidosätter inkluderingar. Om du till exempel definierar `*`en undantagsregel för installeras inga korrigeringsfiler eller paket eftersom de alla är uteslutna. Undantagna patchar visas fortfarande som saknade från maskinen. För Linux-datorer om ett paket ingår men har ett beroende paket som uteslöts, installeras inte paketet.

> [!NOTE]
> Du kan inte ange uppdateringar som har ersatts för att inkluderas i uppdateringsdistributionen.
>

- **Schemainställningar**: Du kan godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid. Du kan också ange en annan tid.

   Du kan också ange om distributionen ska ske en gång eller enligt ett schema med återkommande tider. Om du vill ställa in ett återkommande schema väljer du **Återkommande**under **Återkommande**.

   ![Dialogrutan Schemainställningar](./media/manage-update-multi/update-set-schedule.png)

- **Skript före och efter**: Välj skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).
- **Underhållsfönster (minuter):** Ange den tidsperiod som du vill att uppdateringsdistributionen ska ske. Den här inställningen hjälper till att säkerställa att ändringarna utförs inom ditt definierade servicefönster.

- **Omstartskontroll** - Den här inställningen avgör hur omstarter hanteras för uppdateringsdistributionen.

   |Alternativ|Beskrivning|
   |---|---|
   |Starta om vid behov| **(Standard)** Om det behövs initieras en omstart om underhållsfönstret tillåter det.|
   |Starta alltid om|En omstart initieras oavsett om en krävs. |
   |Starta aldrig om|Oavsett om en omstart krävs, är omstarter undertryckta.|
   |Endast omstart – uppdateringar installeras inte|Det här alternativet ignorerar installation av uppdateringar och initierar bara en omstart.|

När du är klar med konfigurationen av schemat väljer du knappen **Skapa** för att återgå till statusinstrumentpanelen. Tabellen **Schemalagd** visar distributionsschemat som du skapade.

> [!NOTE]
> Uppdateringshantering har stöd för distribution av förstapartsuppdateringar och förnedladdning av korrigeringar. Detta kräver ändringar på de system som korrigeras, se [första part och pre download support](automation-configure-windows-update.md#pre-download-updates) för att lära dig hur du konfigurerar dessa inställningar på dina system.

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**.

Om distributionen körs är dess status **Pågår**. När distributionen har slutförts ändras statusen till **Lyckades**.

Om en eller flera uppdateringar i distributionen misslyckas visas statusen **Misslyckades delvis**.

![Status för uppdateringsdistribution](./media/manage-update-multi/update-view-results.png)

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

Fönstret Uppdatera resultat visar det totala antalet uppdateringar och distributionsresultaten för den virtuella datorn. Tabellen till höger ger en detaljerad uppdelning av varje uppdatering och installationsresultaten. Installationsresultaten kan ha något av följande värden:

- `Not attempted`: Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid baserat på det definierade underhållsfönstret.
- `Succeeded`: Uppdateringen lyckades.
- `Failed`: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Om du vill se jobbströmmen för runbooken som hanterar uppdateringsdistributionen på den virtuella måldatorn väljer du utdatapanelen.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

Mer information om uppdateringshanteringsloggar, utdata och fel finns i [Frågeuppdateringsposter för uppdateringshantering](automation-update-management-query-logs.md).
