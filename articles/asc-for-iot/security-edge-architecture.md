---
title: Förstå Azure Security Center för IoT-säkerhetsmodul för IoT Edge| Microsoft-dokument
description: Förstå arkitekturen och funktionerna i Azure Security Center för IoT-säkerhetsmodul för IoT Edge.
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
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315893"
---
# <a name="azure-iot-edge-security-module"></a>Säkerhetsmodulen Azure IoT Edge

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) ger kraftfulla funktioner för att hantera och utföra affärsarbetsflöden på gränsen.
Den viktigaste delen som IoT Edge spelar i IoT-miljöer gör det särskilt attraktivt för skadliga aktörer.

Azure Security Center för IoT-säkerhetsmodul ger en omfattande säkerhetslösning för dina IoT Edge-enheter.
Azure Security Center för IoT-modul samlar in, sammanställer och analyserar rådsäkerhetsdata från ditt operativsystem och behållarsystem till användbara säkerhetsrekommendationer och aviseringar.

I likhet med Azure Security Center för IoT-säkerhetsagenter för IoT-enheter är Azure Security Center for IoT Edge-modulen mycket anpassningsbar via modultvillingen.
Mer information finns i [Konfigurera din agent.](how-to-agent-configuration.md)

Azure Security Center för IoT-säkerhetsmodul för IoT Edge erbjuder följande funktioner:

- Samlar in råsäkerhetshändelser från det underliggande operativsystemet (Linux) och IoT Edge Container-systemen.
  
  Mer information om tillgängliga säkerhetsdatainsamlare finns i [Azure Security Center för IoT-agentkonfiguration.](how-to-agent-configuration.md)

- Analys av IoT Edge-distributionsmanifest.

- Sammanställer råsäkerhetshändelser i meddelanden som skickas via [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Ta bort konfigurationen med hjälp av säkerhetsmodultvillingen.

  Mer information finns i [Konfigurera en Azure Security Center för IoT-agent.](how-to-agent-configuration.md)

Azure Security Center för IoT-säkerhetsmodul för IoT Edge körs i ett privilegierat läge under IoT Edge.
Privilegierat läge krävs för att modulen ska kunna övervaka operativsystemet och andra IoT Edge-moduler.

## <a name="module-supported-platforms"></a>Plattformar som stöds av modul

Azure Security Center för IoT-säkerhetsmodul för IoT Edge är för närvarande endast tillgängligt för Linux. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om arkitekturen och funktionerna i Azure Security Center för IoT-säkerhetsmodul för IoT Edge.

Om du vill fortsätta komma igång med Azure Security Center för IoT-distribution använder du följande artiklar:

- Distribuera [säkerhetsmodul för IoT Edge](how-to-deploy-edge.md)
- Lär dig hur du [konfigurerar säkerhetsmodulen](how-to-agent-configuration.md)
- Granska [förutsättningarna](service-prerequisites.md) för Azure Security Center för IoT-tjänsten
- Lär dig hur du [aktiverar Azure Security Center för IoT-tjänst i din IoT-hubb](quickstart-onboard-iot-hub.md)
- Läs mer om tjänsten från vanliga frågor och svar om [Azure Security Center för IoT](resources-frequently-asked-questions.md)