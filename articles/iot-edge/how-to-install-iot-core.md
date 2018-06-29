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
ms.openlocfilehash: ae5644a62b794dc8d6ace52f21a452fa70027d39
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029568"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installera IoT kant-körningsmiljön på Windows IoT Core - förhandsgranskning

Azure IoT kant och [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) samverkar för att aktivera edge computing på även små enheter. Azure IoT kant körning kan köras även på små enda Board dator (SBC)-enheter som är mycket vanligt förekommande i branschen IoT. 

Den här artikeln beskriver hur etablering körning på en utvecklings-kort som kör Windows IoT Core. 

**Windows IoT Core stöder för närvarande Azure IoT kanten på Intel x64-baserade processorer.**

## <a name="install-the-container-runtime"></a>Installera behållaren runtime

1. Konfigurera din kort med **skapa 17134 (RS4)** IoT Core avbildningen. 
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

## <a name="finish-installing"></a>Slut installationen

Installera daemonen IoT kant säkerhet och konfigurera den med hjälp av anvisningarna i [i den här artikeln][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Nästa steg

Nu när du har en enhet som kör IoT kant runtime lär du dig hur du [distribuera och övervaka IoT kant moduler i skala][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md#download-the-edge-daemon-package-and-install
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers