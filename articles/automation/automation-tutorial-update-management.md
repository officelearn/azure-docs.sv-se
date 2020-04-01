---
title: Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer
description: Den här artikeln innehåller en översikt över hur du använder Azure Automation Update Management för att hantera uppdateringar och korrigeringar för dina virtuella Azure- och icke-Azure-datorer.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: b5b03efafbed3439e5f8fdf98e2cd5b2d63a3d45
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411048"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer

Du kan använda uppdateringshanteringen för att hantera uppdateringar och korrigeringar av virtuella datorer. I den här självstudien får du lära dig hur du utvärderar statusen för tillgängliga uppdateringar snabbt, planerar installation av uppdateringar som krävs, granskar distributionsresultat och skapar en avisering för att verifiera uppdateringar.

Prisinformation finns i [Automatiseringspriser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Visa en uppdateringsbedömning
> * Konfigurera aviseringar
> * Schemalägga en uppdateringsdistribution
> * Visa resultatet av en distribution

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Lösningen [för uppdateringshantering](automation-update-management.md) aktiverad för en eller flera av dina virtuella datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När du har aktiverat Uppdateringshantering öppnas sidan **Uppdateringshantering.** Om några uppdateringar identifieras som saknade visas en lista över saknade uppdateringar på fliken **Saknade uppdateringar.**

Under **Informationslänk**väljer du uppdateringslänken för att öppna supportartikeln för uppdateringen. Du kan läsa viktig information om uppdateringen.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klicka någon annanstans på uppdateringen för att öppna **loggsökfönstret** för den valda uppdateringen. Frågan för loggsökningen är fördefinierad för den specifika uppdateringen. Du kan ändra den här frågan eller skapa en egen fråga för att visa detaljerad information om uppdateringar som distribuerats eller saknas i din miljö.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurera varningar

I det här steget lär du dig hur du ställer in en avisering som meddelar dig om statusen för en uppdateringsdistribution.

### <a name="alert-conditions"></a>Aviseringsvillkor

Gå till **Aviseringar** under **Övervakning**i ditt Automation-konto och klicka sedan på **Ny varningsregel**.

Ditt Automation-konto har redan valts som resurs. Om du vill ändra den klickar du på **Markera**. På sidan **Välj en resurs** väljer du **Automation-konton** på den **nedrullningsbara menyn Filter efter resurstyp.** Välj ditt Automation-konto och klicka sedan på **Klar**.

Klicka på **Lägg till villkor** för att välja lämplig signal för uppdateringsdistributionen. I följande tabell visas information om de två tillgängliga signalerna.

|Signalnamn|Dimensioner|Beskrivning|
|---|---|---|
|`Total Update Deployment Runs`|– Namnet på uppdateringsdistributionen<br>– Status|Den här signalen varnar för den övergripande statusen för en uppdateringsdistribution.|
|`Total Update Deployment Machine Runs`|– Namnet på uppdateringsdistributionen</br>– Status</br>– Måldator</br>- Uppdatera distributionskörnings-ID|Den här signalen varnar för status för en uppdateringsdistribution som är inriktad på specifika datorer.|

Välj ett giltigt värde i listan för en dimension. Om värdet du vill använda inte finns **\+** i listan klickar du på tecknet bredvid dimensionen och skriver in det anpassade namnet. Välj sedan det värde som du vill söka efter. Om du vill markera alla värden för en dimension klickar du på knappen **Välj. \* ** Om du inte väljer ett värde för en dimension ignoreras dimensionen i Uppdateringshantering.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/signal-logic.png)

Under **Aviseringslogik**, för **Tröskelvärde**, ange **1**. När du är klar väljer du **Klar**.

### <a name="alert-details"></a>Aviseringsinformation

Under **2. Definiera aviseringsinformation**, ange ett namn och en beskrivning för aviseringen. Ange **allvarlighetsgrad** till **Information (allvarlighetsgrad 2)** för en lyckad körning eller **Information (allvarlighetsgrad 1)** för en misslyckad körning.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/define-alert-details.png)

Under **Åtgärdsgrupper**väljer du **Skapa nytt**. En åtgärdsgrupp är en grupp av åtgärder som kan användas i flera aviseringar. Åtgärderna kan omfatta e-postmeddelanden, runbooks, webhooks och många fler. Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

