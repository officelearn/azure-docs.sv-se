---
title: Installera Azure IoT Edge på IoT Core | Microsoft Docs
description: Installera Azure IoT Edge-körningen på en Windows IoT Core-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576006"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installera IoT Edge-körningen på Windows IoT Core – förhandsversion

Azure IoT Edge och [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) fungerar tillsammans för att aktivera edge computing på även små enheter. Azure IoT Edge-körningen kan köras även på mycket liten enda tavla dator (SBC)-enheter som är mycket vanlig i IoT-branschen. 

Den här artikeln beskriver distribuera runtime på en tavla för utveckling som kör Windows IoT Core. 

**Windows IoT Core stöder för närvarande, Azure IoT Edge endast på Intel x64-baserade processorer.**

## <a name="install-the-container-runtime"></a>Installera runtime behållare

1. Konfigurera din tavla med **skapa 17134 (RS4)** IoT Core-avbildning. 
1. Aktivera enheten, sedan [logga in via en fjärranslutning med PowerShell][lnk-powershell].
1. Installera runtime för behållare i PowerShell-konsolen: 

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
   >Den här behållaren runtime från Moby projekt build-servern, och är avsedd för utvärderingssyfte endast. Det har inte testats, godkända eller stöds av Docker.

## <a name="finish-installing"></a>Installationen är klar

Installera IoT Edge Security Daemon och konfigurera den med hjälp av anvisningarna i [i den här artikeln][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Nästa steg

Nu när du har en enhet som kör IoT Edge-körningen, lär du dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
