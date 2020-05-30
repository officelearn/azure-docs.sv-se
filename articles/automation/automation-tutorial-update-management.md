---
title: Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Azure Automation
description: Den här artikeln beskriver hur du använder Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 79cffa7aedd0fc04dd4a747ef28bc67cacf37905
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204896"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer

Den här artikeln beskriver hur du kan använda funktionen Azure Automation [uppdateringshantering](automation-update-management.md) för att hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer. Pris information finns i avsnittet [om Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> Uppdateringshantering stöder distribution av uppdateringar från första part och för hämtning av korrigeringar. Detta stöd kräver ändringar på de system som korrigeras. Mer information om hur du konfigurerar inställningarna på dina system finns i [konfigurera Windows Update inställningar för Azure Automation uppdateringshantering](automation-configure-windows-update.md) .

Innan du använder procedurerna i den här artikeln bör du se till att du har aktiverat Uppdateringshantering på dina virtuella datorer med någon av följande metoder:

* [Aktivera Uppdateringshantering från ett Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Aktivera Uppdateringshantering genom att bläddra i Azure Portal](automation-onboard-solutions-from-browse.md)
* [Aktivera Uppdateringshantering från en runbook](automation-onboard-solutions.md)
* [Aktivera Uppdateringshantering från en virtuell Azure-dator](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Begränsa omfånget för distributionen

Uppdateringshantering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska ta emot uppdateringar. Mer information finns i [begränsa uppdateringshantering distributions omfång](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

Så här visar du en uppdaterings bedömning:

1. I ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**. 

2. Uppdateringarna för din miljö visas på sidan hantering av uppdateringar. Om några uppdateringar identifieras som saknade visas en lista med uppdateringar som saknas på fliken uppdateringar som **saknas** .

3. Under **informations länk**väljer du länken för en uppdatering för att öppna Support artikeln som innehåller viktig information om uppdateringen.

    ![Visa uppdateringsstatus](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Klicka på någon annan stans på uppdateringen för att öppna loggs öknings fönstret. Frågan för loggsökningen är fördefinierad för den specifika uppdateringen. Du kan ändra den här frågan eller skapa en egen fråga för att visa detaljerad information.

    ![Visa uppdateringsstatus](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurera varningar

Följ stegen nedan för att ställa in aviseringar så att du kan se status för en uppdaterings distribution:

1. I ditt Automation-konto går du till **aviseringar** under **övervakning**och klickar sedan på **ny aviserings regel**.

2. Ditt Automation-konto har redan valts som resurs på sidan Skapa aviserings regel. Om du vill ändra det klickar du på **Redigera resurs**. 

3. På sidan Välj en resurs väljer du **Automation-konton** i list rutan **Filtrera efter resurs typ** . 

4. Välj det Automation-konto som du vill använda och klicka sedan på **Slutför**.

5. Klicka på **Lägg till villkor** för att välja den signal som är lämplig för uppdaterings distributionen. I följande tabell visas information om de två tillgängliga signalerna.

    |Signalnamn|Dimensioner|Description
    |---|---|---|
    |`Total Update Deployment Runs`|– Namnet på uppdateringsdistributionen<br>-Status    |Aviseringar om den övergripande statusen för en uppdaterings distribution.|
    |`Total Update Deployment Machine Runs`|– Namnet på uppdateringsdistributionen</br>– Status</br>– Måldator</br>-Uppdatera körnings-ID för distribution    |Aviseringar om status för en uppdaterings distribution som är riktad mot specifika datorer.|

6. För en dimension väljer du ett giltigt värde i listan. Om det värde som du vill använda inte finns i listan klickar du på **\+** bredvid dimensionen och skriver in det anpassade namnet. Välj sedan det värde som du vill söka efter. Om du vill välja alla värden för en dimension klickar du på knappen **Välj \* ** . Om du inte väljer ett värde för en dimension ignorerar Uppdateringshantering dimensionen.

    ![Konfigurera signallogiken](./media/automation-tutorial-update-management/signal-logic.png)

7. Under **aviserings logik**anger du värden i fälten **tids mängd** och **tröskelvärde** och klickar sedan på **Slutför**.

8. I nästa fönster anger du ett namn och en beskrivning för aviseringen.

9. Ange fältet **allvarlighets grad** till **information (allvarlighets grad 2)** för lyckad körning eller **information (allvarlighets grad 1)** för en misslyckad körning.

    ![Konfigurera signallogiken](./media/automation-tutorial-update-management/define-alert-details.png)

10. Klicka på **Ja** eller **Nej**, beroende på hur du vill aktivera varnings regeln.

11. Om du inte vill att aviseringar för den här regeln ska visas väljer du **Ignorera aviseringar**.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurera åtgärds grupper för dina aviseringar

När du har konfigurerat dina aviseringar kan du konfigurera en åtgärds grupp, som är en grupp åtgärder som ska användas över flera aviseringar. Åtgärderna kan omfatta e-postmeddelanden, Runbooks, Webhooks och många fler. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/platform/action-groups.md).

1. Välj en avisering och välj sedan **Skapa ny** under **Åtgärds grupper**. 

2. Ange ett fullständigt namn och ett kort namn för åtgärds gruppen. Uppdateringshantering använder det korta namnet när meddelanden skickas med den angivna gruppen.

3. Under **åtgärder**anger du ett namn som anger åtgärden, till exempel **e-postavisering**. 

4. För **Åtgärds typ**väljer du lämplig typ, till exempel **e-post/SMS/push/röst**. 

5. Klicka på **Redigera information**.

6. Fyll i fönstret för åtgärds typen. Om du till exempel använder **e-post/SMS/push/röst**anger du ett åtgärds namn, markerar kryss rutan **e-** postadress, anger en giltig e-postadress och klickar sedan på **OK**.

    ![Konfigurera en e-poståtgärdsgrupp](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. I fönstret Lägg till åtgärds grupp klickar du på **OK**.

8. För e-postaviseringar kan du anpassa e-postmeddelandets ämne. Välj **Anpassa åtgärder** under **Skapa regel**och välj sedan **e-postämne**. 

9. När du är klar klickar du på **skapa aviserings regel**. 

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

När du schemalägger en uppdaterings distribution skapas en [schema](shared-resources/schedules.md) resurs som är länkad till **MicrosoftOMSComputers-** runbooken som hanterar uppdaterings distributionen på mål datorerna. Du måste schemalägga en distribution som följer ditt versions schema och service fönster för att installera uppdateringar. Du kan välja vilka uppdaterings typer som ska ingå i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>Om du tar bort schema resursen från Azure Portal eller använder PowerShell när du har skapat distributionen bryts den schemalagda uppdaterings distributionen och ett fel uppstår när du försöker konfigurera om schema resursen från portalen. Du kan bara ta bort schema resursen genom att ta bort motsvarande distributions schema.  

Så här schemalägger du en ny uppdaterings distribution:

1. I ditt Automation-konto går du till **uppdaterings hantering** under **uppdaterings hantering**och väljer sedan **Schemalägg uppdaterings distribution**.

2. Under **ny uppdaterings distribution**använder du fältet **namn** för att ange ett unikt namn för distributionen.

3. Välj det operativ system som ska vara mål för uppdaterings distributionen.

4. I området **grupper att uppdatera (för hands version)** definierar du en fråga som kombinerar prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. Läs mer i [använda dynamiska grupper med uppdateringshantering](automation-update-management-groups.md).

5. I området **datorer att uppdatera** väljer du en sparad sökning, en importerad grupp eller väljer **datorer** från List menyn och väljer enskilda datorer. Med det här alternativet kan du se beredskap för Log Analytics agenten för varje dator. Om du vill veta mer om olika metoder för att skapa dator grupper i Azure Monitor loggar, se [dator grupper i Azure Monitor loggar](../azure-monitor/platform/computer-groups.md).

6. Använd området **uppdaterings klassificeringar** för att ange [uppdaterings klassificeringar](automation-view-update-assessments.md#work-with-update-classifications) för produkter. För varje produkt avmarkerar du alla uppdaterings klassificeringar som stöds, men de som ska inkluderas i uppdaterings distributionen.

7. Använd området **Inkludera/exkludera uppdateringar** om du vill välja vissa uppdateringar för distribution. Sidan inkludera/exkludera visar uppdateringar av KB-artikel-ID-nummer som ska tas med eller undantas. 
    
   > [!IMPORTANT]
   > Kom ihåg att undantag åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*` uppdateringshantering undantas alla korrigeringar eller paket från installationen. Undantagna uppdateringar visas fortfarande som saknade från datorerna. För Linux-datorer, om du inkluderar ett paket som har ett beroende paket som har uteslutits, installerar Uppdateringshantering inte huvud paketet.

   > [!NOTE]
   > Du kan inte ange uppdateringar som har ersatts av med i uppdaterings distributionen.

8. Välj **schema inställningar**. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

9. Använd fältet **upprepning** för att ange om distributionen ska ske en gång eller använder ett återkommande schema och klicka sedan på **OK**.

10. I området **före skript + efter skript (för hands version)** väljer du de skript som ska köras före och efter distributionen. Läs mer i [Hantera för-skript och post-skript](pre-post-scripts.md).
    
11. Använd fältet **underhålls period (minuter)** för att ange hur lång tid som tillåts för uppdateringar som ska installeras. Tänk på följande när du anger en underhålls period:

    * Underhålls fönster styr hur många uppdateringar som är installerade.
    * Uppdateringshantering slutar inte att installera nya uppdateringar om slutet av en underhålls period närmar sig.
    * Uppdateringshantering avslutar inte pågående uppdateringar om underhålls perioden har överskridits.
    * Om underhålls perioden överskrids i Windows beror det ofta på att en service pack uppdatering tar lång tid att installera.

    > [!NOTE]
    > Om du vill undvika att uppdateringar tillämpas utanför en underhålls period på Ubuntu konfigurerar du om `Unattended-Upgrade` paketet för att inaktivera automatiska uppdateringar. Information om hur du konfigurerar paketet finns i [avsnittet automatiska uppdateringar i Ubuntu Server guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Använd fältet **alternativ för omstart** för att ange hur omstarter ska hanteras under distributionen. Följande alternativ är tillgängliga: 
    * Starta om vid behov (standard)
    * Starta alltid om
    * Starta aldrig om
    * Endast omstart; Det här alternativet installerar inte uppdateringar

    > [!NOTE]
    > De register nycklar som listas under [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka händelsen omstart om **alternativen för omstart** är inställd på **Starta aldrig om**.

13. När du är klar med att konfigurera distributions schemat klickar du på **skapa**.

    ![Inställningsfönster för uppdateringsschema](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Du kommer tillbaka till statusinstrumentpanelen. Välj **schemalagda uppdaterings distributioner** för att visa det distributions schema som du har skapat.

## <a name="schedule-an-update-deployment-programmatically"></a>Schemalägga en uppdaterings distribution program mässigt

Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create). 

Du kan använda en exempel-Runbook för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Kontrol lera distributions status

När den schemalagda distributionen har startats kan du se dess status på fliken **uppdaterings distributioner** under **uppdaterings hantering**. Statusen är **Pågår** när distributionen körs. När distributionen har slutförts ändras statusen till **slutförd**. Om det uppstår fel med en eller flera uppdateringar i distributionen, **misslyckades statusen delvis**.

## <a name="view-results-of-a-completed-update-deployment"></a>Visa resultatet av en slutförd uppdaterings distribution

När distributionen är färdig kan du välja den för att se instrument panelen.

![Statusinstrumentpanel för Uppdatera distribution för en specifik distribution](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Under **uppdaterings resultat**innehåller en sammanfattning det totala antalet uppdateringar och distributions resultat på de virtuella mål datorerna. Tabellen till höger visar en detaljerad analys av uppdateringarna och installations resultaten för var och en.

De tillgängliga värdena är:

* **Inget försök har gjorts** – uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid, baserat på den definierade varaktigheten för underhålls perioden.
* **Inte valt** – uppdateringen har inte valts för distribution.
* **Lyckades** – uppdateringen är klar.
* **Misslyckades** -uppdateringen misslyckades.

Välj **alla loggar** om du vill se alla logg poster som har skapats i distributionen.

Välj **utdata** om du vill se jobb strömmen för den Runbook som ansvarar för att hantera uppdaterings distributionen på de virtuella mål datorerna.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="view-the-deployment-alert"></a>Visa distributions aviseringen

När din uppdaterings distribution har slutförts visas den avisering som du har angett under installationen för distributionen. Här är till exempel ett e-postmeddelande som bekräftar en korrigering.

![Konfigurera e-poståtgärdsgrupp](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Nästa steg

* Information om scope-konfigurationer finns i [begränsa uppdateringshantering distributions omfång](automation-scope-configurations-update-management.md).
* Om du behöver söka i loggar som lagras i din Log Analytics-arbetsyta, se [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).
* Om du är färdig med distributioner, se [ta bort arbets ytan för att länka från Automation-kontot för uppdateringshantering](automation-unlink-workspace-update-management.md).
* Information om hur du tar bort virtuella datorer från Uppdateringshantering finns i [ta bort virtuella datorer från uppdateringshantering](automation-remove-vms-from-update-management.md).
* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Information om hur du felsöker problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux Update-agenten finns i [Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).