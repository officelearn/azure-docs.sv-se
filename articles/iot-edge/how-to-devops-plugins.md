---
title: Aktivera CI/CD med Jenkins plugin - Azure IoT Edge | Microsoft-dokument
description: Azure IoT Edge-tillägget för Jenkins gör att du kan integrera IoT Edge-utvecklings- och distributionsuppgifter i din befintliga DevOps-lösning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510251"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrera Azure IoT Edge med Jenkins-pipelines

Azure IoT Edge har inbyggt stöd för Azure DevOps och Azure DevOps-projekt, men tillhandahåller också en plugin för att expandera DevOps-funktioner till Jenkins. [Jenkins](https://jenkins.io/) är en automatiseringsserver med öppen källkod som använder plugins för att stödja många typer av utvecklings- och distributionsprojekt, inklusive IoT Edge.

Azure IoT Edge-insticksprogrammet för Jenkins fokuserar på kontinuerlig integrering och kontinuerlig distribution. Du kan skapa en build and push-pipeline som bygger moduler och skickar sina behållaravbildningar till behållarregistret. Skapa sedan en versionspipeline som distribuerar moduler till dina IoT Edge-enheter.

Innan du börjar använda IoT Edge-insticksprogrammet för Jenkins behöver du en IoT-hubb i Azure och ett behållarregister för att hålla dina behållaravbildningar. Använd en Azure Service Principal för att ge Jenkins-deltagarebehörighet till din IoT-hubb så att insticksprogrammet kan skapa distributioner för dina IoT Edge-enheter.

Om du är redo att komma igång kan du hitta installations- och användningsinformation för [Azure IoT Edge-insticksprogrammet för Jenkins](https://plugins.jenkins.io/azure-iot-edge).
