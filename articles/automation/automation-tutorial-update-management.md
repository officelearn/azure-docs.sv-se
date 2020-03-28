---
title: Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer
description: Den här artikeln innehåller en översikt över hur du använder Azure Automation Update Management för att hantera uppdateringar och korrigeringar för dina virtuella Azure- och icke-Azure-datorer.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: f7130f3289ce42ca1481ec14be893c846c9ed55b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335793"
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

När uppdateringshantering är aktiverat visas fönstret **Uppdateringshantering**. Om några uppdateringar identifieras som saknade visas en lista över saknade uppdateringar på fliken **Saknade uppdateringar.**

Under **Informationslänk**väljer du uppdateringslänken för att öppna supportartikeln för uppdateringen. Du kan läsa viktig information om uppdateringen.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klicka någon annanstans på uppdateringen för att öppna **loggsökfönstret** för den valda uppdateringen. Frågan för loggsökningen är fördefinierad för den specifika uppdateringen. Du kan ändra den här frågan eller skapa en egen fråga om du vill visa detaljerad information om uppdateringarna som har distribuerats eller som saknas i din miljö.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurera varningar

I det här steget lär du dig hur du ställer in en avisering som meddelar dig om statusen för en uppdateringsdistribution.

### <a name="alert-conditions"></a>Aviseringsvillkor

Gå till **Aviseringar** under **Övervakning** i ditt Automation-konto och klicka sedan på **+ Ny aviseringsregel**.

Ditt Automation-konto har redan valts som resursen. Om du vill ändra den kan du klicka på **Välj** och välja **Automation-konton** i listrutan **Filtrera efter resurstyp** på sidan **Välj en resurs**. Välj ditt Automation-konto och välj **Klar**.

Klicka på **Lägg till villkor** för att välja lämplig signal för uppdateringsdistributionen. Följande tabell visar information om de två tillgängliga signalerna för uppdateringsdistributioner:

|Signalnamn|Dimensioner|Beskrivning|
|---|---|---|
|**Antal körningar av uppdateringsdistributionen**|– Namnet på uppdateringsdistributionen</br>– Status|Den här signalen används för att avisera om uppdateringsdistributionens övergripande status.|
|**Antal datorspecifika körningar av uppdateringsdistributionen**|– Namnet på uppdateringsdistributionen</br>– Status</br>– Måldator</br>- Uppdatera distributionskörnings-ID|Den här signalen används för att avisera om statusen för en uppdateringsdistribution som är riktad mot specifika datorer|

För dimensionsvärdena väljer du ett giltigt värde i listan. Om värdet du letar efter inte finns i **\+** listan klickar du på tecknet bredvid dimensionen och skriver in det anpassade namnet. Därefter kan du välja det värde som du vill söka efter. Om du vill välja alla värden från en dimension klickar du på knappen **Välj \***. Om du inte väljer ett värde för en dimension ignoreras den dimensionen under utvärderingen.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/signal-logic.png)

Under **Aviseringslogik**, för **Tröskelvärde**, ange **1**. När du är klar väljer du **Klar**.

### <a name="alert-details"></a>Aviseringsinformation

Under **2. Definiera aviseringsinformation**, ange ett namn och en beskrivning för aviseringen. Ange **allvarlighetsgrad** till **Information (allvarlighetsgrad 2)** för en lyckad körning eller **Information (allvarlighetsgrad 1)** för en misslyckad körning.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/define-alert-details.png)

Under **Åtgärdsgrupper**väljer du **Skapa nytt**. En åtgärdsgrupp är en grupp av åtgärder som kan användas i flera aviseringar. Dessa åtgärder kan inkludera, men är inte begränsade till, e-postmeddelanden, runbooks, webhooks och mycket mer. Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

I rutan **Åtgärdsgruppnamn** anger du ett namn för aviseringen och ett kortnamn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

Under **Åtgärder**anger du ett namn för åtgärden, till **exempel E-postmeddelanden**. Under **Åtgärdstyp**väljer du **E-post/SMS/Push/Voice**. Under **Information**väljer du **Redigera information**.

I rutan **e-post/SMS/Push/röst**, ange ett namn. Välj kryssrutan **e-post** och ange sedan en giltig e-postadress.

![Konfigurera en e-poståtgärdsgrupp](./media/automation-tutorial-update-management/configure-email-action-group.png)

Välj **Ok**i fönstret **E-post/SMS/Push/Voice** . Välj **Ok**i fönstret **Lägg till åtgärdsgrupp** .

Om du vill anpassa ämnet för aviseringsmeddelandet väljer du **E-postämne**under **Skapa** **regel**. När du är klar väljer du **Skapa varningsregel**. Varningen berättar när en distribution lyckas och vilka datorer som var en del av denna uppdaterade distributionskörning.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>När du schemalägger en uppdateringsdistribution skapas en [schemaresurs](shared-resources/schedules.md) som är kopplad till **runbooken Patch-MicrosoftOMSComputers** som hanterar uppdateringsdistributionen på måldatorerna. Om du tar bort schemaresursen från Azure-portalen eller använder PowerShell efter att du har skapat distributionen, bryter den schemalagda uppdateringsdistributionen och ett fel visas när du försöker konfigurera om den från portalen. Du kan bara ta bort schemaresursen genom att ta bort motsvarande distributionsschema.  
>

Schemalägg en ny uppdateringsdistribution för den virtuella datorn, gå till **Uppdateringshantering** och välj sedan **Distribution av schemauppdatering**.