Ange ett namn på aviseringen och ett kort namn i fältet **Åtgärdsgruppnamn.** Uppdateringshantering använder kortnamnet i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den angivna gruppen.

Under **Åtgärder**anger du ett namn för åtgärden, till **exempel E-postmeddelande**. För **åtgärdstyp**väljer du **E-post/SMS/Push/Voice**. För **mer information**väljer du Redigera **information**.

I rutan **e-post/SMS/Push/röst**, ange ett namn. Välj kryssrutan **e-post** och ange sedan en giltig e-postadress.

![Konfigurera en e-poståtgärdsgrupp](./media/automation-tutorial-update-management/configure-email-action-group.png)

Klicka på **OK**i fönstret **E-post/SMS/Push/Voice** . Klicka på **OK**i fönstret **Lägg till åtgärdsgrupp** .

Om du vill anpassa ämnet för aviseringsmeddelandet väljer du **E-postämne**under **Skapa** **regel**. När du är klar väljer du **Skapa varningsregel**. Aviseringen talar om när en uppdateringsdistribution lyckas och vilka datorer som ingick i uppdateringsdistributionskörningen.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdateringstyper som ska inkluderas i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>Om du schemalägger en uppdateringsdistribution skapas en [schemaresurs](shared-resources/schedules.md) som är kopplad till **runbooken Patch-MicrosoftOMSComputers** som hanterar uppdateringsdistributionen på måldatorerna. Om du tar bort schemaresursen från Azure-portalen eller använder PowerShell efter att distributionen har skapats, bryter borttagningen den schemalagda uppdateringsdistributionen och visar ett fel när du försöker konfigurera om schemaresursen från portalen. Du kan bara ta bort schemaresursen genom att ta bort motsvarande distributionsschema.  

Schemalägg en ny uppdateringsdistribution för den virtuella datorn, gå till **Uppdateringshantering** och välj sedan **Distribution av schemauppdatering**.

Under **Ny uppdateringsdistribution** anger du följande information:

* **Namn**: Ange ett unikt namn på uppdateringsdistributionen.

* **Operativsystem**: Välj operativsystem som mål för uppdateringsdistributionen.

* **Grupper att uppdatera (förhandsgranska):** Definiera en fråga som kombinerar prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp av virtuella Azure-datorer som ska inkluderas i distributionen. Mer information finns i [Dynamiska grupper](automation-update-management-groups.md).

* **Maskiner som ska uppdateras:** Välj en sparad sökning, importerad grupp eller välj **maskiner** på rullgardinsmenyn och välj enskilda datorer. Om du väljer **Datorer**visas beredskapen för varje dator i kolumnen **För beredskap för uppdateringsagenten.** Mer information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns [i Datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md).

* **Uppdateringsklassificering**: För varje produkt avmarkerar du alla uppdateringsklassificeringar som stöds, men de som ska inkluderas i uppdateringsdistributionen. För den här självstudien lämnar du alla typer valda för alla produkter.

  Klassificeringstyper:

   |Operativsystem  |Typ  |
   |---------|---------|
   |Windows     | Kritiska uppdateringar</br>Säkerhetsuppdateringar</br>Samlade uppdateringar</br>Funktionspaket</br>Service pack</br>Definitionsuppdateringar</br>Verktyg</br>Uppdateringar<br>Drivrutin        |
   |Linux     | Kritiska uppdateringar och säkerhetsuppdateringar</br>Övriga uppdateringar       |

   Beskrivningar av klassificeringstyperna finns i [Uppdatera klassificeringar](automation-view-update-assessments.md#update-classifications).

* **Uppdateringar för att inkludera/utesluta** - Öppnar sidan Inkludera/Uteslut. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik.

> [!NOTE]
> Det är viktigt att veta att undantag åsidosätter inkluderingar. Om du till exempel definierar `*`en undantagsregel för installerar Uppdateringshantering inga korrigeringar eller paket, eftersom de alla är undantagna. Undantagna patchar visas fortfarande som saknade från maskinen. Om du inkluderar ett paket som har ett beroende paket som har uteslutits för Linux-datorer installeras inte huvudpaketet.

> [!NOTE]
> Du kan inte ange uppdateringar som har ersatts för att inkluderas i uppdateringsdistributionen.
>

* **Schemainställningar**: Sidan **Schemainställningar** öppnas. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

   Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Under **Återkommande**, välj **En gång**. Lämna standard som 1 dag och klicka på **OK**. Dessa transaktioner ställer in ett återkommande schema.

* **Skript före och efter**: Välj skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).

