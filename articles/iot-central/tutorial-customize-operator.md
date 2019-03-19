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
ms.openlocfilehash: 86c9a7794146edc4106d8ec30106e1c27556248f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769684"
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
> * Konfigurera standardinstrumentpanelen för program
> * Förhandsgranska standardinstrumentpanelen för program som en operatör

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra de två föregående självstudierna:

* [Definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type.md).
* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Konfigurera enhetens instrumentpanel

Som byggare kan du definiera vilken information som ska visas på en enhetsinstrumentpanel. I självstudien [Definiera en ny enhetstyp i programmet](tutorial-define-device-type.md) lade du till ett linjediagram och annan information till instrumentpanelen **Ansluten luftkonditioneringsenhet**.

1. Om du vill redigera enhetsmallen **Ansluten luftkonditioneringsenhet** väljer du **Enhetsmallar** i den vänstra navigeringsmenyn:

    ![Sidan Enhetsmallar](media/tutorial-customize-operator/devicetemplates.png)

2. För att anpassa din instrumentpanel för enheten, Välj den **anslutna luftkonditionering (1.0.0)** enhet mallen som du skapade i den [definierar en ny typ av enhet i ditt program](tutorial-define-device-type.md) självstudien.

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

    ![Lägga till KPI](media/tutorial-customize-operator/addkpi.png)

5. Välj **Spara**. Du kan nu se KPI-panelen på instrumentpanelen:

    ![KPI-panel](media/tutorial-customize-operator/temperaturekpi.png)

6. Om du vill flytta eller ändra storlek för en panel på instrumentpanelen, flyttar du muspekaren över panelen. Du kan dra panelen till en ny plats eller ändra dess storlek.

## <a name="configure-your-settings-layout"></a>Konfigurera layouten för dina inställningar

Som byggare kan du också konfigurera operatörsvyn för enhetsinställningarna. En operatör använder fliken för enhetsinställningar till att konfigurera en enhet. Till exempel kan en operatör använda inställningsfliken för att ange måltemperaturen för den anslutna luftkonditioneringsenheten.

1. Om du vill redigera inställningslayouten för din anslutna luftkonditioneringsenhet väljer du fliken **Inställningar**.

2. Du kan flytta och ändra storlek på inställningspanelerna:

    ![Redigera inställningslayouten](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurera layouten för dina egenskaper

Du kan också konfigurera operatörsvyn för enhetsegenskaperna, förutom instrumentpanelen och inställningar. En operatör använder fliken för enhetsegenskaper till att hantera enhetsmetadata. Till exempel använder operatören egenskapsfliken för att visa en enhets serienummer eller uppdatera tillverkarens kontaktuppgifter.

1. Om du vill redigera egenskapslayouten för din anslutna luftkonditioneringsenhet väljer du fliken **Egenskaper**.

2. Du kan flytta och ändra storlek på egenskapsfälten:

    ![Redigera egenskapslayouten](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Förhandsversion av enheten

Du använder sidan **Enhetsmallar** till att anpassa flikarna för instrumentpanel, inställningar och egenskaper för en operatör. Du använder sidan **Device Explorer** till att visa och använda enhetsmallen.

1. Om du vill visa och använda mallen för ansluten luftkonditioneringsenhet som operatör går du till sidan **Device Explorer** och väljer den simulerade enhet som IoT Central genererade från din mall:

    ![Visa och använda enhetsmallen](media/tutorial-customize-operator/usetemplate.png)

2. Om du vill uppdatera platsen för den här enheten väljer du **Egenskaper** och redigerar värdet i platspanelen. Välj sedan **spara**:

    ![Redigera ett egenskapsvärde](media/tutorial-customize-operator/editproperty.png)

3. Om du vill skicka en inställning till din anslutna luftkonditionering väljer du **Inställningar** och ändrar inställningsvärdet i panelen. Välj sedan **Uppdatera**:

    ![Skicka inställningen till enheten](media/tutorial-customize-operator/sendsetting.png)

    När enheten får det nya inställningsvärdet, visas inställningen som **synkroniserad** i panelen.

4. Som operatör kan du se enhetsinstrumentpanelen som konfigurerats av byggaren:

    ![Operatörsvy för enhetsinstrumentpanelen](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Konfigurera standardinstrumentpanel

När en builder eller operatorn loggar du in på ett Azure IoT Central program, kan de se instrumentpanelen för program. Du kan konfigurera innehållet i standardinstrumentpanelen för att inkludera det mest användbara och relevant innehållet för en operatör som ett verktyg.

> [!NOTE]
> Användare kan också skapa egna personliga instrumentpaneler och välja en som standard.

1. För att anpassa programmet standardinstrumentpanelen, navigera till den **instrumentpanelen** och välj **redigera** uppe till höger på sidan. En ruta visas med ett bibliotek med objekt som du kan lägga till på instrumentpanelen.

    ![Instrumentpanelen](media/tutorial-customize-operator/builderhome.png)

2. Du kan anpassa instrumentpanelen genom att lägga till paneler från den **biblioteket**. Välj **Länk** och lägg till information om din organisations webbplats. Välj sedan **Spara**:

    ![Lägg till länk till instrumentpanelen](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Du kan också lägga till länkar på sidor i ditt Azure IoT Central-program. Du kan till exempel lägga till en länk på en enhetsinstrumentpanel eller inställningssida.

3. Du kan också välja **bild** och ladda upp en bild som ska visas på instrumentpanelen. En avbildning kan ha en URL som du navigera när du väljer den:

    ![Lägga till en bild på instrumentpanelen](media/tutorial-customize-operator/addimage.png)

    Läs mer i [Hur du förbereder och laddar upp bilder till ditt Azure IoT Central-program](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Förhandsversion av instrumentpanelen

Om du vill förhandsgranska instrumentpanel för program som operatör, Välj **klar** uppe till höger på sidan.

![Växla designläge](media/tutorial-customize-operator/operatorviewhome.png)

Du kan välja att gå till URL: er som du anger som en builder-länk och avbildningar panelerna.

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

* [Övervaka dina enheter (som operatör)](tutorial-monitor-devices.md)
* [Lägga till en ny enhet i programmet (som operatör och enhetsutvecklare)](tutorial-add-device.md)