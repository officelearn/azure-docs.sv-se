---
title: Installera Azure IoT kanten på IoT Core | Microsoft Docs
description: Installera Azure IoT kant runtime på en Core för Windows-IoT-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: af6ce22d1b41ebfe9b64ea5fc1a2cf99478af925
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630042"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installera IoT kant-körningsmiljön på Windows IoT Core - förhandsgranskning

Azure IoT kant och [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) samverkar för att aktivera edge computing på även små enheter. Azure IoT kant körning kan köras även på små enda Board dator (SBC)-enheter som är mycket vanligt förekommande i branschen IoT. 

Den här artikeln innehåller stegvisa etablering körningsmiljön på en [MinnowBoard piggvar] [ lnk-minnow] development board som kör Windows IoT Core. Windows IoT Core stöder Azure IoT kanten på Intel x64-baserade processorer. 

## <a name="install-the-runtime"></a>Installera runtime

1. Installera [instrumentpanelen för Windows 10 IoT Core] [ lnk-core] på ett värdsystem.
1. Följ stegen i [ställa in enheten] [ lnk-board] att konfigurera kortets med MinnowBoard piggvar/MAX skapa 16299 avbildningen. 
1. Aktivera enheten, sedan [logga in via fjärranslutning med PowerShell][lnk-powershell].
1. Installera behållaren runtime i PowerShell-konsolen: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Den här behållaren runtime är från Moby project build-server och riktar sig enbart i utvärderingssyfte. Det har inte testats, godkända eller stöds av Docker.

1. Installera IoT kant-runtime och kontrollera konfigurationen:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Det här skriptet innehåller följande: 
   * Python 3,6
   * Skriptet IoT kant kontrollen (iotedgectl.exe)

Du kan se information utdata från verktyget iotedgectl.exe i grönt i fjärranslutna PowerShell-fönstret. Det behöver inte betyda fel. 

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