---
title: Anpassa operatörsvyerna i Azure IoT Central | Microsoft Docs
description: Som byggare anpassar du operatörsvyerna i Azure IoT Central-programmet.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: c0b42c3efd5e015eaf1fbd750f835d8de8818de9
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43185859"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Självstudie: Anpassa operatörsvyn i Azure IoT Central

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

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du slutföra de två föregående självstudierna:

* [Definiera en ny enhetstyp i Azure IoT Central-programmet](tutorial-define-device-type.md).
* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Konfigurera enhetens instrumentpanel

Som byggare kan du definiera vilken information som ska visas på en enhetsinstrumentpanel. I självstudien [Definiera en ny enhetstyp i programmet](tutorial-define-device-type.md) lade du till ett linjediagram och annan information till instrumentpanelen **Ansluten luftkonditionering-1**.

1. Om du vill redigera enhetsmallen **Ansluten luftkonditionering** väljer du **Explorer** i den vänstra navigeringsmenyn:

    ![Sidan Explorer](media/tutorial-customize-operator/explorer.png)

2. Börja anpassa instrumentpanelen för den anslutna luftkonditioneringsenheten genom att välja enhetsmallen **Ansluten luftkonditionering (1.0.0)**. Välj enheten **Ansluten luftkonditionering-1** som du skapade i självstudien [Definiera en ny enhetstyp i programmet](tutorial-define-device-type.md):

    ![Välja den anslutna luftkonditioneringsenheten](media/tutorial-customize-operator/selectdevice.png)

    När du gör en ändring i en enhet, till exempel **Ansluten luftkonditionering-1**, görs ändringen i den underliggande mallen. Mer information finns i [Skapa en ny version av enhetsmallen](howto-version-devicetemplate.md).

3. Om du vill redigera instrumentpanelen väljer du **Instrumentpanel**:

    ![Sidan Instrumentpanel för enhetsmall](media/tutorial-customize-operator/dashboard.png)

4. Om du vill lägga till en KPI-panel på instrumentpanelen väljer du **KPI**:

    ![Lägga till KPI](media/tutorial-customize-operator/addkpi.png)

    Använd informationen i följande tabell för att definiera KPI:n:

    | Inställning     | Värde |
    | ----------- | ----- |
    | Namn        | Högsta temperatur |
    | Mått | temperatur |
    | Sammansättning | Maximal |
    | Tidsintervall  | Senaste veckan |

5. Välj **Spara**. Du kan nu se KPI-panelen på instrumentpanelen:

    ![KPI-panel](media/tutorial-customize-operator/temperaturekpi.png)

6. Om du vill flytta eller ändra storlek för en panel på instrumentpanelen, flyttar du muspekaren över panelen. Du kan dra panelen till en ny plats eller ändra dess storlek:

    ![Redigera instrumentpanelens layout](media/tutorial-customize-operator/dashboardlayout.png)

## <a name="configure-your-settings-layout"></a>Konfigurera layouten för dina inställningar

Som byggare kan du också konfigurera operatörsvyn för enhetsinställningarna. En operatör använder enhetsinställningssidan till att konfigurera en enhet. Till exempel använder operatören inställningssidan till att ange måltemperaturen för kylskåpet.

1. Om du vill redigera inställningslayouten för din anslutna luftkonditionering väljer du **Inställningar**:

    ![Sidan Inställningar](media/tutorial-customize-operator/settings.png)

2. Du kan flytta och ändra storlek på inställningspanelerna:

    ![Redigera inställningslayouten](media/tutorial-customize-operator/settingslayout.png)

> [!NOTE]
> Du kan inte redigera inställningarnas värden i **Designläge**.

## <a name="configure-your-properties-layout"></a>Konfigurera layouten för dina egenskaper

Du kan också konfigurera operatörsvyn för enhetsegenskaperna, förutom instrumentpanelen och inställningar. En operatör använder enhetens egenskapssida till att hantera enhetsmetadata. Till exempel använder operatören egenskapssidan för att visa enhetens serienummer eller uppdatera kontaktuppgifterna för tillverkaren.

1. Om du vill redigera egenskapslayouten för din anslutna luftkonditionering väljer du **Egenskaper**:

    ![Sidan Egenskaper](media/tutorial-customize-operator/properties.png)

2. Du kan flytta och ändra storlek på egenskapsfälten:

    ![Redigera egenskapslayouten](media/tutorial-customize-operator/propertieslayout.png)

> [!NOTE]
> Du kan inte redigera egenskapernas värden i **Designläge**.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Förhandsgranska den anslutna luftkonditioneringsenheten som operatör

I **Designläge** kan du anpassa instrumentpanel, inställningar och egenskaper för en operatör. Om du stänger av **Designläge** ser du programmet som en operatör.

1. Om du vill se den anslutna luftkonditioneringsenheten som operatör måste du stänga av **Designläge**. Stäng av **Designläge** i **Designläge** högst upp till höger på sidan.

2. Om du vill uppdatera serienumret för enheten, redigerar du värdet i serienummerpanelen och väljer **Spara**:

    ![Redigera ett egenskapsvärde](media/tutorial-customize-operator/editproperty.png)

3. Om du vill skicka en inställning till din anslutna luftkonditionering väljer du **Inställningar** och ändrar inställningsvärdet i panelen. Välj sedan **Uppdatera**:

    ![Skicka inställningen till enheten](media/tutorial-customize-operator/sendsetting.png)

    När enheten får det nya inställningsvärdet, visas inställningen som **synkroniserad** i panelen.

4. Som operatör kan du se enhetsinstrumentpanelen som konfigurerats av byggaren:

    ![Operatörsvy för enhetsinstrumentpanelen](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurera standardstartsidan

När en byggare eller operatör loggar in på ett Azure IoT Central-program, ser de en startsida. Som byggare kan du konfigurera innehållet på startsidan så att det mest användbara och relevanta innehållet visas för operatören.

1. Anpassa standardstartsidan genom att gå till sidan **Start** och aktivera **Designläge** högst upp till höger på sidan. När **Designläge** aktiveras visas en panel från höger med en lista över objekt som du kan lägga till på startsidan.

    ![Sidan Application Builder](media/tutorial-customize-operator/builderhome.png)

2. Om du vill anpassa startsidan lägger du till paneler från **Biblioteket**. Välj **Länk** och lägg till information om din organisations webbplats. Välj sedan **Spara**:

    ![Lägga till en länk på startsidan](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Du kan också lägga till länkar på sidor i ditt Azure IoT Central-program. Du kan till exempel lägga till en länk på en enhetsinstrumentpanel eller inställningssida.

3. Dessutom kan du välja **Bild** och ladda upp en bild som ska visas på startsidan. Bilden kan innehålla en URL som du kommer till när du klickar på den:

    ![Lägga till en bild på startsidan](media/tutorial-customize-operator/addimage.png)

    Läs mer i [Hur du förbereder och laddar upp bilder till ditt Azure IoT Central-program](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Förhandsgranska standardstartsidan som operatör

Om du vill förhandsgranska startsidan som operatör stänger du av **Designläge** längst upp till höger på sidan:

![Växla designläge](media/tutorial-customize-operator/operatorviewhome.png)

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

* [Övervaka dina enheter (som operatör)](tutorial-monitor-devices.md)
* [Lägga till en ny enhet i programmet (som operatör och enhetsutvecklare)](tutorial-add-device.md)
