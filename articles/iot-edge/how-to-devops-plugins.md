---
title: Aktivera CI/CD med Jenkins-plugin-Azure IoT Edge | Microsoft Docs
description: Med Azure IoT Edge tillägget för Jenkins kan du integrera IoT Edge utvecklings-och distributions uppgifter i din befintliga DevOps-lösning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510251"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrera Azure IoT Edge med Jenkins-pipelines

Azure IoT Edge har inbyggt stöd för Azure DevOps och Azure DevOps Projects, men innehåller också ett plugin-program för att expandera DevOps-funktioner till Jenkins. [Jenkins](https://jenkins.io/) är en Automation-server med öppen källkod som använder plugin-program för att stödja många typer av utvecklings-och distributions projekt, inklusive IoT Edge.

Azure IoT Edge-plugin-programmet för Jenkins fokuserar på kontinuerlig integrering och kontinuerlig distribution. Du kan skapa en pipeline för build och push som bygger moduler och skickar sina behållar avbildningar till behållar registret. Skapa sedan en versions pipeline som distribuerar moduler till dina IoT Edge enheter.

Innan du börjar använda IoT Edge-plugin-programmet för Jenkins behöver du en IoT-hubb i Azure och ett behållar register för att rymma behållar avbildningar. Använd ett huvud namn för Azure-tjänsten för att ge Jenkins deltagar behörighet till din IoT-hubb så att plugin-programmet kan skapa distributioner för dina IoT Edge enheter.

Om du är redo att komma igång hittar du installations-och användnings information för [Azure IoT Edge-plugin-programmet för Jenkins](https://plugins.jenkins.io/azure-iot-edge).
