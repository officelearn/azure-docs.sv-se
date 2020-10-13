---
title: inkludera fil
description: inkludera fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877238"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Skapa Azure IoT Edge gateway-enhet

Program för video analys – objekt-och motion-identifiering innehåller en **lva kant objekt detektor** och en **lva mall för kant rörelse avkänning** . I det här avsnittet skapar du en mall för en gateway-enhet med hjälp av distributions manifestet och lägger till gateway-enheten i IoT Central-programmet.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Skapa en enhets mall för LVA Edge-gatewayen

Så här importerar du distributions manifestet och skapar **lva Edge Gateway** -enhetens mall:

1. I ditt IoT Central program, navigerar du till **enhets mallar**och väljer **+ ny**.

1. På sidan **Välj Malltyp** väljer du panelen **Azure IoT Edge** . Välj sedan **Nästa: anpassa**.

1. På sidan **överför ett Azure IoT Edge distributions manifest** anger du *lva Edge Gateway* som Mallnamn och kontrollerar **gateway-enheten med underordnade enheter**.

    Sök inte efter distributions manifestet ännu. Om du gör det förväntar sig distributions guiden ett gränssnitt för varje modul, men du behöver bara exponera gränssnittet för **LvaEdgeGatewayModule**. Du överför manifestet i ett senare steg.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Ladda inte upp distributions manifest":::

    Välj **Nästa: Granskning**.

1. På sidan **Granska** väljer du **skapa**.

### <a name="import-the-device-capability-model"></a>Importera enhetens kapacitets modell

Enhets mal len måste innehålla en modell för enhets kapacitet. På sidan **lva Edge Gateway** väljer du panelen **Importera kapacitets modell** . Navigera till mappen *lva* som du skapade tidigare och välj *LvaEdgeGatewayDcm.jspå* filen.

Enhets mal len **lva Edge Gateway** innehåller nu **modulen lva Edge Gateway** tillsammans med tre gränssnitt: **enhets information**, **lva Edge Gateway-inställningar**och **lva Edge Gateway-gränssnitt**.
