---
title: Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer
description: Den här artikeln innehåller en översikt över hur du använder Azure Automation Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella Azure-och icke-Azure-datorer.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 62c661f75aef77117a61be7e802562e6dde17ba5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604682"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer

Du kan använda uppdateringshanteringen för att hantera uppdateringar och korrigeringar av virtuella datorer. I den här självstudien får du lära dig hur du utvärderar statusen för tillgängliga uppdateringar snabbt, planerar installation av uppdateringar som krävs, granskar distributionsresultat och skapar en avisering för att verifiera uppdateringar.

Pris information finns i avsnittet [om Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa en uppdateringsbedömning
> * Konfigurera aviseringar
> * Schemalägga en uppdateringsdistribution
> * Visa resultatet av en distribution

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Uppdateringshantering](automation-update-management.md) lösning aktive rad för en eller flera av dina virtuella datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När du har aktiverat Uppdateringshantering öppnas sidan hantering av uppdateringar. Om några uppdateringar identifieras som saknade visas en lista med uppdateringar som saknas på fliken uppdateringar som **saknas** .

Under **informations länk**väljer du länken Uppdatera för att öppna Support artikeln för uppdateringen. Du kan läsa viktig information om uppdateringen.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klicka på någon annan stans på uppdateringen för att öppna loggs öknings fönstret för den valda uppdateringen. Frågan för loggsökningen är fördefinierad för den specifika uppdateringen. Du kan ändra den här frågan eller skapa en egen fråga för att visa detaljerad information om uppdateringar som har distribuerats eller som saknas i din miljö.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurera varningar

I det här steget lär du dig hur du ställer in en avisering som meddelar dig om statusen för en uppdateringsdistribution.

### <a name="alert-conditions"></a>Aviseringsvillkor

I ditt Automation-konto går du till **aviseringar** under **övervakning**och klickar sedan på **ny aviserings regel**.

Ditt Automation-konto har redan valts som resurs. Om du vill ändra det klickar du på **Välj**. På sidan Välj en resurs väljer du **Automation-konton** i list rutan **Filtrera efter resurs typ** . Välj ditt Automation-konto och klicka sedan på **Slutför**.

Klicka på **Lägg till villkor** för att välja lämplig signal för uppdateringsdistributionen. I följande tabell visas information om de två tillgängliga signalerna.

|Signalnamn|Dimensioner|Beskrivning|
|---|---|---|
|`Total Update Deployment Runs`|– Namnet på uppdateringsdistributionen<br>– Status|Den här signalen aviserar om den övergripande statusen för en uppdaterings distribution.|
|`Total Update Deployment Machine Runs`|– Namnet på uppdateringsdistributionen</br>– Status</br>– Måldator</br>-Uppdatera körnings-ID för distribution|Den här signalen aviserar om status för en uppdaterings distribution som är riktad mot specifika datorer.|

För en dimension väljer du ett giltigt värde i listan. Om det värde som du vill använda inte finns i listan klickar **\+** du på tecknet bredvid dimensionen och skriver in det anpassade namnet. Välj sedan det värde som du vill söka efter. Om du vill välja alla värden för en dimension klickar du på knappen **Välj \* ** . Om du inte väljer ett värde för en dimension ignorerar Uppdateringshantering dimensionen.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/signal-logic.png)

Under **Aviseringslogik**, för **Tröskelvärde**, ange **1**. När du är klar väljer du **Klar**.

### <a name="alert-details"></a>Aviseringsinformation

Under **2. Definiera aviserings information**, ange ett namn och en beskrivning för aviseringen. Ange **allvarlighetsgrad** till **Information (allvarlighetsgrad 2)** för en lyckad körning eller **Information (allvarlighetsgrad 1)** för en misslyckad körning.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/define-alert-details.png)

Under **Åtgärds grupper**väljer du **Skapa ny**. En åtgärdsgrupp är en grupp av åtgärder som kan användas i flera aviseringar. Åtgärderna kan omfatta e-postmeddelanden, Runbooks, Webhooks och många fler. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/platform/action-groups.md).

