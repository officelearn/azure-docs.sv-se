---
title: Skapa arbetsflöden med Azure IoT Central connector i Microsoft Flow | Microsoft Docs
description: Använd IoT Central-anslutningen i Microsoft Flow att utlösa arbetsflöden direkt och skapa, hämta, uppdatera, ta bort enheter och köra kommandon i arbetsflöden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 5d1e9941244defbf84b20f95e9f2e0402bbe19f2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64693598"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Skapa arbetsflöden med IoT Central-connector i Microsoft Flow

*Det här avsnittet gäller builders och administratörer.*

Använd Microsoft Flow för att automatisera arbetsflöden i många program och tjänster som företagsanvändare förlitar sig på. Använder IoT Central-connector i Microsoft Flow kan utlösa du arbetsflöden när en regel utlöses i IoT Central. Du kan använda åtgärderna i IoT Central connector i ett arbetsflöde som utlöses av IoT Central eller något annat program:
- Skapa en enhet
- Hämta enhetsinformation
- Uppdatera enhetens egenskaper och inställningar
- Köra ett kommando på en enhet
- Ta bort en enhet

Kolla in [mallarna Microsoft Flow](https://aka.ms/iotcentralflowtemplates) som ansluta IoT Central till andra tjänster, till exempel mobila meddelanden och Microsoft Teams.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett program för betala per användning
- Ett Microsoft personlig eller arbets eller skolkonto för att logga in på Flow ([Läs mer om Microsoft Flow prenumerationsavtal](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Utlös ett arbetsflöde

Det här avsnittet visar hur du utlöser ett mobilmeddelande i Flow-mobilappen när en regel utlösare i IoT Central. Du kan skapa den här hela arbetsflödet i din IoT Central-app med hjälp av inbäddad Microsoft Flow-designern.

1. Börja med att [skapar en regel i IoT Central](howto-create-telemetry-rules.md). När du har sparat villkor, Välj den **Microsoft Flow-åtgärden** som en ny åtgärd. En dialogruta öppnas för dig att konfigurera ditt arbetsflöde. IoT Central-användarkonto som du är inloggad på används för att logga in på Microsoft Flow.

    ![Skapa en ny Microsoft Flow-åtgärd](media/howto-add-microsoft-flow/createflowaction.png)

1. Du ser en lista över arbetsflöden tha du har åtkomst till och är kopplade till regeln IoT Central. Klicka på **utforska mallar** eller **New > Skapa från mall** och du kan välja från någon av de tillgängliga mallarna. 

    ![Mall för Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates.png)

1. Du uppmanas att logga in på anslutningar i den valda mallen. När anslutningarna har loggat in, hamnar i designer för att bygga ditt arbetsflöde. Arbetsflödet har en IoT Central-utlösare som har programmet och regeln har redan fyllt i.

1. Du kan anpassa arbetsflödet genom att anpassa den information som skickas till åtgärden och lägga till nya åtgärder. Det här exemplet åtgärden är **meddelanden – skicka ett SMS**. Du kan inkludera *dynamiskt innehåll* från regeln IoT Central, skicka längs viktig information, till exempel enhetens namn och tidsstämpel till ditt meddelande.

    > [!NOTE]
    > Välj den **mer** text i fönstret för dynamiskt innehåll för att hämta mått och egenskapen värden som utlöste regeln.

    ![Flow Redigera åtgärd med dynamiska fönstret öppet](./media/howto-add-microsoft-flow/flowdynamicpane.png)

1. När du är klar redigera åtgärden Välj **spara**. Du omdirigeras till sidan med översikten över ditt arbetsflöde. Här kan du se körningshistoriken och dela den med andra kollegor.

    > [!NOTE]
    > Om du vill att andra användare i din IoT Central-app för att redigera den här regeln, måste du dela den med dem i Microsoft Flow. Lägga till sina Microsoft Flow-konton som ägare i arbetsflödet.

1. Om du går tillbaka till din IoT Central-app, visas den här regeln har en Microsoft Flow-åtgärd i området för åtgärder.

Du kan också skapa arbetsflöden med IoT Central-kopplingen direkt från Microsoft Flow. Du kan sedan välja vilken IoT Central-app för att ansluta till.

## <a name="create-a-device-in-a-workflow"></a>Skapa en enhet i ett arbetsflöde

Det här avsnittet visar hur du skapar en ny enhet i IoT Central vid push-installation för en knapp på en mobil enhet med hjälp av Microsoft Flow-mobilappen. Du kan använda den här åtgärden i Flow för att integrera ERP-system som Dynamics med IoT Central genom att skapa en ny enhet när en enhet har lagts till någon annanstans.

1. Börja med att skapa ett tomt arbetsflöde i Microsoft Flow.

1. Sök efter **flödesknapp för mobil** som en utlösare.

1. Lägg till textinmatning anropas i den här utlösaren **enhetsnamn**. Ändra en beskrivande text för att vara **ange enhetsnamn för den nya enheten**.

1. Lägg till en ny åtgärd. Sök efter den **Azure IoT Central - skapa en enhet** åtgärd.

1. Välj programmet och välj en mall för enheten du skapar en enhet från i listrutorna. Du ser åtgärden som utökas för att visa alla egenskaper och inställningar för enheten.

1. Välj fältet enhetsnamn. Rutan med dynamiskt innehåll, Välj **enhetsnamn**. Det här värdet skickas från Indataposten användaren anger via mobilappen och är namnet på den nya enheten i IoT Central. I det här exemplet är det enda obligatoriska fältet namnet på enheten som anges med röd asterisk. Mall för en annan enhet kan ha flera obligatoriska fält som måste fyllas i för att skapa en ny enhet.

    ![Flow skapar dynamiska Enhetsfönstret för åtgärden](./media/howto-add-microsoft-flow/flowcreatedevice.png)

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

1. Välj ditt program i listrutan. Nu behöver du ett ID för den befintliga enheten som du vill uppdatera. 

    > [!NOTE] 
    > **Du måste använda ID som hittades i URL: en** på informationssidan för enheten på den enhet som du vill uppdatera. Enhets-ID hittades i den enhetsutforskare lista över enheter är inte rätt som ska användas i Microsoft Flow.

    ![IoT Central-ID från URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Du kan uppdatera namnet på enheten. Om du vill uppdatera någon av enhetens egenskaper och inställningar, måste du välja mallen enhet på den enhet som du vill uppdatera i den **enheten mallen** listrutan. Åtgärd-panelen expanderar för att visa alla egenskaper och inställningar som du kan uppdatera.

    ![Flow arbetsflödet för enhet](./media/howto-add-microsoft-flow/flowupdatedevice.png)

1. Välj var och en av de egenskaper och inställningar som du vill uppdatera. Rutan med dynamiskt innehåll, Välj den motsvarande indata från utlösaren. I det här exemplet sprids platsvärdet ned för att uppdatera enhetens Platsegenskapen.

1. Slutligen kan spara ditt arbetsflöde.

1. Testa ditt arbetsflöde i Microsoft Flow-mobilappen. Gå till den **knappar** fliken i appen. Du bör se din knapp -> Uppdatera ett arbetsflöde för enheten. Ange indata, och se enheten uppdateras i IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Hämta enhetsinformation i ett arbetsflöde

Du kan få information om enhet med dess ID den **Azure IoT Central - hämta en enhet** åtgärd. 
> [!NOTE] 
> **Du måste använda ID som hittades i URL: en** på informationssidan för enheten på den enhet som du vill uppdatera. Enhets-ID hittades i den enhetsutforskare lista över enheter är inte rätt som ska användas i Microsoft Flow.

Du kan få information som enhetsnamn, enhetsnamn för mallen, egenskapsvärden och värden ska skickas till senare åtgärder i arbetsflödet. Här är ett exempel på ett arbetsflöde som skickar med kundnamn egenskapens värde från en enhet till Microsoft Teams.

   ![Arbetsflöde för Flow get-enhet](./media/howto-add-microsoft-flow/flowgetdevice.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Köra ett kommando på en enhet i ett arbetsflöde
Du kan köra ett kommando på en enhet som anges av dess ID med hjälp av den **Azure IoT Central - köra ett kommando** åtgärd. 

> [!NOTE] 
> **Du måste använda ID som hittades i URL: en** på informationssidan för enheten på den enhet som du vill uppdatera. Enhets-ID hittades i den enhetsutforskare lista över enheter är inte rätt som ska användas i Microsoft Flow.
    
Du kan välja kommandot för att köra och skicka parametrar för kommandot via den här åtgärden. Här är ett exempel på ett arbetsflöde som kör ett kommando för omstart av enheten från en knapp i Microsoft Flow-mobilappen.

   ![Arbetsflöde för Flow get-enhet](./media/howto-add-microsoft-flow/flowrunacommand.png)

## <a name="delete-a-device-in-a-workflow"></a>Ta bort en enhet i ett arbetsflöde

Du kan ta bort en enhet med dess ID med hjälp av den **Azure IoT Central - ta bort en enhet** åtgärd. 
> [!NOTE] 
> **Du måste använda ID som hittades i URL: en** på informationssidan för enheten på den enhet som du vill uppdatera. Enhets-ID hittades i den enhetsutforskare lista över enheter är inte rätt som ska användas i Microsoft Flow.

Här är ett exempel på ett arbetsflöde som tar bort en enhet på en knapptryckning i Microsoft Flow-mobilappen.

   ![Arbetsflöde för Flow ta bort enhet](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att skapa en anslutning till Azure IoT Central anslutningen följer här några tips om hur du.

1. Personliga Microsoft-konton (till exempel @hotmail.com, @live.com, @outlook.com domäner) stöds inte just nu. Du måste använda ett Azure Active Directory (AD)-arbetskonto eller skolkonto.

2. Om du vill använda IoT Central-anslutningen i Microsoft Flow, måste du har loggat in minst en gång programmet IoT Central. Annars visas programmet inte programmet listrutorna.

3. Om du får ett fel när du använder en Azure AD-konto, försök att öppna Windows PowerShell och kör följande cmdletar har som administratör.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Microsoft Flow för att skapa arbetsflöden, föreslagna nästa steg är att [hantera enheter](howto-manage-devices.md).

