---
title: Skapa arbetsflöden med IoT Central-connector i Microsoft Flow | Microsoft Docs
description: Använd IoT Central-anslutningen i Microsoft Flow att utlösa arbetsflöden direkt och skapa, uppdatera och ta bort enheter i arbetsflöden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: article
ms.service: azure-iot-central
manager: peterpr
ms.openlocfilehash: 9d8a5dfa452c0cf8f6e8fdb372e508d1d9ba25b6
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465647"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Skapa arbetsflöden med IoT Central-connector i Microsoft Flow

*Det här avsnittet gäller builders och administratörer.*

Använd Microsoft Flow för att automatisera arbetsflöden i många program och tjänster som företagsanvändare förlitar sig på. Använder IoT Central-connector i Microsoft Flow kan utlösa du arbetsflöden när en regel utlöses i IoT Central. I ett arbetsflöde som utlöses av IoT Central eller något annat program, kan du använda åtgärderna i IoT Central-anslutningsappen skapar en enhet, uppdatera enhetens egenskaper och inställningar eller ta bort en enhet. Kolla in [mallarna Microsoft Flow](https://aka.ms/iotcentralflowtemplates) som ansluta IoT Central till andra tjänster, till exempel mobila meddelanden och Microsoft Teams.

> [!NOTE] 
> Du måste logga in på Microsoft Flow med ett Microsoft-personal eller arbets- eller skolkonto. Läs mer om Microsoft Flow prenumerationsavtal [här](https://aka.ms/microsoftflowplans).

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Utlös ett arbetsflöde när en regel utlöses

Det här avsnittet visar hur du utlöser ett mobilmeddelande i Flow-mobilappen när en regel utlöses i IoT Central.

1. Börja med att [skapar en regel i IoT Central](howto-create-telemetry-rules.md). När du har sparat villkor klickar du på den **Microsoft Flow-åtgärden** som en ny åtgärd. En ny flik eller fönster ska öppnas i din webbläsare tar dig till Microsoft Flow.

    ![Skapa en ny Microsoft Flow-åtgärd](media/howto-add-microsoft-flow/createflowaction.PNG)

1. Logga in på Microsoft Flow. Detta behöver inte vara samma konto som det som du använder i IoT Central. Kommer du att hamna på en översiktssida som visar en IoT Central-koppling som ansluter till en anpassad åtgärd.

1. Logga in på den IoT Central-anslutningen och klicka på **Fortsätt**. Du kommer till Microsoft Flow-designern att bygga ditt arbetsflöde. Arbetsflödet har en IoT Central-utlösare som har programmet och regeln har redan fyllt i.

1. Välj **+ nytt steg** och **Lägg till en åtgärd**. Nu kan du lägga till alla åtgärder som du vill ditt arbetsflöde. Exempelvis kan vi skicka ett SMS. Sök efter **meddelande**, och välj **meddelanden – skicka ett SMS**.

1. I åtgärden, fyller du i textfältet med vad du vill att dina meddelanden att säga. Du kan inkludera *dynamiskt innehåll* från regeln IoT Central, skicka längs viktig information, till exempel enhetens namn och tidsstämpel till ditt meddelande.

    > [!NOTE]
    > Klicka på ”se mer”-texten i fönstret för dynamiskt innehåll för att hämta mått och egenskapsvärden som utlöste regeln.

    ![Flow Redigera åtgärd med dynamiska fönstret öppet](./media/howto-add-microsoft-flow/flowdynamicpane.PNG)

1. När du är klar redigering åtgärden, klickar du på **spara**. Du omdirigeras till sidan med översikten över ditt arbetsflöde. Här kan du se körningshistoriken och dela den med andra kollegor.

    > [!NOTE]
    > Om du vill att andra användare i din IoT Central-app för att redigera den här regeln, måste du dela den med dem i Microsoft Flow. Lägga till sina Microsoft Flow-konton som ägare i arbetsflödet.

1. Om du går tillbaka till din IoT Central-app, visas den här regeln har en Microsoft Flow-åtgärd i området åtgärder.

Du kan alltid börja skapa ett arbetsflöde med IoT Central-kopplingen i Microsoft Flow. Du kan sedan välja vilken IoT Central-app och vilken regel för att ansluta till.

## <a name="create-a-device-in-a-workflow"></a>Skapa en enhet i ett arbetsflöde

Det här avsnittet visar hur du skapar en ny enhet i IoT Central vid push-installation för en knapp på en mobil enhet med hjälp av Microsoft Flow-mobilappen. Du kan använda den här åtgärden i Flow för att integrera ERP-system som Dynamics med IoT Central genom att skapa en ny enhet när en enhet har lagts till någon annanstans.

1. Börja med att skapa ett tomt arbetsflöde i Microsoft Flow.

1. Sök efter **flödesknapp för mobil** som en utlösare.

1. Lägg till textinmatning anropas i den här utlösaren **enhetsnamn**. Ändra en beskrivande text för att vara **ange enhetsnamn för den nya enheten**.

1. Lägg till en ny åtgärd. Sök efter den **Azure IoT Central - skapa en enhet** åtgärd.

1. Välj programmet och välj en mall för enheten du skapar en enhet från i listrutorna. Du ser åtgärden som utökas för att visa alla egenskaper och inställningar för enheten.

1. Välj fältet enhetsnamn. Rutan med dynamiskt innehåll, Välj **enhetsnamn**. Det här värdet skickas från användaren anger indata via mobilappen och kommer att namnet på den nya enheten i IoT Central. I det här exemplet är det enda obligatoriska fältet namnet på enheten som anges med röd asterisk. Mall för en annan enhet kan ha flera obligatoriska fält som måste fyllas i för att skapa en ny enhet.

    ![Flow skapar dynamiska Enhetsfönstret för åtgärden](./media/howto-add-microsoft-flow/flowcreatedevice.PNG)
1. (Valfritt) Fyll i andra fält som du vill för skapa nya enheter.

1. Slutligen kan spara ditt arbetsflöde.

1. Testa ditt arbetsflöde i Microsoft Flow-mobilappen. Gå till den **knappar** fliken i appen. Du bör se din knapp -> Skapa ett nytt arbetsflöde för enheten. Ange namnet på den nya enheten och titta på den dyker upp i IoT Central!

    ![Flow skapar mobila enheter-skärmbild](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Uppdatera en enhet i ett arbetsflöde

Det här avsnittet beskrivs hur du uppdaterar inställningar och egenskaper i IoT Central vid push-installation för en knapp på en mobil enhet med hjälp av Microsoft Flow-mobilappen.

1. Börja med att skapa ett tomt arbetsflöde i Microsoft Flow.

1. Sök efter **flödesknapp för mobil** som en utlösare.

1. Lägg till indata som i den här åtgärden en **plats** textinmatning som motsvarar en inställning eller egenskapen som du vill ändra. Ändra en beskrivande text.

1. Lägg till en ny åtgärd. Sök efter den **Azure IoT Central - uppdatera en enhet** åtgärd.

1. Välj ditt program i listrutan. Nu behöver du ett ID för den befintliga enheten som du vill uppdatera. ID för IoT Central-enhet får du i webbläsarens URL-Adressen.

    ![IoT Central device explorer enhets-ID](./media/howto-add-microsoft-flow/iotcdeviceid.PNG)

1. Du kan uppdatera namnet på enheten. Om du vill uppdatera någon av enhetens egenskaper och inställningar, måste du välja mallen enhet på den enhet som du vill uppdatera i den **enheten mallen** listrutan. Åtgärd-panelen expanderar för att visa alla egenskaper och inställningar som du kan uppdatera.

1. Välj var och en av de egenskaper och inställningar som du vill uppdatera. Rutan med dynamiskt innehåll, Välj den motsvarande indata från utlösaren. I det här exemplet sprids platsvärdet ned för att uppdatera enhetens Platsegenskapen.

1. Slutligen kan spara ditt arbetsflöde.

1. Testa ditt arbetsflöde i Microsoft Flow-mobilappen. Gå till den **knappar** fliken i appen. Du bör se din knapp -> Uppdatera ett arbetsflöde för enheten. Ange indata, och se enheten uppdateras i IoT Central!

## <a name="delete-a-device-in-a-workflow"></a>Ta bort en enhet i ett arbetsflöde

Du kan ta bort en enhet med dess enhet-ID med den **Azure IoT Central - ta bort en enhet** åtgärd. Här är ett exempel på ett arbetsflöde som tar bort en enhet på en knapptryckning i Microsoft Flow-mobilappen.

   ![Arbetsflöde för Flow ta bort enhet](./media/howto-add-microsoft-flow/flowdeletedevice.PNG)
    
## <a name="troubleshooting"></a>Felsökning

Om du har problem med att skapa en anslutning till Azure IoT Central anslutningen följer här några tips om hur du.

1. Personliga Microsoft-konton (till exempel @hotmail.com, @live.com, @outlook.com domäner) stöds inte just nu. Du måste använda ett AAD-arbetskonto eller skolkonto.

2. Om du vill använda IoT Central-anslutningen i Microsoft Flow, måste du har loggat in minst en gång programmet IoT Central. Annars visas programmet inte programmet listrutorna.

3. Om du får ett fel när du använder ett AAD-konto, försök att öppna Windows PowerShell och kör följande cmdletar har som administratör.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du använder Microsoft Flow för att skapa arbetsflöden, föreslagna nästa steg är att [hantera enheter](howto-manage-devices.md).

