---
title: Hantera uppdateringar för flera virtuella datorer i Azure Automation
description: Den här artikeln beskriver hur du hanterar uppdateringar för flera virtuella datorer.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 864b6793f65c69c83c0e26d01a10e156b1094889
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741028"
---
# <a name="manage-updates-for-multiple-vms"></a>Hantera uppdateringar för flera virtuella datorer

Du kan använda Azure Automation Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella Windows-och Linux-datorer. Från [Azure Automation](automation-offering-get-started.md)-kontot kan du:

- Aktivera virtuella datorer för uppdaterings hantering.
- Bedöma statusen för tillgängliga uppdateringar.
- Schemalägga installation av obligatoriska uppdateringar.
- Granska distributions resultaten för att kontrol lera att uppdateringarna har tillämpats på alla virtuella datorer som Uppdateringshantering har Aktiver ATS för.

Mer information om system kraven för Uppdateringshantering finns [uppdateringshantering klient krav](automation-update-management.md#client-requirements).

## <a name="prerequisites"></a>Krav

* En virtuell dator eller en dator med ett operativ system som stöds installerat.
* Åtkomst till ett uppdaterings lager för virtuella Linux-datorer som är aktiverade för Uppdateringshantering.

## <a name="enable-update-management-for-azure-vms"></a>Aktivera Uppdateringshantering för virtuella Azure-datorer

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **uppdaterings hantering**.

2. Välj **Lägg till virtuella Azure-datorer**.

    ![Fliken Lägg till virtuell Azure-dator](./media/manage-update-multi/update-onboard-vm.png)

3. Välj en virtuell dator som du vill aktivera och välj **Aktivera** under **Aktivera uppdateringshantering**.

    ![Dialogrutan Aktivera hantering av uppdateringar](./media/manage-update-multi/update-enable.png)

    När åtgärden har slutförts aktive ras Uppdateringshantering på den virtuella datorn.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Aktivera Uppdateringshantering för icke-virtuella datorer och datorer som inte är Azure-datorer

Log Analytics-agenten för Windows och Linux måste installeras på de virtuella datorer som körs i företags nätverket eller i en annan moln miljö för att du ska kunna göra det med Uppdateringshantering. Mer information om system kraven och de metoder som stöds för att distribuera agenten till datorer som ligger utanför Azure finns i [Översikt över Log Analytics agenten](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visa datorer som är anslutna till ditt Automation-konto

När du har aktiverat Uppdateringshantering för dina datorer kan du Visa dator information genom att välja **datorer**. Du kan se information om dator namn, kompatibilitetsstatus, miljö, OS-typ, viktiga uppdateringar och säkerhets uppdateringar installerade, andra uppdateringar som är installerade och för att uppdatera agent beredskap för dina datorer.

  ![Visa fliken för datorer](./media/manage-update-multi/update-computers-tab.png)

Datorer som nyligen har Aktiver ATS för Uppdateringshantering kanske inte har utvärderats ännu. Kompatibilitetstillstånd för dessa datorer är `Not assessed` . Här är en lista över möjliga värden för kompatibilitetstillstånd:

- `Compliant`: Datorer som inte saknar kritiska uppdateringar eller säkerhets uppdateringar.
- `Non-compliant`: Datorer som saknar minst en kritisk eller säkerhets uppdatering.
- `Not assessed`: Uppdaterings utvärderings data har inte tagits emot från datorn inom den förväntade tids ramen. För Linux-datorer är den förväntade tidsram den senaste timmen. För Windows-datorer är den förväntade tids ramen de senaste 12 timmarna.

Om du vill visa agentens status väljer du länken i kolumnen **Uppdatera agent beredskap** . Om du väljer det här alternativet öppnas fönstret Hybrid Worker och status för Hybrid Worker visas. Följande bild visar ett exempel på en agent som inte har anslutits till Uppdateringshantering under en längre tid:

![Visa fliken för datorer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När Uppdateringshantering har Aktiver ATS öppnas fönstret Uppdateringshantering. Du kan se en lista med uppdateringar som saknas på fliken ** Uppdateringar som saknas**.

## <a name="collect-data"></a>Samla in data

Agenter som är installerade på virtuella datorer och datorer samlar in data om uppdateringar. Agenterna skickar data till Azure Uppdateringshantering.

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som Uppdateringshantering stöder:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Uppdateringshantering samlar in information om system uppdateringar från Windows-agenter och initierar sedan installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Uppdateringshantering samlar in information om system uppdateringar från Linux-agenter och initierar sedan installationen av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Uppdateringshantering samlar in information om system uppdateringar från agenter i en ansluten hanterings grupp. |
| Azure-lagringskonto |Nej |Azure Storage innehåller inte information om system uppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

När en dator har slutfört en sökning efter uppdateringens efterlevnad vidarebefordrar agenten informationen i bulk till Azure Monitor loggar. På en Windows-dator körs kompatibilitetskontroll var 12: e timme som standard.

Förutom genomsöknings schemat initieras genomsökningen av kompatibiliteten inom 15 minuter från den MMA som startas om, innan installationen av uppdateringen och efter installationen av uppdateringen.

För en Linux-dator utförs genomsökningen varje timme som standard. Om MMA-agenten startas om initieras en kompatibilitetskontroll inom 15 minuter.

Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data för de datorer som hanteras.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

Om du vill installera uppdateringar schemalägger du en distribution som överensstämmer med ditt versions schema och service fönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>När du schemalägger en uppdaterings distribution skapar den en [schema](shared-resources/schedules.md) resurs som är länkad till **MicrosoftOMSComputers-** runbooken som hanterar uppdaterings distributionen på mål datorerna. Om du tar bort schema resursen från Azure Portal eller använder PowerShell när du har skapat distributionen bryts den schemalagda uppdaterings distributionen och ett fel uppstår när du försöker konfigurera om den från portalen. Du kan bara ta bort schema resursen genom att ta bort motsvarande distributions schema.
>

Om du vill schemalägga en ny uppdaterings distribution för en eller flera virtuella datorer väljer du **Schemalägg uppdaterings distribution**under **uppdaterings hantering**.

I fönstret **ny uppdaterings distribution** anger du följande information:

- **Namn**: Ange ett unikt namn som identifierar uppdaterings distributionen.
- **Operativ system**: Välj **Windows** eller **Linux**.
- **Grupper att uppdatera**: definiera en fråga baserat på en kombination av prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. För virtuella datorer som inte är Azure-datorer används sparade sökningar för att skapa en dynamisk grupp som ska inkluderas i distributionen. Mer information finns i [dynamiska grupper](automation-update-management-groups.md).
- **Datorer som ska uppdateras**: Välj en sparad sökning, importerad grupp eller Välj datorer för att välja de datorer som du vill uppdatera.

   >[!NOTE]
   >Att välja det sparade sökalternativet returnerar inte dator identiteter, bara deras namn. Om du har flera virtuella datorer med samma namn i flera resurs grupper returneras de i resultaten. Du rekommenderas att använda alternativet **grupper för uppdatering** för att se till att du inkluderar unika virtuella datorer som matchar dina kriterier.

   Om du väljer **datorer**visas datorns beredskap i kolumnen **Uppdatera agent beredskap** . Du kan se hälso tillståndet för datorn innan du schemalägger uppdaterings distributionen. Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md)

  ![Nytt distributions fönster för uppdatering](./media/manage-update-multi/update-select-computers.png)

- **Uppdaterings klassificering**: Välj vilka typer av program vara som ska ingå i uppdaterings distributionen. En beskrivning av klassificerings typerna finns i [uppdaterings klassificeringar](automation-view-update-assessments.md#work-with-update-classifications). Klassificeringstyper:
  - Kritiska uppdateringar
  - Säkerhetsuppdateringar
  - Samlade uppdateringar
  - Funktionspaket
  - Service Pack
  - Definitionsuppdateringar
  - Verktyg
  - Uppdateringar

- **Uppdateringar att inkludera/exkludera** – detta öppnar sidan Inkludera/exkludera. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik. Mer information om hur inkludering hanteras finns i [Schemalägga en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Det är viktigt att veta att undantagen åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*` , installeras inga korrigeringar eller paket eftersom de undantas. Undantagna uppdateringar visas fortfarande som saknas på datorn. För Linux-datorer om ett paket ingår men har ett beroende paket som uteslutits, installeras inte paketet.

> [!NOTE]
> Du kan inte ange uppdateringar som har ersatts för inkludering med uppdaterings distributionen.

- **Schemainställningar**: Du kan godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid. Du kan också ange en annan tid.

   Du kan också ange om distributionen ska ske en gång eller enligt ett schema med återkommande tider. Om du vill ställa in ett återkommande schema väljer du **återkommande**under **upprepning**.

   ![Dialogrutan Schemainställningar](./media/manage-update-multi/update-set-schedule.png)

- **Skript före och efter**: Välj skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).
- **Underhålls period (minuter)**: Ange den tids period som du vill att uppdaterings distributionen ska ske. Den här inställningen hjälper till att säkerställa att ändringarna utförs inom ditt definierade servicefönster.

- **Starta om kontroll** – den här inställningen avgör hur omstarter hanteras för uppdaterings distributionen.

   |Alternativ|Beskrivning|
   |---|---|
   |Starta om vid behov| **(Standard)** Vid behov initieras en omstart om underhålls perioden tillåter.|
   |Starta alltid om|En omstart startas oavsett om det krävs en sådan. |
   |Starta aldrig om|Oavsett om en omstart krävs ignoreras omstarter.|
   |Endast omstart – uppdateringar installeras inte|Det här alternativet ignorerar installationen av uppdateringar och startar bara en omstart.|

När du är klar med konfigurationen av schemat väljer du knappen **skapa** för att återgå till status instrument panelen. Den **schemalagda** tabellen visar det distributions schema som du har skapat.

> [!NOTE]
> Uppdateringshantering har stöd för distribution av förstapartsuppdateringar och förnedladdning av korrigeringar. Detta kräver att ändringar av systemen som korrigeras finns i [första part och för hämtnings support](automation-configure-windows-update.md#pre-download-updates) för att lära dig hur du konfigurerar dessa inställningar på dina system.

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**.

Om distributionen körs är dess status **Pågår**. När distributionen har slutförts ändras statusen till **slutförd**.

Om en eller flera uppdateringar i distributionen misslyckas visas statusen **Misslyckades delvis**.

![Status för uppdateringsdistribution](./media/manage-update-multi/update-view-results.png)

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

I fönstret uppdaterings resultat visas det totala antalet uppdateringar och distributions resultat för den virtuella datorn. Tabellen till höger ger en detaljerad analys av varje uppdatering och installations resultaten. Installationsresultaten kan ha något av följande värden:

- `Not attempted`: Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid baserat på den definierade underhålls perioden.
- `Succeeded`: Uppdateringen har slutförts.
- `Failed`: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen utdata om du vill se jobb strömmen för den Runbook som hanterar uppdaterings distributionen på den virtuella mål datorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

[Fråga Uppdateringshanteringsloggar](automation-update-management-query-logs.md)
