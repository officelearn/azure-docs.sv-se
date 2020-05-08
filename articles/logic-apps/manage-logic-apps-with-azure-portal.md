---
title: Hantera Logic Apps i Azure Portal
description: Redigera, aktivera, inaktivera eller ta bort Logic Apps med hjälp av Azure Portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: d80972cd200b8f85e14d316c4c06a38f88ac81b5
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598172"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Hantera Logic Apps i Azure Portal

Du kan hantera Logic Apps med hjälp av [Azure Portal](https://portal.azure.com) eller [Visual Studio](manage-logic-apps-with-visual-studio.md). Den här artikeln visar hur du redigerar, inaktiverar, aktiverar eller tar bort Logic Apps i Azure Portal. Om du inte har använt Azure Logic Apps, se [Vad är Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En befintlig Logic-app. Information om hur du skapar en Logic app i Azure Portal finns i [snabb start: skapa ditt första arbets flöde med hjälp av Azure Logic Apps-Azure Portal](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Hitta dina Logi Kap par

Följ dessa steg om du vill söka efter och öppna din Logic app:

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

1. I Azure Search-fältet anger `logic apps`du och väljer **Logic Apps**.

   ![Sök efter och välj "Logic Apps"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. På sidan **Logic Apps** letar du reda på och väljer den Logic-app som du vill hantera.

   När **översikts** fönstret för Logic Apps öppnas kan du filtrera listan som visas på sidan **Logic Apps** på följande sätt:

   * Sök efter Logic Apps efter namn
   * Filtrera Logic Apps efter prenumeration, resurs grupp, plats och Taggar
   * Gruppera Logic Apps efter resurs grupp, typ, prenumeration och plats

## <a name="view-logic-app-properties"></a>Visa egenskaper för Logic app

1. [Leta upp och öppna din Logic app](#find-logic-app)i Azure Portal.

1. Från din Logic Apps-meny, under **Inställningar**, väljer du **Egenskaper**.

1. I fönstret **Egenskaper** kan du Visa och kopiera följande information om din Logic app:

   * **Namn**
   * **Resurs-ID**
   * **Resursgrupp**
   * **Position**
   * **Typ** 
   * **Prenumerations namn**
   * **Prenumerations-ID:t**
   * **Åtkomst slut punkt**
   * **Utgående IP-adresser för körning**
   * **Åtkomst till slut punktens IP-adresser**
   * **Ansluta utgående IP-adresser**

## <a name="disable-or-enable-logic-apps"></a>Inaktivera eller aktivera Logic Apps

Du kan aktivera eller inaktivera en [enda Logic-app](#disable-enable-single-logic-app) eller [flera Logic apps samtidigt](#disable-or-enable-multiple-logic-apps) i Azure Portal. Du kan också [Aktivera eller inaktivera Logic Apps i Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Att inaktivera din Logic app påverkar arbets flödes instanserna och körs på följande sätt:

* Alla pågående och väntande körningar fortsätter tills de är klara. Den här processen kan ta lite tid beroende på hur många körningar som körs.

* Logic Appss motorn skapar eller kör inte nya arbets flödes instanser.

* Utlösaren utlöses inte nästa gången dess villkor är uppfyllda.

* Utlösarens tillstånd ommedlemmar den tidpunkt då Logic app stoppades. Så om du aktiverar Logic-appen igen utlöses utlösaren för alla obearbetade objekt sedan den senaste körningen.

  Om du inte vill att din Logi Kap par ska initieras på obearbetade objekt sedan den senaste körningen rensar du utlösarens tillstånd innan du aktiverar Logic-appen igen:

  1. [Leta upp och öppna din Logic app](#find-logic-app)i Azure Portal.

  1. Redigera valfri del av Logic Apps-utlösaren.

  1. Spara ändringarna. Det här steget återställer utlösarens aktuella status.

  1. Återaktivera [din Logic app](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Inaktivera eller aktivera en enda Logic-app

1. [Leta upp och öppna din Logic app](#find-logic-app)i Azure Portal.

1. I din Logic Apps-meny väljer du **Översikt**. Välj bland följande alternativ:

   * Välj **inaktivera**i verktygsfältet.

     ![Inaktivera en app för enskild logik i Azure Portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Om din Logic app redan är inaktive rad visas bara alternativet **Aktivera** .

   * Välj **Aktivera**i verktygsfältet.

     ![Aktivera en enda Logic-app i Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Om din Logic app redan är aktive rad visas bara alternativet **inaktivera** . 

   Azure Portal visar ett meddelande i Azures huvud verktygsfält som bekräftar om åtgärden lyckades eller misslyckades.

   ![Meddelande för att bekräfta åtgärds status](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Inaktivera eller aktivera flera Logic Apps

1. [Leta upp de](#find-logic-app) Logi Kap par som du vill inaktivera eller aktivera i Azure Portal.

1. Om du vill kontrol lera om en Logic app är aktive rad eller inaktive rad går du till sidan **Logic Apps** och granskar kolumnen **status** för den Logic-appen. 

   ![Kolumnen Logic Apps status](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Om kolumnen **status** inte visas går du till **Logic Apps** verktygsfältet och väljer **prova för hands version**.

   ![Aktivera för hands version](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. I CheckBox-kolumnen väljer du de Logi Kap par som du vill inaktivera eller aktivera. Välj **inaktivera** eller **Aktivera**i verktygsfältet.

   ![Aktivera eller inaktivera flera Logic Apps i Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. När rutan bekräftelse visas väljer du **Ja** för att fortsätta.

   Azure Portal visar ett meddelande i Azures huvud verktygsfält som bekräftar om åtgärden lyckades eller misslyckades.

## <a name="delete-logic-apps"></a>Ta bort Logic Apps

Du kan [ta bort en enda Logic-app](#delete-single-logic-app) eller [ta bort flera Logic apps samtidigt](#delete-multiple-logic-apps) i Azure Portal. Du kan också [ta bort din Logic app i Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Att ta bort din Logic app påverkar arbets flödes instanserna på följande sätt:

* Alla pågående och väntande körningar fortsätter tills de är klara. Den här processen kan ta lite tid beroende på hur många körningar som körs.

* Logic Appss motorn skapar eller kör inte nya arbets flödes instanser.

> [!NOTE]
> Om du tar bort och återskapar en underordnad Logic-app måste du spara om den överordnade Logic-appen. Den omskapade underordnade appen kommer att ha olika metadata.
> Om du inte sparar om den överordnade Logic-appen när du har återskapat dess underordnade Logic-program, kommer anropen till den underordnade Logic-appen att Miss varnas med fel "obehörig". Det här beteendet gäller för över-underordnade Logic Apps, till exempel de som använder artefakter i integrations konton eller anropar Azure Functions.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Ta bort appen med en enda logik

1. [Leta upp och öppna din Logic app](#find-logic-app)i Azure Portal.

1. I din Logic Apps-meny väljer du **Översikt**. I din Logic Apps-verktygsfält väljer du **ta bort**.

   ![I verktygsfältet för Logic app väljer du ta bort](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. När rutan bekräftelse visas anger du namnet på din Logic Apps och väljer **ta bort**.

   ![Bekräfta att du vill ta bort din Logic app](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure Portal visar ett meddelande i Azures huvud verktygsfält som bekräftar om åtgärden lyckades eller misslyckades.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Ta bort flera Logic Apps

1. I Azure Portal [letar du reda på de Logi Kap par som du vill ta bort](#find-logic-app).

1. I kryss Rute kolumnen väljer du de Logi Kap par som du vill ta bort. Välj **ta bort**i verktygsfältet.

   ![Ta bort flera Logic Apps](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. När bekräftelse rutan visas anger `yes`du och väljer **ta bort**.

   ![Bekräfta att du vill ta bort dina Logic Apps](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure Portal visar ett meddelande i Azures huvud verktygsfält som bekräftar om åtgärden lyckades eller misslyckades.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Hantera Logic app-versioner

Du kan använda Azure Portal för versions kontroll av dina Logic Apps. Du hittar din Logic Apps versions historik och höjer tidigare versioner.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Hitta och Visa tidigare versioner

1. [Leta upp den Logic-app som du vill hantera](#find-logic-app)i Azure Portal.

1. I din Logic Apps-meny, under **utvecklingsverktyg**, väljer du **versioner**.

   ![På din Logic Apps-meny väljer du versioner under utvecklings verktyg](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Välj den **version** av din Logic-app som ska hanteras från listan. Du kan ange **versions** identifieraren i Sök fältet för att filtrera listan.

1. På sidan **Historik version** visas den tidigare versionens information i skrivskyddat läge. Du kan välja mellan lägena Logic Apps **Designer** och **kodvy** .

   ![Sidan historik version för Logic app med vyn kodvyn och vyn Logic Apps designer](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Höj upp tidigare versioner

1. [Leta upp och välj den version som du vill befordra](#find-version-history)i din Logic Apps versions historik.

1. På sidan **Historik version** väljer du **befordra**.

   ![Knappen befordra i Logic Apps versions historik](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. På sidan **Logic Apps designer** som öppnas redigerar du den version som du vill uppgradera efter behov. Du kan växla mellan **Designer** och **kod visnings** lägen. Du kan också uppdatera **parametrar**, **mallar**och **kopplingar**.

   ![Logic Apps designer-sida för att uppgradera en tidigare version](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Om du vill spara uppdateringar och slutföra befordran av den tidigare versionen väljer du **Spara**. (Om du vill avbryta ändringarna väljer du **Ignorera**.) 

   När du [visar versions historiken för din Logic Apps](#find-version-history) igen visas den uppgraderade versionen överst i listan och har en ny identifierare.

## <a name="next-steps"></a>Nästa steg

* [Övervaka logikappar](monitor-logic-apps.md)
