---
title: Lägga till en riktig enhet till ett Azure IoT Central-program | Microsoft Docs
description: Lägg till en riktig enhet till Azure IoT Central-programmet i egenskap av operatör.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878175"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Självstudier: Lägg till en simulerad enhet till ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar hur du lägger till och konfigurerar en simulerad enhet till din Microsoft Azure IoT Central program.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny simulerad enhet
> * Använd en simulerad enhet i byggnads miljön

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra den första Builder-självstudien för att skapa ett Azure IoT Central-program:

* [Definiera en ny enhetstyp](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (obligatoriskt)

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Om du vill lägga till en riktig enhet i programmet använder du den **miljö sensor** enhets mall som du skapade i själv studie kursen [definiera en ny enhets typ](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Om du vill lägga till en ny enhet som en operatör väljer du **enheter** i den vänstra navigerings menyn. Fliken **enheter** visar **alla enheter** och mallen **miljö sensor** enhet.

1. Om du vill lägga till en simulerad miljö sensor väljer du **+ ny**. Använd det föreslagna **enhets-ID: t** eller ange ditt eget **enhets-ID**. Du kan också ange ett namn på den nya enheten. Ändra den **simulerade** växlingen till **på** och välj sedan **skapa**.

    ![Simulerad enhet](./media/tutorial-add-device-pnp/simulated-device.png)

Nu kan du interagera med de vyer som har skapats av verktyget för enhets mal len med simulerade data.

## <a name="use-a-simulated-device-to-improve-views"></a>Använd en simulerad enhet för att förbättra vyer

När du har skapat en ny simulerad enhet kan verktyget använda den här enheten för att fortsätta att förbättra och bygga på vyerna för enhets mal len.

1. I din enhets vy kopierar du enhets **-ID: t** för den simulerade enhet som du skapade.

1. Välj fliken **enhets mallar** i den vänstra navigerings menyn och välj mallen **miljö sensor** .

1. Välj någon av de vyer som du vill redigera eller skapa en ny vy. Klicka på **Konfigurera för hands versions enhet**. Här kan du välja mellan att inte ha någon förhands gransknings enhet, med en riktig enhet som du kan konfigurera för testning eller från en befintlig enhet som du har lagt till i IoT Central.

1. Välj **Välj från en enhet som körs** och ange **enhets-ID** för den simulerade enhet som du har kopierat.

1. Välj **Använd**. Nu kan du se samma simulerade enhet i din enhets mall för att skapa upplevelser. Den här vyn är särskilt användbar för diagram och andra visualiseringar.

    ![Konfigurera för hands versions enhet](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Lägga till en ny simulerad enhet
* Använd en simulerad enhet i byggnads miljön

Nu när du har anslutit en simulerad enhet till ditt Azure IoT Central-program, finns det några förslag på nästa steg.

Som operatör lär du dig att:

> [!div class="nextstepaction"]
> [Konfigurera regler](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Som enhetsutvecklare lär du dig att:

> [!div class="nextstepaction"]
> [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



