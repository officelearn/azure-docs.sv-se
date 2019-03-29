---
title: Distribuera en ASC för IoT Edge-modul | Microsoft Docs
description: Mer information om hur distribuera ASC för IoT security-agenten på IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580500"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Distribuera säkerhetsmodul på din IoT Edge-enhet

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC för IoT **azureiotsecurity** modulen innehåller en omfattande lösning för din IoT Edge-enhet.
Modul för maskinvarusäkerhet samlar in, aggregerar och analyserar raw säkerhetsdata från datorn operativsystem och en behållare i handlingsbara rekommendationer och aviseringar.
Mer information finns i [säkerhetsmodul för IoT Edge](security-edge-architecture.md).

I den här guiden lär du dig att distribuera en modul för maskinvarusäkerhet på din IoT Edge-enhet.

## <a name="deploy-security-module"></a>Distribuera säkerhetsmodul

Använd följande steg för att distribuera en ASC för IoT-säkerhetsmodul för IoT Edge.

### <a name="prerequisites"></a>Förutsättningar

1. Kontrollera att enheten är [registrerad som en IoT Edge-enhet](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. ASC för IoT Edge-modulen kräver den [AuditD framework](https://linux.die.net/man/8/auditd) installerad på Edge-enhet.

   Installera framework genom att köra följande kommando på din Edge-enhet:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Distributionen med hjälp av Azure-portalen

1. Öppna **Marketplace** i Azure-portalen.

1. Sök efter **azure iot-säkerhet** och klicka på **Azure IoT-säkerhet**.

   ![](media/howto/edge_onboarding_7.png)

1. Klicka på **Skapa**.

1. Välj din **prenumeration**, **IoT Hub** och **IoT Edge-enhetsnamn**, klicka sedan på **skapa**.

1. Klicka på **nästa** två gånger till **granska distribution**.

1. Se till att **edgeHub.settings.createOptions** konfigureras på följande sätt:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Klicka på **skicka** att slutföra distributionen.


## <a name="next-steps"></a>Nästa steg

Mer information om konfigurationsalternativ fortsätter du att den här guiden för Modulkonfiguration av. 
> [!div class="nextstepaction"]
> [Modulkonfigurationen så hjälper](./how-to-agent-configuration.md)