Under **Ny uppdateringsdistribution** anger du följande information:

* **Namn**: Ange ett unikt namn på uppdateringsdistributionen.

* **Operativsystem**: Välj operativsystem som mål för uppdateringsdistributionen.

* **Grupper att uppdatera (förhandsversion)**: definiera en fråga som baseras på en kombination av prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i din distribution. Mer information finns i [Dynamiska grupper](automation-update-management-groups.md)

* **Datorer som ska uppdateras**: Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Maskiner**visas maskinens beredskap i kolumnen **För beredskap för uppdateringsagenten.** Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md)

* **Uppdateringsklassificering**: Välj de uppdateringsklassificeringar som stöds tillgängliga för varje produkt som kan inkluderas i uppdateringsdistributionen. Låt alla typer vara markerade för den här självstudien.

  Klassificeringstyper:

   |Operativsystem  |Typ  |
   |---------|---------|
   |Windows     | Kritiska uppdateringar</br>Säkerhetsuppdateringar</br>Samlade uppdateringar</br>Funktionspaket</br>Service pack</br>Definitionsuppdateringar</br>Verktyg</br>Uppdateringar        |
   |Linux     | Kritiska uppdateringar och säkerhetsuppdateringar</br>Övriga uppdateringar       |

   En beskrivning av klassificeringstyper finns i [uppdatera klassificeringar](automation-view-update-assessments.md#update-classifications).

* **Uppdateringar att inkludera/exkludera** – detta öppnar sidan **Inkludera/exkludera**. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik.

> [!NOTE]
> Det är viktigt att veta att undantag åsidosätter inkluderingar. Om du till exempel definierar `*`en undantagsregel för installeras inga korrigeringsfiler eller paket eftersom de alla är uteslutna. Undantagna patchar visas fortfarande som saknade från maskinen. För Linux-datorer om ett paket ingår men har ett beroende paket som uteslöts, installeras inte paketet.

> [!NOTE]
> Du kan inte ange uppdateringar som har ersatts för att inkluderas i uppdateringsdistributionen.
>

* **Schemainställningar**: Sidan **Schemainställningar** öppnas. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

   Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Under **Återkommande**, välj **En gång**. Lämna standard som 1 dag och välj **Ok**. Då ställs ett återkommande schema in.

* **Skript före och efter**: Välj skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).

* **Underhållsperiod (minuter)**: Låt standardvärdet stå kvar. Underhållsfönster styr hur lång tid det är tillåtet för uppdateringar att installera. Tänk på följande information när du anger ett underhållsfönster.

  * Underhållsfönster styr hur många uppdateringar som ska installeras.
  * Uppdateringshantering slutar inte installera nya uppdateringar om slutet av ett underhållsfönster närmar sig.
  * Uppdateringshantering avslutar inte pågående uppdateringar om när underhållsfönstret överskrids.
  * Om underhållsfönstret överskrids i Windows beror det ofta på att en Service Pack-uppdatering tar lång tid att installera.

  > [!NOTE]
  > Om du vill undvika att uppdateringar tillämpas utanför ett underhållsfönster på Ubuntu konfigurerar du om paketet Obevakad uppgradering för att inaktivera automatiska uppdateringar. Information om hur du konfigurerar paketet finns [i avsnittet Automatiska uppdateringar i Guiden För automatiska uppdateringar i guiden För automatiska uppdateringar](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Omstartsalternativ**: Den här inställningen avgör hur omstarter ska hanteras. De tillgängliga alternativen är:
  * Starta om vid behov (standard)
  * Starta alltid om
  * Starta aldrig om
  * Endast omstart – uppdateringar installeras inte

> [!NOTE]
> Registernycklarna som anges under [Registernycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka en omstartshändelse om **Omstartskontroll** är inställd **på Never Reboot**.

När du är klar med att konfigurera schemat väljer du **Skapa**.

![Inställningsfönster för uppdateringsschema](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Du kommer tillbaka till statusinstrumentpanelen. Välj **Schemalagda uppdateringsdistributioner** om du vill visa distributionsschemat du har skapat.

> [!NOTE]
> Uppdateringshantering har stöd för distribution av förstapartsuppdateringar och förnedladdning av korrigeringar. Detta kräver ändringar på de system som korrigeras. Information om hur du konfigurerar dessa inställningar på systemet finns i avsnittet om [support för förstapart och förnedladdning](automation-configure-windows-update.md).

**Uppdateringsdistributioner** kan också skapas programmässigt. Mer information om hur du skapar en **uppdateringsdistribution** med REST API finns i [Konfigurationer för programuppdatering - Skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempelkörningsbok som kan användas för att skapa en veckovis **uppdateringsdistribution**. Mer information om den här runbooken finns i [Skapa en veckovis uppdateringsdistribution för en eller flera virtuella datorer i en resursgrupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**. Statusen är **Pågår** när distributionen körs. När distributionen är klar, om den lyckas, ändras statusen till **Lyckades**. Om det uppstod några fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för en specifik distribution](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Under **Uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn. Tabellen till höger visar detaljer för varje uppdatering och installationsresultaten.

I följande lista visas tillgängliga värden:

* **Inget försök har gjorts**: Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* **Lyckades**: Uppdateringen lyckades.
* **Misslyckades**: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

När uppdateringsdistributionen av är klar skickas e-post som liknar följande exempel för att visa resultatet av distributionen:

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