I fältet **Åtgärds grupp namn** anger du ett namn för aviseringen och ett kort namn. Uppdateringshantering använder det korta namnet i stället för ett fullständigt åtgärds grupp namn när meddelanden skickas med den angivna gruppen.

Under **åtgärder**anger du ett namn för åtgärden, t. ex. **e-postavisering**. För **Åtgärds typ**väljer du **e-post/SMS/push/röst**. Välj **Redigera information**om du vill ha mer **information**.

I rutan e-post/SMS/Push/röst, ange ett namn. Markera kryss rutan **e-** postadress och ange en giltig e-postadress.

![Konfigurera en e-poståtgärdsgrupp](./media/automation-tutorial-update-management/configure-email-action-group.png)

I fönstret e-post/SMS/push/Voice klickar du på **OK**. I fönstret Lägg till åtgärds grupp klickar du på **OK**.

Om du vill anpassa ämnet för e-postaviseringen under **Skapa regel**, under **Anpassa åtgärder**, väljer du **e-postämne**. När du är klar väljer du **Skapa varningsregel**. Aviseringen meddelar dig när en uppdaterings distribution lyckas och vilka datorer som ingår i uppdaterings distributionen.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdaterings typer som ska ingå i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>När du schemalägger en uppdaterings distribution skapas en [schema](shared-resources/schedules.md) resurs som är länkad till **MicrosoftOMSComputers-** runbooken som hanterar uppdaterings distributionen på mål datorerna. Om du tar bort schema resursen från Azure Portal eller använder PowerShell när du har skapat distributionen bryts den schemalagda uppdaterings distributionen och ett fel uppstår när du försöker konfigurera om schema resursen från portalen. Du kan bara ta bort schema resursen genom att ta bort motsvarande distributions schema.  

Schemalägg en ny uppdateringsdistribution för den virtuella datorn, gå till **Uppdateringshantering** och välj sedan **Distribution av schemauppdatering**.

Under **Ny uppdateringsdistribution** anger du följande information:

* **Namn**: Ange ett unikt namn på uppdateringsdistributionen.

* **Operativsystem**: Välj operativsystem som mål för uppdateringsdistributionen.

* **Grupper att uppdatera (för hands version)**: definiera en fråga som kombinerar prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. Mer information finns i [dynamiska grupper](automation-update-management-groups.md).

* **Datorer som ska uppdateras**: Välj en sparad sökning, importerad grupp eller Välj **datorer** i list menyn och välj enskilda datorer. Om du väljer **datorer**visas beredskapen för varje dator i kolumnen **Uppdatera agent beredskap** . Om du vill veta mer om olika metoder för att skapa dator grupper i Azure Monitor loggar, se [dator grupper i Azure Monitor loggar](../azure-monitor/platform/computer-groups.md).

