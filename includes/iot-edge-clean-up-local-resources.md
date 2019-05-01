---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866809"
---
### <a name="delete-local-resources"></a>Ta bort lokala resurser

Om du vill ta bort IoT Edge-körningen och relaterade resurser från enheten kan du använda lämpliga kommandon för operativsystemet på enheten. 

#### <a name="windows"></a>Windows

Avinstallera IoT Edge-körningen.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

När IoT Edge-körningen tas bort stoppas de containrar som den skapade, men de finns fortfarande kvar på enheten. Visa alla containrar.

   ```powershell
   docker ps -a
   ```

Ta bort de körningscontainrar som har skapats på enheten.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Ta bort alla ytterligare containrar som visades i `docker ps`-utdata genom att referera till containernamn. 

#### <a name="linux"></a>Linux

Ta bort IoT Edge-körningen.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

När IoT Edge-körningen tas bort stoppas de containrar som den skapade, men de finns fortfarande kvar på enheten. Visa alla containrar.

   ```bash
   sudo docker ps -a
   ```

Ta bort de körningscontainrar som har skapats på enheten.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Ta bort alla ytterligare containrar som visades i `docker ps`-utdata genom att referera till containernamn. 

Ta bort körmiljön för containern.

   ```bash
   sudo apt-get remove --purge moby
   ```