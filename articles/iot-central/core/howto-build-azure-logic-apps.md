---
title: Bygg arbets flöden med IoT Central Connector i Azure Logic Apps | Microsoft Docs
description: Använd IoT Central-anslutningen i Azure Logic Apps för att utlösa arbets flöden och skapa, uppdatera och ta bort enheter i arbets flöden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3625d7374090bca73e2f054f7da4b58e951e7719
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990274"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Bygg arbets flöden med IoT Central Connector i Azure Logic Apps

*Det här avsnittet gäller för byggare och administratörer.*

Använd Azure Logic Apps för att bygga automatiserade skalbara arbets flöden som integrerar appar och data över moln tjänster och lokala system. Azure Logic Apps har många anslutningar över flera Azure-tjänster, Microsoft-tjänster och andra SaaS-produkter (program vara som en tjänst). Med hjälp av IoT Central Connector i Azure Logic Apps kan du utlösa arbets flöden när en regel utlöses i IoT Central. Du kan också använda åtgärderna i IoT Central Connector för att skapa en enhet, uppdatera en enhets egenskaper och inställningar eller ta bort en enhet.

Du kan använda IoT Central anslutningen i Microsoft Flow. Både Azure Logic Apps och Microsoft Flow är designer-första integrerings tjänster, men det är en aning skilda mål grupper. Flow ger alla kontors anställda möjlighet att bygga affärs arbets flöden som de behöver. Logic Apps ger IT-proffs möjlighet att bygga upp de integreringar som de behöver för att ansluta data. Jämför flöde och Logic Apps [här](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Lär dig mer om hur du skapar arbets flöden med IoT Central Connector i Microsoft Flow [här](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Krav

- Ett program som skapats med en standard pris plan
- Ett Azure-konto och-prenumeration för att skapa och hantera Logic Apps

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Utlös ett arbets flöde när en regel utlöses

I det här avsnittet visas hur du skickar ett meddelande till Microsoft Teams när en regel utlöses. Du kan konfigurera arbets flödet så att det använder andra anslutningar för att till exempel skicka en händelse till Händelsehubben, skapa ett nytt arbets objekt i Azure DevOps eller infoga en ny rad i SQL Server.

1. Börja med att [skapa en regel i IoT Central](howto-create-telemetry-rules.md). När du har sparat regel villkoren väljer du panelen **Azure Logic Apps** som en ny åtgärd. Välj **skapa i Azure Portal**. Du kommer till Azure Portal att skapa en ny Logic app. Du kan behöva logga in på ditt Azure-konto.

1. Ange den information som krävs för att skapa en ny Logic-app. Du kan välja en Azure-prenumeration för att etablera din nya Logic app i. Det behöver inte vara samma prenumeration som din IoT Central-app skapades i. Välj **Skapa**.

    ![Skapa Logic app i Azure Portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. När din Logic app har skapats, kommer du automatiskt att navigera till Logic Apps designer. Välj **Tom Logic app**. 

    ![Skapa en tom logikapp](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. I designern söker du efter "IoT Central" och väljer **när en regel** utlöses. Logga in på anslutningen med det konto som du loggar in på IoT Central-appen med.

    ![Logga in på IoT Central koppling](./media/howto-build-azure-logic-apps/addtrigger.png)

1. När du har loggat in bör du se fälten. Välj **program** och **regel** i list rutorna.

    ![Välj program och regel](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Lägg till en ny åtgärd. Sök efter **meddelande team** och välj **publicera ett meddelande** från Microsoft Teams-anslutningsprogrammet. Logga in på anslutningen med det konto som du använder i Microsoft Teams.

1. I åtgärden väljer du **team** och **kanal**. Fyll i **meddelande** fältet med det du vill att varje meddelande ska säga. Du kan inkludera *dynamiskt innehåll* från IoT Centrals regeln och skicka viktig information som enhets namn och tidsstämpel till ditt meddelande.
    > [!NOTE]
    > Välj alternativet för att **Se mer** text i fönstret dynamiskt innehåll för att hämta mått-och egenskaps värden som utlöste regeln.

    ![Redigerings åtgärd för Logic app med dynamiskt fönster öppen](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. När du är klar med redigeringen av åtgärden väljer du **Spara**.

1. Om du går tillbaka till din IoT Central-app, ser du att den här regeln har en Azure Logic Apps-åtgärd i avsnittet åtgärder.

Du kan alltid börja skapa ett arbets flöde med hjälp av IoT Central Connector i Logic Apps i Azure Portal. Du kan sedan välja vilken IoT Central app och vilken regel som ska anslutas till, och den fungerar fortfarande på samma sätt. Du behöver inte starta från sidan IoT Central regler varje tillfälle.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Skapa, uppdatera och ta bort en enhet i ett arbets flöde

När du skapar ett arbets flöde i din Logic app kan du lägga till en åtgärd med hjälp av IoT Central-anslutningen. Tillgängliga åtgärder är **skapa en enhet**, **Uppdatera en enhet**och **ta bort en enhet**.

> [!NOTE]
> För att **Uppdatera en enhet** och **ta bort en enhet**behöver du ett ID för den befintliga enhet som du vill uppdatera eller ta bort. Du kan hämta ID: t för den IoT Central enheten i **Device Explorer**

![Enhets-ID för IoT Central enhets Utforskaren](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Microsoft Flow för att bygga arbets flöden, är det föreslagna nästa steg att [Hantera enheter](howto-manage-devices.md).
