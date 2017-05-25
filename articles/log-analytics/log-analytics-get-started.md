---
title: "Komma igång med en arbetsyta för Azure Log Analytics | Microsoft Docs"
description: "Du kan komma igång med en arbetsyta i Log Analytics på bara några minuter."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 508716de-72d3-4c06-9218-1ede631f23a6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 634b4c99b746ad0b8b3238e11b3c305d2cf7082f
ms.contentlocale: sv-se
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-a-log-analytics-workspace"></a>Komma igång med en arbetsyta för Log Analytics
Du kan snabbt komma igång med Azure Log Analytics som hjälper dig att utvärdera operativ information som samlas in från din IT-infrastruktur. Med den här artikeln kan du enkelt börja utforska, analysera och vidta åtgärder baserat på data som du samlar in *kostnadsfritt*.

Den här artikeln fungerar som en introduktion till Log Analytics med en kort självstudie som leder dig igenom en minimal distribution i Azure och hjälper dig att komma igång med att använda tjänsten. Den logiska behållaren där dina hanteringsdata i Azure lagras kallas för en arbetsyta. När du har granskat den här informationen och slutfört din egna utvärdering kan du ta bort utvärderingsarbetsytan. Eftersom den här artikeln är en självstudie tar den inte upp vägledning för affärsbehov, planering och arkitektur.

>[!NOTE]
>Om du använder Microsoft Azure Government-molnet bör du istället använda [Azure Government-övervakning + Management-dokumentation](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#log-analytics).

Här är en snabb översikt över processen som används för att komma igång:

![processdiagram](./media/log-analytics-get-started/onboard-oms.png)

## <a name="1-create-an-azure-account-and-sign-in"></a>1 Skapa ett Azure-konto och logga in

Om du inte redan har ett Azure-konto måste du skapa ett för att kunna använda Log Analytics. Du kan skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) som gäller i 30 dagar och som ger dig åtkomst till vilka Azure-tjänster du vill.

