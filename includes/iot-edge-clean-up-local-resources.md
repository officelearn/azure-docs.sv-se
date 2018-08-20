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
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046909"
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