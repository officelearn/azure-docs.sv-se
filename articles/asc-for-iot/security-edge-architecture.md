---
title: Förstå Azure Security Center för IoT-säkerhetsmodulen för IoT Edge för hands version | Microsoft Docs
description: Förstå arkitekturen och funktionerna i Azure Security Center för IoT Security-modulen för IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596849"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge säkerhetsmodulen

> [!IMPORTANT]
> Azure Security Center för IoT-tjänsten för IoT Edge finns för närvarande i offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) tillhandahåller kraftfulla funktioner för att hantera och utföra affärs arbets flöden i gränsen.
Den nyckel del som IoT Edge spelar i IoT-miljöer gör det särskilt attraktivt för skadliga aktörer.

Azure Security Center för IoT-säkerhetsmodulen innehåller en omfattande säkerhetslösning för dina IoT Edge-enheter.
Azure Security Center för IoT-modulen samlar in, samlar in och analyserar rå säkerhets data från operativ systemet och behållar systemet till rekommenderade säkerhets rekommendationer och aviseringar.

Precis som Azure Security Center för IoT-säkerhetsagenter för IoT-enheter, är Azure Security Center för IoT Edge modul mycket anpassningsbar genom sin modul.
Mer information finns i [Konfigurera agenten](how-to-agent-configuration.md) .

Azure Security Center för IoT Security-modulen för IoT Edge erbjuder följande funktioner:

- Samlar in rå säkerhets händelser från det underliggande operativ systemet (Linux) och IoT Edge container system.
  
  Se [Azure Security Center för IoT agent-konfiguration](how-to-agent-configuration.md) för att lära dig mer om tillgängliga säkerhets data insamlare.

- Analys av IoT Edge distributions manifest.

- Sammanställer rå säkerhets händelser till meddelanden som skickas via [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Ta bort konfigurationen genom att använda säkerhetsmodulen dubbla.

  Mer information finns i [Konfigurera ett Azure Security Center för IoT-agent](how-to-agent-configuration.md) .

Azure Security Center för IoT-säkerhetsmodulen för IoT Edge körs i privilegierat läge under IoT Edge.
Privilegierat läge krävs för att modulen ska kunna övervaka operativ systemet och andra IoT Edge moduler.

## <a name="module-supported-platforms"></a>Plattformar som stöds av moduler

Azure Security Center för IoT-säkerhetsmodulen för IoT Edge är för närvarande bara tillgänglig för Linux. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om arkitekturen och funktionerna i Azure Security Center för IoT Security-modulen för IoT Edge.

Använd följande artiklar om du vill fortsätta att komma igång med Azure Security Center för IoT-distribution:

- Distribuera [säkerhetsmodulen för IoT Edge](how-to-deploy-edge.md)
- Lär dig hur du [konfigurerar din säkerhetsmodul](how-to-agent-configuration.md)
- Granska förutsättningarna för Azure Security Center för IoT [-tjänster](service-prerequisites.md)
- Lär dig hur du [aktiverar Azure Security Center för IoT-tjänsten i din IoT Hub](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten i [vanliga frågor och svar om Azure Security Center for IoT](resources-frequently-asked-questions.md)