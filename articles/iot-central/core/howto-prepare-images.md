---
title: Ladda upp bilder till ditt Azure IoT Central-program | Microsoft Docs
description: Som ett verktyg kan du läsa om hur du förbereder och laddar upp bilder till ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d8a863ee971fa154740f3726034fc4604b02fd34
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949929"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Förbereda och ladda upp avbildningar till ditt Azure IoT Central-program

Den här artikeln beskriver hur du kan anpassa ditt Azure IoT Central-program genom att ladda upp anpassade avbildningar som ett verktyg. Du kan till exempel anpassa en instrument panel för enheten med en bild av enheten.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central-program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
1. Ett verktyg för att skala och ändra storlek på bildfiler.

## <a name="choose-where-to-use-custom-images"></a>Välj var anpassade bilder ska användas

Du kan lägga till anpassade avbildningar på följande platser i ett Azure IoT Central-program:

* Sidan **Mina program**

    ![Bild på program hanterings Sidan](media/howto-prepare-images/applicationmanager.png)

* Instrument panelen för programmet

    ![Bild på program instrument panel](media/howto-prepare-images/homepage.png)

* En mall för enhet

    ![Bild på enhets mal len](media/howto-prepare-images/devicetemplate.png)

* En panel på en enhets instrument panel

    ![Bild på enhets panel](media/howto-prepare-images/devicetile.png)

* En panel på en instrument panel för enhets uppsättningar

    ![Bild på panelen enhets uppsättning](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Förbereda avbildningarna

På alla fyra platser kan du använda antingen PNG-, GIF-eller JPEG-bilder.

I följande tabell sammanfattas de bild storlekar som du kan använda:

| Plats | Storlekar |
| -------- | ------ |
| Program hanterare | 268x160 BPT |
| Enhets mall | 64x64 BPT |
| Paneler på instrument panelen | Den minsta storleks rutan är 200x200 BPT, större paneler kan vara antingen kvadratiska eller rektangulära multiplar av små paneler. Till exempel 200x400 px, 400x200 px eller 400x400 px |

För bästa visning i programmet bör du skapa bilder som matchar de dimensioner som visas i föregående tabell.

## <a name="upload-the-images"></a>Ladda upp avbildningarna

I följande avsnitt beskrivs hur du laddar upp avbildningarna på olika platser:

### <a name="application-manager"></a>Program hanterare

Om du vill ladda upp en bild som ska användas på sidan **Mina program** går du till sidan **program inställningar** i avsnittet **Administration** . Du måste vara administratör för att slutföra den här uppgiften:

![Ladda upp program avbildning](media/howto-prepare-images/uploadapplicationmanager.png)

Välj panelen **program avbildning** för att ladda upp en bild (268x160 px) från den lokala datorn.

### <a name="application-dashboard"></a>Instrumentpanel för program

Om du vill ladda upp en bild på program instrument panelen navigerar du till sidan **instrument panel** i programmet och väljer **Redigera**. Du måste vara ett verktyg för att slutföra den här uppgiften:

![Ladda upp instrument panels avbildning](media/howto-prepare-images/uploadhomepage.png)

Under **Konfigurera avbildning**väljer du panelen **bild** för att ladda upp en bild från den lokala datorn. Den minsta storleks rutan är 200x200 BPT, större paneler kan vara antingen kvadratiska eller rektangulära multiplar av små paneler. Till exempel 200x400 px, 400x200 BPT eller 400x400 px.

**Spara** den uppladdade avbildningen. Du kan ändra storlek på den i redigerings läge. Välj **klart** när du är färdig.

### <a name="device-template"></a>Enhets mall

Om du vill ladda upp en avbildning i en enhets mall navigerar du till **enhetens mallar** och väljer enhets mal len. Du måste vara ett verktyg för att slutföra den här uppgiften:

![Ladda upp enhets mal len avbildning](media/howto-prepare-images/uploaddevicetemplate.png)

Välj panelen bild för att ladda upp en bild (64x64 px) från den lokala datorn.

### <a name="device-dashboard"></a>Instrumentpanel för enhet

Om du vill ladda upp en avbildning på en enhets instrument panel navigerar du till **enhetens mallar** och väljer enhets mal len. Välj fliken **instrument panel** . Du måste vara ett verktyg för att slutföra den här uppgiften:

![Ladda upp instrument panels bild för enhet](media/howto-prepare-images/uploaddevicedashboard.png)

Under **Konfigurera avbildning**väljer du panelen **bild** och väljer sedan den fil som ska laddas upp från den lokala datorn. Den minsta storleks rutan är 200x200 BPT, större paneler kan vara antingen kvadratiska eller rektangulära multiplar av små paneler. Till exempel 200x400 px, 400x200 BPT eller 400x400 px.

**Spara** den uppladdade avbildningen. Du kan ändra storlek på och flytta den i redigerings läget. Välj **klart** när du är färdig.

### <a name="device-set-dashboard"></a>Instrument panel för enhets uppsättning

Om du vill ladda upp en avbildning på en instrument panel för enhets uppsättningar, navigerar du till **enhets uppsättningar** och väljer enhets uppsättningen och sedan en enhet. Välj sedan sidan **instrument panel** och välj **Redigera**:

![Ladda upp instrument panels bild för enhets uppsättning](media/howto-prepare-images/uploaddevicesetdashboard.png)

Under **Konfigurera avbildning**väljer du panelen **bild** för att ladda upp en bild från den lokala datorn. Den minsta storleks rutan är 200x200 BPT, större paneler kan vara antingen kvadratiska eller rektangulära multiplar av små paneler. Till exempel 200x400 px, 400x200 BPT eller 400x400 px.

**Spara** den uppladdade avbildningen. Du kan ändra storlek på och flytta den i redigerings läget. Välj **klart** när du är färdig.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du förbereder och laddar upp bilder till ditt Azure IoT Central-program, så är det förslag på nästa steg:

* [Anpassa Azure IoT Central UI](./howto-customize-ui.md)
* [Lägga till paneler på din instrument panel](./howto-add-tiles-to-your-dashboard.md)
* [Hantera enheter i ditt Azure IoT Central-program](howto-manage-devices.md)
