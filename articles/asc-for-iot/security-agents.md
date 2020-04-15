---
title: Översikt över säkerhetsagenter
description: Kom igång med att förstå, konfigurera, distribuera och använda Azure Security Center för IoT-säkerhetstjänstagenter på dina IoT-enheter.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310653"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Komma igång med Azure Security Center för IoT-enhetssäkerhetsagenter

Azure Security Center för IoT-säkerhetsagenter erbjuder förbättrade säkerhetsfunktioner, till exempel övervakning av fjärranslutningar, aktiva program, inloggningshändelser och metodtips för konfiguration av operativsystem. Ta kontroll över ditt skydd för hotskydd för enhetsfältet och säkerhetshållning med en enda tjänst.

Referensarkitektur för Linux- och Windows-säkerhetsagenter, både i C# och C tillhandahålls.

Azure Security Center för IoT-säkerhetsagenter hanterar rå händelsesamling från enhetens operativsystem, händelseaggregering för att minska kostnaderna och konfiguration via en enhetsmodultvilling. Säkerhetsmeddelanden skickas via din IoT Hub till Azure Security Center för IoT-analystjänster.

Använd följande arbetsflöde för att distribuera och testa ditt Azure Security Center för IoT-säkerhetsagenter:

1. [Aktivera Azure Security Center för IoT-tjänst till din IoT-hubb](quickstart-onboard-iot-hub.md)
1. Om din IoT Hub inte har några registrerade enheter [registrerar du en ny enhet](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Skapa en azureiotsecurity-säkerhetsmodul](quickstart-create-security-twin.md) för dina enheter.
1. Om du vill installera agenten på en Azure-simulerad enhet i stället för att installera på en verklig [enhet, snurra upp en ny Virtuell Azure Machine (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) i en tillgänglig zon.
1. [Distribuera ett Azure Security Center för IoT-säkerhetsagent](how-to-deploy-linux-cs.md) på din IoT-enhet eller ny virtuell dator.
1. Följ instruktionerna för [trigger_events](https://aka.ms/iot-security-github-trigger-events) att köra en ofarlig simulering av en attack.
1. Verifiera Azure Security Center för IoT-aviseringar som svar på den simulerade attacken i föregående steg. Börja verifieringen fem minuter efter att skriptet har körts.
1. Utforska [aviseringar,](concept-security-alerts.md) [rekommendationer](concept-recommendations.md)och [djupdykning med Log Analytics](how-to-security-data-access.md) med IoT Hub.

## <a name="next-steps"></a>Nästa steg

- Konfigurera [din lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhetsagent](how-to-deploy-agent.md)