### <a name="to-create-a-free-account-and-sign-in"></a>För att skapa ett kostnadsfritt konto och logga in
1. Följ anvisningarna i [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).
2. Gå till [Azure Portal](https://portal.azure.com) och logga in.

## <a name="2-create-a-workspace"></a>2 Skapa en arbetsyta

Nästa steg är att skapa en arbetsyta.

1. I Azure Portal söker du efter *Log Analytics* i listan över tjänster på Marketplace och väljer sedan **Log Analytics**.  
    ![Azure Portal](./media/log-analytics-get-started/log-analytics-portal.png)
2. Klicka på **Skapa** och välj alternativ för följande objekt:
   * **OMS-arbetsytan** – Ange ett namn för arbetsytan.
   * **Prenumeration** – Om du har flera prenumerationer väljer du den du vill associera med den nya arbetsytan.
   * **Resursgrupp**
   * **Plats**
   * **prisnivå**  
       ![snabbregistrering](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Klicka på **OK** för att se en lista över dina arbetsytor.
4. Välj en arbetsyta om du vill se information om den i Azure Portal.       
    ![information om arbetsytan](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         

## <a name="3-add-solutions-and-solution-offerings"></a>3 Lägg till lösningar och erbjudanden för lösningar

Lägg sedan till hanteringslösningar och erbjudanden för lösningar. Hanteringslösningar är en samling logik, visualisering och datahämtningsregler som tillhandahåller mått som pivoteras runt ett särskilt problematisk område. Ett erbjudande för lösningar är ett paket med hanteringslösningar.

Genom att lägga till lösningar i arbetsytan kan Log Analytics samla in olika typer av data från datorer som är anslutna till din arbetsyta med agenter. Vi täcker publicering av agenter senare.

### <a name="to-add-solutions-and-solution-offerings"></a>Lägg till lösningar och erbjudanden för lösningar

1. I Azure Portal klickar du på **Ny** och sedan skriver du **Activity Log Analytics** i rutan **Sök på marketplace** och trycker på RETUR.
2. Välj **Activity Log Analytics** och klicka sedan på **Skapa** på bladet Allt.  
    ![Activity Log Analytics](./media/log-analytics-get-started/activity-log-analytics.png)  
3. På bladet *hanteringslösningsnamn* väljer du en arbetsyta som du vill koppla till hanteringslösningen.
4. Klicka på **Skapa**.  
    ![lösningens arbetsyta](./media/log-analytics-get-started/solution-workspace.png)  
5. Upprepa steg 1-4 om du vill lägga till:
    - Tjänsterbjudandet **Säkerhet och efterlevnad** med lösningarna Utvärdering av program mot skadlig kod samt Säkerhet och granskning.
    - Tjänsterbjudandet **Automatisering och kontroll** med lösningarna Automation Hybrid Worker, Ändringsspårning och Systemuppdateringsbedömning (kallas även för Hantering av uppdateringar). När du lägger till lösningserbjudandet måste du skapa ett Automation-konto.  
        ![Automation-konto](./media/log-analytics-get-started/automation-account.png)  
6. Du kan se de hanteringslösningar som du lade till arbetsytan genom att navigera till **Log Analytics** > **Prenumerationer** > ***Namn på arbetsyta*** > **Översikt**. Paneler för hanteringslösningar som du har lagt till visas.  
    >[!NOTE]
    >Eftersom vi inte ännu har anslutit några agenter till arbetsytan visas inte några data för de lösningar som du har lagt till.  

    ![lösningspaneler utan data](./media/log-analytics-get-started/solutions-no-data.png)

## <a name="4-create-a-vm-and-onboard-an-agent"></a>4 Skapa en virtuell dator och publicera en agent

Härnäst skapar du en enkel virtuell dator i Azure. När du har skapat en virtuell dator publicerar du OMS-agenten för att aktivera den. När du aktiverar agenten börjar insamling av data från den virtuella datorn och skickar data till Log Analytics.

### <a name="to-create-a-virtual-machine"></a>Skapa en virtuell dator

- Följ anvisningarna i [Skapa din första virtuella Windows-dator på Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) och starta den nya virtuella datorn.

### <a name="connect-the-virtual-machine-to-log-analytics"></a>Anslut den virtuella datorn till Log Analytics

- Följ anvisningarna i [Connect Azure virtual machines to Log Analytics](log-analytics-azure-vm-extension.md) (Anslut virtuella Azure-datorer till Log Analytics) för att ansluta den virtuella datorn till Log Analytics i Azure Portal.

## <a name="5-view-and-act-on-data"></a>5 Se och vidta åtgärder för data

Tidigare aktiverade du Activity Log Analytics-lösningen och tjänsterbjudandena för säkerhet och efterlevnad samt automatisering och kontroll. Härnäst börjar vi titta på de data som samlas in av lösningar och resultat i loggsökningar.

Titta först på data som visas från lösningarna. Titta sedan på några loggsökningar som kan nås från loggsökningar. Loggsökningar låter dig kombinera och korrelera datordata från flera källor i din miljö. Mer information finns i [Loggsökningar i Log Analytics](log-analytics-log-searches.md). Till sist kan du vidta åtgärder för data som vi hittar med OMS-portalen, som är utanför Azure Portal.

### <a name="to-view-antimalware-data"></a>Visa data för program mot skadlig kod

1. Navigera till **Log Analytics** > ***din arbetsyta*** i Azure Portal.
2. Klicka på **Översikt** under **Allmänt** på bladet för din arbetsyta.  
    ![Översikt](./media/log-analytics-get-started/overview.png)
3. Klicka på ikonen **Utvärdering av program mot skadlig kod**. I det här exemplet kan du se att Windows Defender är installerat på en dator, men att dess signatur är inaktuell.  
    ![Programvara mot skadlig kod](./media/log-analytics-get-started/solution-antimalware.png)
4. I det här exemplet klickar du på **Signature out of date** (Signaturen är inaktuell) under **Skyddsstatus** för att öppna loggsökning och se information om de datorer som har inaktuella signaturer. Observera att namnet på datorn i det här exemplet är *getstarted*. Om fler än en dator har inaktuella signaturer visas de i loggsökningsresultaten.  
    ![Program mot skadlig kod är inaktuellt](./media/log-analytics-get-started/antimalware-search.png)

### <a name="to-view-security-and-audit-data"></a>För att se säkerhet- och granskningsdata

1. Klicka på **Översikt** under **Allmänt** på bladet för din arbetsyta.  
2. Klicka på ikonen **Säkerhet och granskning**. I det här exemplet kan du se att det finns två viktiga problem: en dator saknar viktiga uppdateringar och en dator har otillräckligt skydd.  
    ![Säkerhet och granskning](./media/log-analytics-get-started/security-audit.png)
3. I det här exemplet klickar du på **Datorer som saknar viktiga uppdateringar** under **Anmärkningsvärda problem** för att öppna loggsökning och se information om datorer som saknar viktiga uppdateringar. I det här exemplet saknas en viktig uppdatering och 63 andra uppdateringar.  
    ![Loggsökning för säkerhet och granskning](./media/log-analytics-get-started/security-audit-log-search.png)

### <a name="to-view-and-act-on-system-update-data"></a>Visa och vidta åtgärder för systemuppdateringsdata

1. Klicka på **Översikt** under **Allmänt** på bladet för din arbetsyta.  
2. Klicka på ikonen **Bedömning av systemuppdatering**. I det här exemplet kan du se att en Windows-dator med namnet *getstarted* saknar viktiga uppdateringar och en saknar definitionsuppdateringar.  
    ![Systemuppdateringar](./media/log-analytics-get-started/system-updates.png)
3. Det här exemplet klickar du på **Viktiga uppdateringar** under **Saknade uppdateringar** för att öppna loggsökning och se information om datorer som saknar viktiga uppdateringar. I det här exemplet saknas en uppdatering och det finns en nödvändig uppdatering.  
    ![Systemuppdateringar för loggsökning](./media/log-analytics-get-started/system-updates-log-search.png)
4. Gå till webbplatsen [Operations Management Suite](http://microsoft.com/oms) och logga in med ditt Azure-konto. Observera att lösningsinformationen liknar det som visas i Azure Portal när du har loggat in.  
    ![OMS-portalen](./media/log-analytics-get-started/oms-portal.png)
5. Klicka på ikonen **Hantering av uppdateringar**.
6. Observera att systemuppdateringsinformationen i instrumentpanelen Hantering av uppdateringar liknar systemuppdateringsinformationen som du kan se i Azure Portal. Men ikonen för att **hantera distributioner av uppdateringar** är ny. Klicka på ikonen för att **hantera distributioner av uppdateringar**.  
    ![ikonen Hantering av uppdateringar](./media/log-analytics-get-started/update-management.png)
7. Klicka på **Lägg till** på sidan **Distributioner av uppdateringar** för att skapa en *uppdateringskörning*.  
    ![Distributioner av uppdateringar](./media/log-analytics-get-started/update-management-update-deployments.png)
8.  Gå till sidan **Ny distribution av uppdateringar** och skriv ett namn för distributionen. Välj sedan de datorer som ska uppdateras (i det här exemplet *getstarted*), välj ett schema och klicka sedan på **Spara**.  
    ![Ny distribution](./media/log-analytics-get-started/new-deployment.png)  
    När du har sparat distributionen av uppdateringar kan du se den schemalagda uppdateringen.  
    ![schemalagd uppdatering](./media/log-analytics-get-started/scheduled-update.png)  
    När uppdateringskörningen har slutförts visas statusen **Slutförd**.
    ![färdig uppdatering](./media/log-analytics-get-started/completed-update.png)
9. När uppdateringskörningen har slutförts kan du se om körningen lyckades eller inte, och du kan se information om vilka uppdateringar som tillämpades.

## <a name="after-evaluation"></a>Efter utvärdering

I den här självstudien installerade du en agent på en virtuell dator och kom igång snabbt. Stegen som du följde var snabba och enkla. Men de flesta stora organisationer och företag har komplexa lokala IT-infrastrukturer. Därmed kan det krävas ytterligare planering och arbete för att samla in data från dessa komplexa miljöer än vad som visas i självstudien. Granska informationen i det följande avsnittet med de efterföljande stegen för länkar till användbara artiklar.

Du kan eventuellt ta bort arbetsytan som du skapade med den här självstudien.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om hur du ansluter [Windows-agenter](log-analytics-windows-agents.md) till Log Analytics.
* Lär dig mer om hur du ansluter [Operations Manager-agenter](log-analytics-om-agents.md) till Log Analytics.
* [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
* Bekanta dig med [loggsökningar](log-analytics-log-searches.md) för att visa detaljerad information som samlas in av lösningar.