* **Underhållsperiod (minuter)**: Låt standardvärdet stå kvar. Underhållsfönster styr hur lång tid det är tillåtet för uppdateringar att installera. Tänk på följande information när du anger ett underhållsfönster:

  * Underhållsfönster styr hur många uppdateringar som är installerade.
  * Uppdateringshantering slutar inte installera nya uppdateringar om slutet av ett underhållsfönster närmar sig.
  * Uppdateringshantering avslutar inte pågående uppdateringar om underhållsfönstret överskrids.
  * Om underhållsfönstret överskrids i Windows beror det ofta på att det tar lång tid att installera en Service Pack-uppdatering.

  > [!NOTE]
  > Om du vill undvika att uppdateringar tillämpas utanför ett underhållsfönster på Ubuntu konfigurerar du om paketet Obevakad uppgradering för att inaktivera automatiska uppdateringar. Information om hur du konfigurerar paketet finns [i avsnittet Automatiska uppdateringar i Guiden För automatiska uppdateringar i guiden För automatiska uppdateringar](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Omstartsalternativ**: Används för att ange alternativ för hantering av omstarter. Följande alternativ är tillgängliga:
  * Starta om vid behov (standard)
  * Starta alltid om
  * Starta aldrig om
  * Starta bara om - installerar inte uppdateringar

> [!NOTE]
> Registernycklarna som anges under [Registernycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka en omstart händelse om Omstart **alternativ** är inställd på Aldrig **starta om**.

När du är klar med konfigurationen av schemat klickar du på **Skapa**.

![Inställningsfönster för uppdateringsschema](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Du kommer tillbaka till statusinstrumentpanelen. Välj **Schemalagda uppdateringsdistributioner** för att visa det distributionsschema som just har skapats.

> [!NOTE]
> Uppdateringshantering stöder distribution av uppdateringar från första part och korrigeringsfiler före nedladdning. Det här stödet kräver ändringar i de system som korrigeras. Se [Support för första part och förhandshämtning för](automation-configure-windows-update.md) att lära dig hur du konfigurerar dessa inställningar på dina system.

Du kan också skapa uppdateringsdistributioner programmässigt. Mer information om hur du skapar en uppdateringsdistribution med REST API finns i [Konfigurationer för programuppdatering - Skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempelkörningsbok som du kan använda för att skapa en veckovis uppdateringsdistribution. Mer information om den här runbooken finns i [Skapa en veckovis uppdateringsdistribution för en eller flera virtuella datorer i en resursgrupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**. Statusen är **Pågår** när distributionen körs. När distributionen har slutförts ändras statusen till **Lyckades**. Om det finns fel med en eller flera uppdateringar i distributionen, är **statusen Delvis misslyckad**.

Välj den slutförda uppdateringsdistributionen för att se dess instrumentpanel.

![Statusinstrumentpanel för Uppdatera distribution för en specifik distribution](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Under **Uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn. Tabellen till höger visar detaljer för varje uppdatering och installationsresultaten.

De tillgängliga värdena är:

* **Inte försök:** Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid tillgänglig, baserat på underhållsfönstrets varaktighet som definierats.
* **Lyckades**: Uppdateringen lyckades.
* **Misslyckades**: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill visa alla loggposter som distributionen har skapat.

Välj **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

När uppdateringsdistributionen lyckas får du ett bekräftande e-postmeddelande som liknar följande:

![Konfigurera e-poståtgärdsgrupp](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för hantering av uppdateringar
> * Visa en uppdateringsbedömning
> * Konfigurera aviseringar
> * Schemalägga en uppdateringsdistribution
> * Visa resultatet av en distribution

Fortsätt till översikten för uppdateringshanteringslösningen.

> [!div class="nextstepaction"]
> [Uppdateringshanteringslösning](automation-update-management.md)