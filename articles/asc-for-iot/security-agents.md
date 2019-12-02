---
title: Kom igång med Azure Security Center för IoT-säkerhetsagenter | Microsoft Docs
description: Kom igång med att förstå, konfigurera, distribuera och använda Azure Security Center för IoT Security Service-agenter på dina IoT-enheter.
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
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664202"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Kom igång med Azure Security Center för IoT-enhetens säkerhets agenter

Azure Security Center för IoT-säkerhetsagenter ger förbättrade säkerhetsfunktioner, till exempel övervakning av fjärr anslutningar, aktiva program, inloggnings händelser och metod tips för operativ system konfiguration. Ta kontroll över ditt enhets fält hot skydd och säkerhets position med en enda tjänst. 

Referens arkitektur för Linux-och Windows-säkerhetsagenter C# , både i och C tillhandahålls.

Azure Security Center för IoT-säkerhetsagenter hanterar rå händelse insamling från enhetens operativ system, händelse agg regering för att minska kostnaderna och konfigurationen genom en enhets modul med dubbla. Säkerhets meddelanden skickas via din IoT Hub till Azure Security Center för IoT Analytics-tjänster.

Använd följande arbets flöde för att distribuera och testa Azure Security Center för IoT-säkerhetsagenter: 

1. [Aktivera Azure Security Center för IoT-tjänsten till din IoT Hub](quickstart-onboard-iot-hub.md)
1. Om din IoT Hub inte har några registrerade enheter [registrerar du en ny enhet](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Skapa en azureiotsecurity säkerhetsmodul](quickstart-create-security-twin.md) för dina enheter.
1. Om du vill installera agenten på en simulerad Azure-enhet i stället för att installera på en faktisk enhet, kan du [skapa en ny virtuell Azure-dator (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) i en tillgänglig zon. 
1. [Distribuera en Azure Security Center för IoT-Säkerhetsagenten](how-to-deploy-linux-cs.md) på din IoT-enhet eller en ny virtuell dator.
1. Följ anvisningarna för [trigger_events](https://aka.ms/iot-security-github-trigger-events) för att köra en oskadlig simulering av ett angrepp.
1. Verifiera Azure Security Center för IoT-aviseringar som svar på det simulerade angreppet i föregående steg. Börja verifiera fem minuter efter att du kört skriptet.
1. Utforska [aviseringar](concept-security-alerts.md), [rekommendationer](concept-recommendations.md)och [djupgående att använda Log Analytics](how-to-security-data-access.md) med hjälp av IoT Hub. 


## <a name="next-steps"></a>Nästa steg

- Konfigurera din [lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhets agent](how-to-deploy-agent.md)
