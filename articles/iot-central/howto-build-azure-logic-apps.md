---
title: Skapa arbetsflöden med IoT Central-anslutningsapp i Azure Logic Apps | Microsoft Docs
description: Använd IoT Central-anslutningsapp i Azure Logic Apps att utlösa arbetsflöden direkt och skapa, uppdatera och ta bort enheter i arbetsflöden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 635c8d0f149895798eece8cf3b48712ab74374ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887140"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Skapa arbetsflöden med IoT Central-anslutningsapp i Azure Logic Apps

*Det här avsnittet gäller builders och administratörer.*

Använd Azure Logic Apps för att skapa automatiska skalbara arbetsflöden som integrerar appar och data i molntjänster och lokala system. Med Azure Logic Apps har många anslutningsappar för många Azure-tjänster, Microsoft-tjänster och andra Software-As-A-Service (SaaS)-produkter. Använda IoT Central-anslutningsapp i Azure Logic Apps kan utlösa du arbetsflöden när en regel utlöses i IoT Central. Du kan också använda åtgärderna i IoT Central-anslutningsappen skapar en enhet, uppdatera enhetens egenskaper och inställningar eller ta bort en enhet.

Du kan använda IoT Central-anslutningen i Microsoft Flow. Både Azure Logic Apps och Microsoft Flow är designer först integrationstjänster, men något annorlunda målgrupper. Flow kontorspersonalen att skapa affärs-arbetsflöden som de behöver. Logic Apps gör det möjligt för IT-proffs att skapa integreringar som de behöver för att ansluta data. Jämföra Flow och Logic Apps [här](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Lär dig mer om hur du skapar arbetsflöden med IoT Central-connector i Microsoft Flow [här](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett program för betala per användning
- En Azure-konto och prenumeration för att skapa och hantera Logic apps

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Utlös ett arbetsflöde när en regel utlöses

Det här avsnittet visar hur du publicera ett meddelande till Microsoft Teams när en regel utlöses. Du kan konfigurera ditt arbetsflöde om du vill använda andra anslutningsappar för att göra saker som att skicka en händelse till din Event hub, skapa ett nytt Azure DevOps-arbetsobjekt eller infoga en ny rad i SQLServer.

1. Börja med att [skapar en regel i IoT Central](howto-create-telemetry-rules.md). När du har sparat villkor, Välj den **Azure Logic Apps** panelen som en ny åtgärd. Välj **skapa i Azure-portalen**. Du tas till Azure-portalen för att skapa en ny logikapp. Du kan behöva logga in på ditt Azure-konto.

1. Ange informationen som krävs för att skapa en ny logikapp. Du kan välja en Azure-prenumeration att etablera den nya logikappen till. Det behöver inte vara samma prenumeration som IoT Central-appen skapades i. Välj **Skapa**.

    ![Skapa logikapp i Azure-portalen](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. När logikappen har skapats är du automatiskt navigerat till Logic Apps Designer. Välj **tom Logikapp**. 

    ![Skapa en tom logikapp](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. I designern, söker du efter ”iot central” och välj den **när en regel utlöses** utlösaren. Logga in på anslutningen med det konto som du loggar in på din IoT Central-app med.

    ![Logga in på IoT Central-anslutningsapp](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Efter inloggningen lyckas, bör du se fält som visas. Välj den **programmet** och **regeln** från listrutorna.

    ![Välj program och regel](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Lägg till en ny åtgärd. Sök efter **publicera meddelande team** och välj **publicera ett meddelande** från Microsoft Teams-anslutningen. Logga in på anslutningen med det konto som används i Microsoft Teams.

1. I åtgärden, väljer du den **Team** och **kanal**. Fyll i den **meddelande** med vad du vill att varje meddelande att säga. Du kan inkludera *dynamiskt innehåll* från regeln IoT Central, skicka längs viktig information, till exempel enhetens namn och tidsstämpel till ditt meddelande.
    > [!NOTE]
    > Välj den **mer** text i fönstret för dynamiskt innehåll för att hämta mått och egenskapen värden som utlöste regeln.

    ![Logic app redigering åtgärd med dynamiska rutan Öppna](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. När du är klar redigera åtgärden Välj **spara**.

1. Om du går tillbaka till din IoT Central-app, visas den här regeln har en Azure Logic Apps-åtgärd i området åtgärder.

Du kan alltid börja skapa ett arbetsflöde med IoT Central-anslutningsapp i Logic Apps i Azure Portal. Du kan sedan välja vilken IoT Central-app och vilken regel för att ansluta till och den fortfarande fungerar på samma sätt. Det finns inget behov av att starta från sidan IoT Central regler varje gång.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Skapa, uppdatera och ta bort en enhet i ett arbetsflöde

När du skapar ett arbetsflöde i din logikapp kan du lägga till en åtgärd med IoT Central-kopplingen. De tillgängliga åtgärderna i **skapar du en**, **uppdatera en enhet**, och **ta bort en enhet**.

> [!NOTE]
> För **uppdatera en enhet** och **ta bort en enhet**, du behöver ett ID för den befintliga enheten som du vill uppdatera eller ta bort. Du kan hämta ID för IoT Central-enheten i den **Device Explorer**

![IoT Central device explorer enhets-ID](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Microsoft Flow för att skapa arbetsflöden, föreslagna nästa steg är att [hantera enheter](howto-manage-devices.md).
