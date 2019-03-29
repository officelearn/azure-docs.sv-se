---
title: Kom igång med Azure Security Center (ASC) för förhandsversionen av IoT | Microsoft Docs
description: Kom igång förstå det grundläggande arbetsflödet i ASC för IoT-funktioner och -tjänsten.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 1186b362cf8f59f24020ae9afa3526e2e27b1794
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575223"
---
# <a name="get-started-with-azure-security-center-asc-for-iot"></a>Kom igång med Azure Security Center (ASC) för IoT 

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en förklaring av de olika byggstenarna för ASC för IoT-tjänsten och beskriver hur du kommer igång med [aktiverar tjänsten](quickstart-onboard-iot-hub.md). 

ASC för IoT kan integreras smidigt i din IoT-hubb för att tillhandahålla säkerhetsanalys av IoT hub-konfiguration, enhetsidentitet och hub-enhet kommunikationsmönster.
För ökad säkerhet-funktioner ger ASC för IoT agentbaserad insamling av säkerhetsdata från dina IoT-enheter.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC för IoT sömlös IoT Hub-integrering

När du försöker skydda dina enskilda IoT-enheter krävs kan samla in data direkt från enheterna eller från sitt nätverk. För att stödja den här insatsen erbjuder ASC för IoT en uppsättningen av låg-fotavtryck security agenter för övervakning och härdning.

I ASC för förhandsversionen av IoT-referensarkitekturen för Linux och Windows security agenter, både i C# och C tillhandahålls.
Agenterna hantera raw händelseinsamling från enhetens operativsystem, händelse aggregering till att minska kostnaderna och konfiguration via en enhetstvilling för modulen.
Säkerhetsvarningar skickas via din IoT-hubb i ASC för IoT-tjänster för analys.

## <a name="asc-for-iot-basics"></a>ASC för IoT-grunderna

Välj arbetsflöde-scenario som bäst uppfyller dina IoT-enhets- och miljöinformation krav:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Kom igång med ASC för IoT smidig integrering i IoT Hub 

>[!Note]
>Det här arbetsflödet kan du använda tjänsten utan att använda ASC för agenter för IoT-säkerhet. 

Om du vill aktivera övervakning av din enhet Använd Identitetshantering, enhet till molnet och molnet till enheten kommunikationsmönster, följande grundläggande arbetsflöde för testning och för att starta tjänsten: 

1. [Aktivera ASC för IoT-tjänsten på IoT Hub](quickstart-onboard-iot-hub.md)
1. Om din IoT-hubb har inga registrerade enheter [registrera en ny enhet](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Skapa en modul för maskinvarusäkerhet ascforiot](quickstart-create-security-twin.md) för dina enheter. 
1. Definiera enhets- och normalt via [anpassade aviseringar](quickstart-create-custom-alerts.md). 
1. Utföra tester för att verifiera status för tjänsten och system. 
1. Utforska [aviseringar](concept-security-alerts.md), [rekommendationer](concept-recommendations.md), och [djupdykning använda Log Analytics](how-to-security-data-access.md) med hjälp av IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Kom igång med ASC för agenter för IoT-säkerhet

Genom att utnyttja ASC för IoT förbättrad säkerhetsfunktioner, till exempel övervaka fjärranslutningar, aktiva program, inloggningshändelser och bästa praxis för OS-konfiguration med hjälp av följande grundläggande arbetsflöde att testa och aktivera tjänsten: 

1. [Aktivera ASC för IoT-tjänsten till IoT Hub](quickstart-onboard-iot-hub.md)
1. Om din IoT-hubb har inga registrerade enheter [registrera en ny enhet](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Skapa en modul för maskinvarusäkerhet azureiotsecurity](quickstart-create-security-twin.md) för dina enheter.
1. Installera agenten på en Azure-simulerad enhet i stället för att installera på en enhet, [snurra upp en ny Azure virtuell dator (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) i en zon som är tillgängliga. 
1. [Distribuera en ASC för IoT-säkerhetsagenten](how-to-deploy-linux-cs.md) på din IoT-enhet eller en ny virtuell dator.
1. Följ anvisningarna för [trigger_events](https://aka.ms/iot-security-github-trigger-events) att köra en simulering av märker att ofarliga attacker.
1. Kontrollera ASC för IoT-aviseringar som svar på simulerade attacker i föregående steg. 
    - Börja verifieringen fem minuter när skriptet har körts.
1. Utforska [aviseringar](concept-security-alerts.md), [rekommendationer](concept-recommendations.md), och [djupdykning använda Log Analytics](how-to-security-data-access.md) med hjälp av IoT Hub. 

## <a name="next-steps"></a>Nästa steg

- Aktivera [ASC för IoT](quickstart-onboard-iot-hub.md)
- Konfigurera din [lösning](quickstart-configure-your-solution.md)
- [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
- Konfigurera [anpassade aviseringar](quickstart-create-custom-alerts.md)
- [Distribuera en säkerhetsagenten](how-to-deploy-agent.md)
