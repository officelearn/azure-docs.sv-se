---
title: "Installera Azure IoT kanten på IoT Core | Microsoft Docs"
description: "Installera Azure IoT kant runtime på en Core för Windows-IoT-enhet"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installera IoT kant-körningsmiljön på Windows IoT Core - förhandsgranskning

Azure IoT kant körning kan köras även på små enda Board dator (SBC)-enheter som är mycket vanligt förekommande i branschen IoT. Den här artikeln innehåller stegvisa etablering körningsmiljön på en [MinnowBoard piggvar] [ lnk-minnow] development board som kör Windows IoT Core.

## <a name="install-the-runtime"></a>Installera runtime

1. Installera [instrumentpanelen för Windows 10 IoT Core] [ lnk-core] på ett värdsystem.
1. Följ stegen i [ställa in enheten] [ lnk-board] att konfigurera kortets med MinnowBoard piggvar/MAX skapa 16299 avbildningen. 
1. Aktivera enheten, sedan [logga in via fjärranslutning med PowerShell][lnk-powershell].
1. I konsolen PowerShell [installera binärfilerna för Docker][lnk-docker-install].
1. Kör följande kommando i PowerShell-konsolen för att installera IoT kant-runtime och verifiera konfigurationen:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Det här skriptet innehåller följande: 
   * Python 3,6
   * Skriptet IoT kant kontrollen (iotedgectl.exe)

Du kan se information utdata från verktyget iotedgectl.exe i rött i fjärranslutna PowerShell-fönstret. Det behöver inte betyda fel. 

## <a name="next-steps"></a>Nästa steg

Nu när du har en enhet som kör IoT kant runtime lär du dig hur du [distribuera och övervaka IoT kant moduler i skala][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers