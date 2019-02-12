---
title: Anpassa operatörsvyerna i Azure IoT Central | Microsoft Docs
description: Som byggare anpassar du operatörsvyerna i Azure IoT Central-programmet.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 267a619fe32a8d4af0ee9cc8a5001d7a321c3098
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765162"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Självstudier: Anpassa operatörsvyn i Azure IoT Central (Ny UI-design)

Den här självstudien visar hur du som är byggare anpassar operatörsvyn för programmet. När du som byggare gör en ändring i programmet kan du förhandsgranska operatörsvyn i Microsoft Azure IoT Central.

I den här självstudien anpassar du programmet så att det visar relevant information om en ansluten luftkonditioneringsenheten för en operatör. Med dina anpassningar kan operatören hantera de luftkonditioneringsenheter som är anslutna till programmet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera enhetens instrumentpanel
> * Konfigurera layouten för dina enhetsinställningar
> * Konfigurera layouten för dina enhetsegenskaper
> * Förhandsgranska enheten som operatör
> * Konfigurera standardstartsidan
> * Förhandsgranska standardstartsidan som operatör

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du slutföra de två föregående självstudierna:

* [Definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="configure-your-device-dashboard"></a>Konfigurera enhetens instrumentpanel

Som byggare kan du definiera vilken information som ska visas på en enhetsinstrumentpanel. I självstudien [Definiera en ny enhetstyp i programmet](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) lade du till ett linjediagram och annan information till instrumentpanelen **Ansluten luftkonditioneringsenhet**.

1. Om du vill redigera enhetsmallen **Ansluten luftkonditioneringsenhet** väljer du **Enhetsmallar** i den vänstra navigeringsmenyn:

    ![Sidan Enhetsmallar](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. Om du vill anpassa enhetsinstrumentpanelen klickar du på **Ansluten luftkonditioneringsenhet (1.0.0)** som du skapade i självstudien [Definiera en ny enhetstyp i programmet](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

3. Om du vill redigera instrumentpanelen väljer fliken **Instrumentpanel**.

4. Om du vill lägga till en KPI-panel (Key Performance Indicator) på instrumentpanelen väljer du **KPI**:

    Använd informationen i följande tabell för att definiera KPI:n:

    | Inställning     | Värde |
    | ----------- | ----- |
    | Namn        | Högsta temperatur |
    | Tidsintervall  | Senaste veckan |
    | Typ av mått | Telemetri |
    | Mått | temperatur |
    | Sammansättning | Maximal |
    | Synlighet  | Enabled |

    ![Lägga till KPI](media/tutorial-customize-operator-experimental/addkpi.png)

5. Klicka på **Spara**. Du kan nu se KPI-panelen på instrumentpanelen:

    ![KPI-panel](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. Om du vill flytta eller ändra storlek för en panel på instrumentpanelen, flyttar du muspekaren över panelen. Du kan dra panelen till en ny plats eller ändra dess storlek.

## <a name="configure-your-settings-layout"></a>Konfigurera layouten för dina inställningar

Som byggare kan du också konfigurera operatörsvyn för enhetsinställningarna. En operatör använder fliken för enhetsinställningar till att konfigurera en enhet. Till exempel kan en operatör använda inställningsfliken för att ange måltemperaturen för den anslutna luftkonditioneringsenheten.

1. Om du vill redigera inställningslayouten för din anslutna luftkonditioneringsenhet väljer du fliken **Inställningar**.

2. Du kan flytta och ändra storlek på inställningspanelerna:

    ![Redigera inställningslayouten](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurera layouten för dina egenskaper

Du kan också konfigurera operatörsvyn för enhetsegenskaperna, förutom instrumentpanelen och inställningar. En operatör använder fliken för enhetsegenskaper till att hantera enhetsmetadata. Till exempel använder operatören egenskapsfliken för att visa en enhets serienummer eller uppdatera tillverkarens kontaktuppgifter.

1. Om du vill redigera egenskapslayouten för din anslutna luftkonditioneringsenhet väljer du fliken **Egenskaper**.

2. Du kan flytta och ändra storlek på egenskapsfälten:

    ![Redigera egenskapslayouten](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Förhandsgranska den anslutna luftkonditioneringsenheten som operatör

Du använder sidan **Enhetsmallar** till att anpassa flikarna för instrumentpanel, inställningar och egenskaper för en operatör. Du använder sidan **Device Explorer** till att visa och använda enhetsmallen.

1. Om du vill visa och använda mallen för ansluten luftkonditioneringsenhet som operatör går du till sidan **Device Explorer** och väljer den simulerade enhet som IoT Central genererade från din mall:

    ![Visa och använda enhetsmallen](media/tutorial-customize-operator-experimental/usetemplate.png)

2. Om du vill uppdatera platsen för den här enheten väljer du **Egenskaper** och redigerar värdet i platspanelen. Klicka sedan på **Spara**:

    ![Redigera ett egenskapsvärde](media/tutorial-customize-operator-experimental/editproperty.png)

3. Om du vill skicka en inställning till din anslutna luftkonditionering väljer du **Inställningar** och ändrar inställningsvärdet i panelen. Välj sedan **Uppdatera**:

    ![Skicka inställningen till enheten](media/tutorial-customize-operator-experimental/sendsetting.png)

    När enheten får det nya inställningsvärdet, visas inställningen som **synkroniserad** i panelen.

4. Som operatör kan du se enhetsinstrumentpanelen som konfigurerats av byggaren:

    ![Operatörsvy för enhetsinstrumentpanelen](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurera standardstartsidan

När en byggare eller operatör loggar in på ett Azure IoT Central-program, ser de en startsida. Som byggare kan du konfigurera innehållet på startsidan så att det mest användbara och relevanta innehållet visas för operatören.

1. Anpassa standardstartsidan genom att gå till sidan **Start** och redigera **Redigera** längst upp till höger på sidan. En panel skjuts ut från vänster med en lista med objekt som du kan lägga till på sidan **Start**:

    ![Sidan Application Builder](media/tutorial-customize-operator-experimental/builderhome.png)

2. Om du vill anpassa sidan **Start** lägger du till paneler från **Biblioteket**. Välj **Länk** och lägg till information om din organisations webbplats. Klicka sedan på **Spara**:

    ![Lägga till en länk på startsidan](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > Du kan också lägga till länkar på sidor i ditt Azure IoT Central-program. Du kan till exempel lägga till en länk på en enhetsinstrumentpanel eller inställningssida.

3. Dessutom kan du välja **Bild** och ladda upp en bild som ska visas på startsidan. Bilden kan innehålla en URL som du kommer till när du klickar på den:

    ![Lägga till en bild på startsidan](media/tutorial-customize-operator-experimental/addimage.png)

    Läs mer i [Hur du förbereder och laddar upp bilder till ditt Azure IoT Central-program](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="preview-the-default-home-page-as-an-operator"></a>Förhandsgranska standardstartsidan som operatör

Om du vill förhandsgranska startsidan som operatör klickar du på **Klar** längst upp till höger på sidan:

![Växla designläge](media/tutorial-customize-operator-experimental/operatorviewhome.png)

Du kan klicka på länken och bildpanelerna för att gå till de URL:er som du angav som byggare.

## <a name="next-steps"></a>Nästa steg

I självstudien har du lärt dig att anpassa operatörsvyn för programmet.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Konfigurera enhetens instrumentpanel
> * Konfigurera layouten för dina enhetsinställningar
> * Konfigurera layouten för dina enhetsegenskaper
> * Förhandsgranska enheten som operatör
> * Konfigurera standardstartsidan
> * Förhandsgranska standardstartsidan som operatör

Nu när du har lärt dig att anpassa operatörsvyn för programmet kan du gå vidare för att:

* [Övervaka dina enheter (som operatör)](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Lägga till en ny enhet i programmet (som operatör och enhetsutvecklare)](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
