---
title: Aktivera CI/CD med Jenkins plugin - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-tillägget för Jenkins kan du integrera IoT Edge devlopment and deployment-aktiviteter i din befintliga DevOps-lösning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 173e6ff91acd2ad28d7203b2b5db65e0ee0ecc43
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910845"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrera Azure IoT Edge med Jenkins-pipeliner

Azure IoT Edge har inbyggt stöd för Azure DevOps och Azure DevOps-projekt, men innehåller också ett plugin-program för att expandera DevOps-funktionerna till Jenkins. [Jenkins](https://jenkins.io/) är en öppen källkod automatiseringsserver som använder plugin-program som stöd för många typer av utveckling och distribution av projekt, bland annat IoT Edge. 

Azure IoT Edge-plugin för Jenkins fokuserar på kontinuerlig integrering och kontinuerlig distribution. Du kan skapa en bygg- och push-pipeline som skapar moduler och skickar sina behållaravbildningar till ditt behållarregister. Skapa sedan en releasepipeline som distribuerar moduler till din IoT Edge-enheter. 

Innan du börjar med IoT Edge-pluginprogrammet för Jenkins, behöver du en IoT-hubb i Azure och ett behållarregister för att lagra dina behållaravbildningar. Använd ett tjänstens huvudnamn för Azure för att ge Jenkins deltagarbehörighet till din IoT-hubb så att plugin-programmet kan skapa distributioner för din IoT Edge-enheter. 

Om du är redo att sätta igång, hitta installationen och använda informationen för den [Azure IoT Edge-plugin för Jenkins](https://plugins.jenkins.io/azure-iot-edge).