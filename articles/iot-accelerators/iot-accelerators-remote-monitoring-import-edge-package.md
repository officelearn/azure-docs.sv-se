---
title: Import Edge-paket för fjärr övervakning – Azure | Microsoft Docs
description: I den här artikeln beskrivs hur du importerar ett IoT Edge-paket till lösningen för fjärr styrnings lösningar
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012296"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importera ett IoT Edge-paket till acceleratorn för fjärrövervakningslösningen

Ett distributions manifest definierar de moduler som ska distribueras till en IoT Edge enhet. Den här artikeln förutsätter att en utvecklare i din organisation redan har skapat distributions manifestet. Information om hur en utvecklare skapar ett manifest finns i något av följande IoT Edge instruktions artiklar:

- [Distribuera Azure IoT Edge moduler från Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Distribuera Azure IoT Edge-moduler med Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Distribuera Azure IoT Edge moduler från Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

En utvecklare skapar och testar ett distributions manifest i en utvecklings miljö. När du är klar kan du importera manifestet till din lösnings Accelerator för fjärr styrning.

## <a name="export-a-manifest"></a>Exportera ett manifest

Använd Azure Portal för att exportera distributions manifestet från utvecklings miljön:

1. I Azure Portal navigerar du till den IoT-hubb som du använder för att utveckla och testa dina IoT Edge enheter. Klicka på **IoT Edge** och **IoT Edge distributioner**: ![ IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klicka på den distribution som har den distributions konfiguration som du vill använda. Sidan **distributions information** visar: ![ IoT Edge distributions information](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klicka på **hämta IoT Edge manifest**:  ![ Ladda ned distributions manifest](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Spara JSON-filen som en lokal fil **med namnetdeploymentmanifest.jspå**.

Nu har du en fil som innehåller distributions manifestet. I nästa avsnitt importerar du det här manifestet som ett paket till den fjärranslutna övervaknings lösningen.

## <a name="import-a-package"></a>Importera ett paket

Följ stegen nedan för att importera ett gräns distributions manifest som ett paket till din lösning:

1. Gå till sidan **paket** på sidan för webb gränssnitt för fjärrövervakning:  ![ sidan paket](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klicka på **+ nytt paket**, Välj **Edge-manifest** som pakettyp och klicka på **bläddra** för att välja den **deploymentmanifest.jspå** filen som du sparade i föregående avsnitt:  ![ Välj manifest](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Klicka på **överför** för att lägga till paketet i din lösning för fjärrövervakning:  ![ överfört paket](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Du har nu laddat upp ett IoT Edge distributions manifest som ett paket. På sidan **distributioner** kan du distribuera det här paketet till dina anslutna IoT Edge enheter.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar moduler till en IoT Edge-enhet från lösningen för fjärrövervakning är nästa steg att lära dig mer om [IoT Edge](../iot-edge/about-iot-edge.md).