* **Uppdaterings klassificering**: för varje produkt avmarkerar du alla uppdaterings klassificeringar som stöds, men de som ska inkluderas i uppdaterings distributionen. I den här självstudien lämnar du alla typer markerade för alla produkter.

  Klassificeringstyper:

   |Operativsystem  |Typ  |
   |---------|---------|
   |Windows     | Kritiska uppdateringar</br>Säkerhetsuppdateringar</br>Samlade uppdateringar</br>Funktionspaket</br>Service pack</br>Definitionsuppdateringar</br>Verktyg</br>Uppdateringar<br>Drivrutin        |
   |Linux     | Kritiska uppdateringar och säkerhetsuppdateringar</br>Övriga uppdateringar       |

   Beskrivningar av klassificerings typerna finns i [uppdaterings klassificeringar](automation-view-update-assessments.md#update-classifications).

* **Uppdateringar som ska tas med/undanta** – öppnar sidan inkludera/exkludera. Uppdateringar som ska tas med eller undantas finns på separata flikar genom att ange KB-artikelns ID-nummer. När du anger ett eller flera ID-nummer måste du ta bort eller avmarkera alla klassificeringar med uppdaterings distributionen. Detta säkerställer att inga andra uppdateringar tas med i uppdaterings paketet när du anger uppdaterings-ID: n.

> [!NOTE]
> Det är viktigt att veta att undantagen åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*`uppdateringshantering installerar inga korrigeringar eller paket, eftersom de undantas. Undantagna uppdateringar visas fortfarande som saknas på datorn. För Linux-datorer, om du inkluderar ett paket som har ett beroende paket som har uteslutits, installerar Uppdateringshantering inte huvud paketet.

> [!NOTE]
> Du kan inte ange uppdateringar som har ersatts för inkludering med uppdaterings distributionen.
>

* **Schemainställningar**: Sidan Schemainställningar öppnas. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

   Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Under **Återkommande**, välj **En gång**. Låt standardvärdet vara 1 dag och klicka på **OK**. Dessa poster ställer in ett återkommande schema.

* **Skript före och efter**: Välj skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).

* **Underhållsperiod (minuter)**: Låt standardvärdet stå kvar. Windows-underhåll styr den tids period som tillåts för att installera uppdateringar. Tänk på följande när du anger en underhålls period:

  * Underhålls fönster styr hur många uppdateringar som är installerade.
  * Uppdateringshantering slutar inte att installera nya uppdateringar om slutet av en underhålls period närmar sig.
  * Uppdateringshantering avslutar inte pågående uppdateringar om underhålls perioden har överskridits.
  * Om underhålls perioden överskrids i Windows beror det ofta på att en service pack uppdatering tar lång tid att installera.

  > [!NOTE]
  > Om du vill undvika att uppdateringar tillämpas utanför en underhålls period på Ubuntu konfigurerar du om det obevakade uppgraderings paketet så att automatiska uppdateringar inaktive ras. Information om hur du konfigurerar paketet finns i [avsnittet automatiska uppdateringar i Ubuntu Server guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Alternativ för omstart**: Använd för att ange alternativ för att hantera omstarter. Följande alternativ är tillgängliga:
  * Starta om vid behov (standard)
  * Starta alltid om
  * Starta aldrig om
  * Endast omstart – installerar inte uppdateringar

> [!NOTE]
> De register nycklar som listas under [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka händelsen omstart om **alternativen för omstart** är inställd på **Starta aldrig om**.

När du är klar med konfigurationen av schemat klickar du på **skapa**.

![Inställningsfönster för uppdateringsschema](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Du kommer tillbaka till statusinstrumentpanelen. Välj **schemalagda uppdaterings distributioner** för att visa det distributions schema som nyss skapats.

> [!NOTE]
> Uppdateringshantering stöder distribution av uppdateringar från första part och för hämtnings bara uppdateringar. Detta stöd kräver ändringar på de system som korrigeras. Mer information om hur du konfigurerar dessa inställningar på dina system finns i [första part och för hämtnings stöd](automation-configure-windows-update.md) .

Du kan också skapa uppdaterings distributioner program mässigt. Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempel-Runbook som du kan använda för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**. Statusen är **Pågår** när distributionen körs. När distributionen har slutförts ändras statusen till **slutförd**. Om det uppstår fel med en eller flera uppdateringar i distributionen, **misslyckades statusen delvis**.

Välj slutförd uppdaterings distribution för att se instrument panelen.

![Statusinstrumentpanel för Uppdatera distribution för en specifik distribution](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Under **Uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn. Tabellen till höger visar detaljer för varje uppdatering och installationsresultaten.

De tillgängliga värdena är:

* **Inget försök har gjorts**: uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid, baserat på den underhålls period som definierats.
* **Lyckades**: uppdateringen lyckades.
* **Misslyckades**: uppdateringen misslyckades.

Välj **alla loggar** om du vill se alla logg poster som har skapats i distributionen.

Välj **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

När distributionen av uppdateringen lyckas får du ett bekräftelse meddelande som liknar följande:

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