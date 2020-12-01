---
title: Distributionsalternativ
description: Kom igång med att förstå det grundläggande arbets flödet i Defender för IoT-funktioner och-tjänster.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340024"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Komma igång med Azure Defender för IoT

Den här artikeln beskriver de distributions-och onboarding-processer som krävs för att få Azure Defender för IoT igång. Ytterligare steg krävs också. Vi rekommenderar att du förstår de här stegen och bekantar dig med information i medföljande dokument.

När du har slutfört alla steg kommer Azure Defender för IoT-sensorer att övervaka ditt nätverk. Beroende på hur du konfigurerar din lösning kan identifieringar också skickas till den lokala hanterings konsolen eller till IoT Hub.

Utför följande steg för att få Azure Defender för IoT igång.

## <a name="1-set-up-azure"></a>1. Konfigurera Azure

- Konfigurera ett Azure-konto. Mer information finns i [skapa ett Azure-konto](/learn/modules/create-an-azure-account/).

- Brand vägg eller proxy: om du har en brand vägg eller liknande mellanliggande nätverks enhet som är konfigurerad för att tillåta vissa anslutningar kontrollerar du att antingen *. azure-devices.net:443 är öppen för brand väggen eller proxyservern. Om jokertecken inte stöds eller om du vill ha mer kontroll, ska det angivna IoT Hub fullständiga domän namnet öppnas i din VB eller proxy. Mer information finns i [referens-IoT Hub slut punkter](../iot-hub/iot-hub-devguide-endpoints.md).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. distribuera maskin vara, program vara och onboard to sensor

- Köp sensor maskin vara och installations program. Följ stegen som beskrivs här och mer information finns i den här artikeln och [maskin varu guiden för Defender för IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) och i [installations guiden](https://aka.ms/AzureDefenderforIoTInstallSensorISO)för.

  - När du har installerat sensorn registrerar du autentiseringsuppgifterna för sensor inloggningen på ett säkert sätt. Du behöver autentiseringsuppgifterna för att ladda upp aktiverings filen till sensorn.

  - Om du arbetar med sensorer som hanteras lokalt kan du på ett säkert sätt anteckna IP-adressen för sensorn eller sensor namnet som definierats i installationen. Du kanske vill använda den när du skapar ett sensor namn under sensor registreringen i Defender för IoT-portalen. Du kan använda dem senare för att säkerställa enklare spårning och konsekvent namngivning mellan registrerings namnet i Azure Defender för IoT-portalen och IP-adressen för den distribuerade sensorn som visas i sensor konsolen.

- Registrera sensorn med Defender för IoT-portalen och ladda ned en sensor aktiverings fil.

- Ladda upp aktiverings filen till sensorn.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. utför nätverks konfigurationen för sensor övervakning och hantering

- Anslut din sensor till nätverket. Beskrivs i [installations guiden för nätverk](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. börja identifiera ditt nätverk

- Anpassa systeminställningarna i sensor konsolen.

- Anslut sensorer till en lokal hanterings konsol.

Mer information finns i användar [handboken för Azure Defender för IoT-sensor](https://aka.ms/AzureDefenderforIoTUserGuide) och [Användar handbok för Defender för lokal hanterings konsol](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. Fyll i Azure Sentinel med aviserings information

- Om du vill skicka aviserings information till Azure Sentinel konfigurerar du Azure Sentinel: [Anslut dina data från Defender för IoT till Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Nästa steg

- Aktivera [Defender för IoT](quickstart-onboard-iot-hub.md)
- Konfigurera din [lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhets agent](how-to-deploy-agent.md)