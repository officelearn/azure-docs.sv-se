---
title: Använd Azure IoT Central-anslutningen i Microsoft Flow | Microsoft Docs
description: Använd IoT Central-anslutningen i Microsoft Flow för att utlösa arbets flöden och skapa, Hämta, uppdatera, ta bort enheter och köra kommandon i arbets flöden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: f1912b90df75f3d85f6ba5d730c723cf42803237
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930384"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Bygg arbets flöden med IoT Central Connector i Microsoft Flow

*Det här avsnittet gäller för byggare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Använd Microsoft Flow för att automatisera arbets flöden i många program och tjänster som företags användare förlitar sig på. Med hjälp av IoT Central Connector i Microsoft Flow kan du utlösa arbets flöden när en regel utlöses i IoT Central. I ett arbets flöde som har utlösts av IoT Central eller något annat program, kan du använda åtgärderna i IoT Central-anslutningen för att:
- Skapa en enhet
- Hämta enhets information
- Uppdatera en enhets egenskaper och inställningar
- Köra ett kommando på en enhet
- Ta bort en enhet

Kolla [Microsoft Flow mallar](https://aka.ms/iotcentralflowtemplates) som ansluter IoT Central till andra tjänster, till exempel mobila meddelanden och Microsoft team.

## <a name="prerequisites"></a>Krav

- Ett program som du betalar per användning
- Ett personligt Microsoft-konto eller arbets-eller skol konto för att använda Microsoft Flow ([Läs mer om Microsoft Flow-planer](https://aka.ms/microsoftflowplans))
- Ett arbets-eller skol konto för att använda Azure IoT Central-anslutningen

## <a name="trigger-a-workflow"></a>Utlös ett arbets flöde

Det här avsnittet visar hur du utlöser ett mobil meddelande i Flow-mobilappen när en regel utlöses i IoT Central. Du kan bygga hela arbets flödet i din IoT Central-app med hjälp av den inbäddade Microsoft Flow designer.

1. Börja med att [skapa en regel i IoT Central](howto-create-telemetry-rules.md). När du har sparat regel villkoren väljer du **åtgärden Microsoft Flow åtgärd** som en ny åtgärd. Ett dialog fönster öppnas där du kan konfigurera arbets flödet. IoT Central användar kontot som du är inloggad på kommer att användas för att logga in på Microsoft Flow.

    ![Skapa en ny Microsoft Flow-åtgärd](media/howto-add-microsoft-flow/createflowaction.png)

1. Du kommer att se en lista över arbets flöden som du har åtkomst till och som är kopplade till den här IoT Centrals regeln. Klicka på **utforska mallar** eller **Ny > Skapa från mall** och du kan välja bland någon av de tillgängliga mallarna. 

    ![Tillgängliga Microsoft Flow mallar](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Du uppmanas att logga in på kopplingarna i den mall som du har valt. 

    > [!NOTE]
    > Om du vill använda Azure IoT Central-anslutningen måste du logga in med ett Azure Active Directory konto (arbets-eller skol konto). Ett personligt konto, till exempel abc@outlook.com eller abc@live.com, stöds inte av Azure IoT Central-anslutningen.

    När du har loggat in på kopplingarna, kommer du att landa i designern för att bygga arbets flödet. Arbets flödet har en IoT Central-utlösare som har ditt program och din regel ifylld redan.

1. Du kan anpassa arbets flödet genom att anpassa den information som skickas till åtgärden och lägga till nya åtgärder. I det här exemplet är åtgärden **aviseringar – skicka mig ett mobil meddelande**. Du kan inkludera *dynamiskt innehåll* från IoT Centrals regeln och skicka viktig information som enhets namn och tidsstämpel till ditt meddelande.

    > [!NOTE]
    > Välj alternativet för att **Se mer** text i fönstret dynamiskt innehåll för att hämta mått-och egenskaps värden som utlöste regeln.

    ![Flödes redigerings åtgärd med dynamiskt fönster öppen](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. När du är klar med redigeringen av åtgärden väljer du **Spara**. Du dirigeras till arbets flödets översikts sida. Här kan du se körnings historiken och dela den med andra kollegor.

    > [!NOTE]
    > Om du vill att andra användare i din IoT Central-app ska redigera den här regeln måste du dela den med dem i Microsoft Flow. Lägg till sina Microsoft Flow konton som ägare i ditt arbets flöde.

1. Om du går tillbaka till IoT Central-appen ser du att den här regeln har en Microsoft Flow-åtgärd i avsnittet åtgärder.

Du kan också bygga arbets flöden med hjälp av IoT Central Connector direkt från Microsoft Flow. Du kan sedan välja vilken IoT Central app du vill ansluta till.

## <a name="create-a-device-in-a-workflow"></a>Skapa en enhet i ett arbets flöde

I det här avsnittet visas hur du skapar en ny enhet i IoT Central när du trycker på en knapp på en mobil enhet med hjälp av Microsoft Flow mobilappen. Du kan använda den här åtgärden i Flow för att integrera ERP-system som Dynamics med IoT Central genom att skapa en ny enhet när en enhet läggs till någon annan stans.

1. Börja med att skapa ett tomt arbets flöde i Microsoft Flow.

1. Sök efter **flödes knapp för mobilt** som en utlösare.

1. I den här utlösaren lägger du till en text inspelning med namnet **enhets namn**. Ändra beskrivningen så att den **nya enhetens enhets namn**anges.

1. Lägg till en ny åtgärd. Sök efter **Azure-IoT Central – skapa en enhets** åtgärd.

1. Välj ditt program och välj en enhets mal len för att skapa en enhet från i list rutorna. Du ser att åtgärden expanderar visar alla egenskaper och inställningar för enheten.

1. Välj fältet enhets namn. I fönstret dynamiskt innehåll väljer du **enhets namn**. Det här värdet skickas från indata som användaren anger via mobilappen och är namnet på den nya enheten i IoT Central. I det här exemplet är det enda obligatoriska fältet enhets namnet, som anges av den röda asterisken. En annan enhets mall kan ha flera obligatoriska fält som måste fyllas i för att skapa en ny enhet.

    ![Flödes åtgärd för att skapa enhets åtgärd dynamiskt fönster](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Valfritt Fyll i andra fält efter behov för att skapa nya enheter.

1. Spara slutligen arbets flödet.

1. Testa arbets flödet i Microsoft Flow mobilapp. Gå till fliken **knappar** i appen. Du bör se din knapp > Skapa ett nytt enhets arbets flöde. Ange namnet på den nya enheten och se hur det visas i IoT Central!

    ![Flöde skapa enhet för mobil skärm bild](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Uppdatera en enhet i ett arbets flöde

I det här avsnittet visas hur du uppdaterar enhets inställningar och egenskaper i IoT Central när du trycker på en knapp på en mobil enhet med hjälp av Microsoft Flow mobilappen.

1. Börja med att skapa ett tomt arbets flöde i Microsoft Flow.

1. Sök efter **flödes knapp för mobilt** som en utlösare.

1. I den här utlösaren lägger du till en indatatyp som en **plats** text inmatare som motsvarar en inställning eller egenskap som du vill ändra. Ändra beskrivnings texten.

1. Lägg till en ny åtgärd. Sök efter **Azure-IoT Central – uppdatera en enhets** åtgärd.

1. Välj ditt program i list rutan. Nu behöver du ett ID för den befintliga enhet som du vill uppdatera. 

    > [!NOTE] 
    > **Du måste använda det ID som finns i URL: en** på enhets informations sidan för enheten som du vill uppdatera. Enhets-ID: t som finns i enhets listans lista över enheter är inte den rätt som används i Microsoft Flow.

    ![IoT Central-ID från URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Du kan uppdatera enhetens namn. Om du vill uppdatera någon av enhetens egenskaper och inställningar måste du välja enhets mal len för den enhet som du vill uppdatera i list rutan för **enhets mal len** . Åtgärds panelen expanderar för att visa alla egenskaper och inställningar som du kan uppdatera.

    ![Flödes uppdatering enhets arbets flöde](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Välj var och en av de egenskaper och inställningar som du vill uppdatera. I fönstret dynamiskt innehåll väljer du motsvarande indatatyper från utlösaren. I det här exemplet sprids platsens värde nedåt för att uppdatera enhetens plats egenskap.

1. Spara slutligen arbets flödet.

1. Testa arbets flödet i Microsoft Flow mobilapp. Gå till fliken **knappar** i appen. Du bör se knappen – > Uppdatera ett enhets arbets flöde. Ange indata och se att enheten uppdateras i IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Hämta enhets information i ett arbets flöde

Du kan hämta enhets information genom att använda dess ID med hjälp av **Azure IoT Central – hämta en enhets** åtgärd. 
> [!NOTE] 
> **Du måste använda det ID som finns i URL: en** på enhets informations sidan för enheten som du vill uppdatera. Enhets-ID: t som finns i enhets listans lista över enheter är inte den rätt som används i Microsoft Flow.

Du kan få information som enhets namn, namn på enhets mall, egenskaps värden och inställnings värden för att skicka till senare åtgärder i arbets flödet. Här är ett exempel på ett arbets flöde som passerar tillsammans med egenskap svärdet för kund namn från en enhet till Microsoft Teams.

   ![Flöde för Hämta enhets arbets flöde](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Köra ett kommando på en enhet i ett arbets flöde
Du kan köra ett kommando på en enhet som anges med dess ID med hjälp av **Azure-IoT Central – köra en kommando** åtgärd. 

> [!NOTE] 
> **Du måste använda det ID som finns i URL: en** på enhets informations sidan för enheten som du vill uppdatera. Enhets-ID: t som finns i enhets listans lista över enheter är inte den rätt som används i Microsoft Flow.
    
Du kan välja kommandot för att köra och skicka i parametrarna för kommandot genom den här åtgärden. Här är ett exempel på ett arbets flöde som kör ett kommando för omstart av enhet från en knapp i Microsoft Flow mobilapp.

   ![Flöde för Hämta enhets arbets flöde](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Ta bort en enhet i ett arbets flöde

Du kan ta bort en enhet med dess ID med hjälp av **Azure IoT Central – ta bort en enhets** åtgärd. 
> [!NOTE] 
> **Du måste använda det ID som finns i URL: en** på enhets informations sidan för enheten som du vill uppdatera. Enhets-ID: t som finns i enhets listans lista över enheter är inte den rätt som används i Microsoft Flow.

Här är ett exempel på ett arbets flöde som tar bort en enhet vid en knapp tryckning i Microsoft Flow mobilapp.

   ![Flöde ta bort arbets flöde för enhet](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att skapa en anslutning till Azure IoT Central-anslutningen finns här några tips som hjälper dig.

1. Microsoft personliga konton (till exempel @hotmail.com, @live.com@outlook.com domäner) stöds inte för tillfället. Du måste använda ett arbets-eller skol konto för Azure Active Directory (AD).

2. Om du vill använda IoT Central anslutningen i Microsoft Flow måste du ha loggat in på IoT Central programmet minst en gång. Annars visas inte programmet i list rutorna i programmet.

3. Om du får ett fel när du använder ett Azure AD-konto kan du försöka öppna Windows PowerShell och köra följande cmdletarna som administratör.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Microsoft Flow för att bygga arbets flöden, är det föreslagna nästa steg att [Hantera enheter](howto-manage-devices.md).

