---
title: Hantera logikappar i Azure-portalen
description: Redigera, aktivera, inaktivera eller ta bort logikappar med hjälp av Azure-portalen.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/06/2020
ms.openlocfilehash: a35f34ed27299077dca78630511ea3c291caff60
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804980"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Hantera logikappar i Azure-portalen

Du kan hantera logikappar med [Azure-portalen](https://portal.azure.com) eller [Visual Studio](manage-logic-apps-with-visual-studio.md). Den här artikeln visar hur du redigerar, inaktiverar, aktiverar eller tar bort logikappar i Azure-portalen. Om du inte har gjort det tidigare i Azure Logic Apps läser du [Vad är Azure Logic Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En befintlig logikapp. Mer information om hur du skapar en logikapp i Azure-portalen finns i [Snabbstart: Skapa ditt första arbetsflöde med hjälp av Azure Logic Apps - Azure portal](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Hitta dina logikappar

Så här hittar du och öppnar logikappen:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

1. I Sökfältet i `logic apps`Azure anger du och väljer **Logic Apps**.

   ![Sök efter och välj "Logikappar"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. På sidan **Logikappar** hittar du och väljer den logikapp som du vill hantera.

   När logikappens **översiktsfönster** har öppnats kan du filtrera listan som visas på sidan **Logic Apps** på följande sätt:

   * Sök efter logikappar efter namn
   * Filtrera logikappar efter prenumeration, resursgrupp, plats och taggar
   * Gruppera logikappar efter resursgrupp, typ, prenumeration och plats

## <a name="view-logic-app-properties"></a>Visa egenskaper för logikapp

1. I Azure-portalen [kan du hitta och öppna logikappen](#find-logic-app).

1. Välj **Egenskaper**under **Inställningar**på logikappens meny .

1. I fönstret **Egenskaper** kan du visa och kopiera följande information om logikappen:

   * **Namn**
   * **Resurs-ID**
   * **Resursgrupp**
   * **Location**
   * **Typ** 
   * **Namn på prenumeration**
   * **Prenumerations-ID**
   * **Slutpunkt för Åtkomst**
   * **Utgående IP-adresser för körning**
   * **IP-adresser för åtkomst till slutpunkter**
   * **Ansluta utgående IP-adresser**

## <a name="disable-or-enable-logic-apps"></a>Inaktivera eller aktivera logikappar

Du kan aktivera eller inaktivera en [enda logikapp](#disable-enable-single-logic-app) eller [flera logikappar samtidigt](#disable-or-enable-multiple-logic-apps) i Azure-portalen. Du kan också [aktivera eller inaktivera logikappar i Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Om du inaktiverar logikappen påverkas dina arbetsflödesinstanser och körs på följande sätt:

* Alla pågående och väntande körningar fortsätter tills de är klara. Beroende på antalet av dessa körningar kan den här processen ta lite tid.

* Logic Apps-motorn skapar eller kör inte nya arbetsflödesinstanser.

* Utlösaren avfyras inte nästa gång dess villkor är uppfyllda.

* Utlösarläget kommer ihåg den punkt där logikappen stoppades. Så om du återaktiverar logikappen utlöses utlösaren för alla obearbetade objekt sedan den senaste körningen.

  Om du vill förhindra att logikappen aktiveras på obearbetade objekt sedan den senaste körningen avmarkerar du utlösarens tillstånd innan du återaktiverar logikappen:

  1. I Azure-portalen [kan du hitta och öppna logikappen](#find-logic-app).

  1. Redigera någon del av logikappens utlösare.

  1. Spara ändringarna. Det här steget återställer utlösarens aktuella tillstånd.

  1. [Återaktivera logikappen](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Inaktivera eller aktivera en logikapp

1. I Azure-portalen [kan du hitta och öppna logikappen](#find-logic-app).

1. På logikappens meny väljer du **Översikt**. Välj bland följande alternativ:

   * Välj **Inaktivera**i verktygsfältet .

     ![Inaktivera en logikapp i Azure Portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Om logikappen redan är inaktiverad visas bara alternativet **Aktivera.**

   * Välj **Aktivera**i verktygsfältet .

     ![Aktivera en logikapp i Azure portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Om logikappen redan är aktiverad visas bara alternativet **Inaktivera.** 

   Azure-portalen visar ett meddelande i huvudverktygsfältet i Azure som bekräftar om åtgärden lyckades eller misslyckades.

   ![Meddelande för att bekräfta åtgärdsstatus](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Inaktivera eller aktivera flera logikappar

1. Leta reda på de logikappar som du vill inaktivera eller aktivera [i Azure-portalen.](#find-logic-app)

1. Om du vill kontrollera om en logikapp är aktiverad eller inaktiverad granskar du kolumnen **Status** för logikappen på sidan **Logic Apps.** 

   ![Statuskolumn för Logic Apps](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Om kolumnen **Status** inte visas väljer du Prova **förhandsgranska**i verktygsfältet **Logic Apps.**

   ![Aktivera förhandsgranskning](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Markera de logikappar som du vill inaktivera eller aktivera i kryssrutan. Välj **Inaktivera** eller **Aktivera**i verktygsfältet .

   ![Aktivera eller inaktivera flera logikappar i Azure-portalen](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. När bekräftelserutan visas väljer du **Ja** för att fortsätta.

   Azure-portalen visar ett meddelande i huvudverktygsfältet i Azure som bekräftar om åtgärden lyckades eller misslyckades.

## <a name="delete-logic-apps"></a>Ta bort logikappar

Du kan [ta bort en enda logikapp](#delete-single-logic-app) eller ta bort flera [logikappar samtidigt](#delete-multiple-logic-apps) i Azure-portalen. Du kan också [ta bort logikappen i Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Om du tar bort logikappen påverkas dina arbetsflödesinstanser på följande sätt:

* Alla pågående och väntande körningar fortsätter tills de är klara. Beroende på antalet av dessa körningar kan den här processen ta lite tid.

* Logic Apps-motorn skapar eller kör inte nya arbetsflödesinstanser.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Ta bort en logikapp

1. I Azure-portalen [kan du hitta och öppna logikappen](#find-logic-app).

1. På logikappens meny väljer du **Översikt**. Välj **Ta bort**i logikappens verktygsfält .

   ![Välj Ta bort i verktygsfältet Logikapp](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. När bekräftelserutan visas anger du logikappens namn och väljer **Ta bort**.

   ![Bekräfta att du tar bort logikappen](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure-portalen visar ett meddelande i huvudverktygsfältet i Azure som bekräftar om åtgärden lyckades eller misslyckades.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Ta bort flera logikappar

1. I Azure-portalen [hittar du de logikappar som du vill ta bort](#find-logic-app).

1. Markera de logikappar som du vill ta bort i kryssrutans kolumn. Välj **Ta bort**i verktygsfältet .

   ![Ta bort flera logikappar](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. När bekräftelserutan visas `yes`anger du och väljer **Ta bort**.

   ![Bekräfta att du tar bort logikapparna](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure-portalen visar ett meddelande i huvudverktygsfältet i Azure som bekräftar om åtgärden lyckades eller misslyckades.

## <a name="next-steps"></a>Nästa steg

* [Övervaka logikappar](monitor-logic-apps.md)
