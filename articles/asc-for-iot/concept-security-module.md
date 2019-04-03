---
title: Förstå Azure Security Center för IoT-säkerhetsmodul twins förhandsversion | Microsoft Docs
description: Läs mer om begreppet modultvillingar för säkerhet och hur de används i Azure Security Center för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c5814b188c73ea03094d7dae565e40ca09e705c2
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862445"
---
# <a name="security-module"></a>Säkerhetsmodul

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar hur Azure Security Center (ASC) för IoT använder enhetstvillingar och moduler. 

## <a name="device-twins"></a>Enhetstvillingar

För IoT-lösningar som skapats i Azure, spela enhetstvillingar en viktig roll i både enhetshantering och automatisering av affärsprocesser.  

ASC för IoT erbjuder fullständig integrering med din befintliga IoT plattform för enhetshantering, så att du kan hantera säkerhetsstatusen enheten samt kontrollera användning av befintliga funktioner för kontroll av enheter. Integration uppnås genom att använda IoT-hubben twin mekanism.  

Läs mer om begreppet [enhetstvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) i Azure IoT Hub. 

## <a name="security-module-twins"></a>Security modultvillingar

ASC för IoT upprätthåller en security modultvillingen för varje enhet i tjänsten.
Modultvilling security innehåller all information som rör enhetssäkerhet för varje specifik enhet i din lösning.
Egenskaper för enhet security underhålls i en dedikerad security modultvilling för säkrare kommunikation och för att aktivera uppdateringar och underhåll som kräver färre resurser.  

Se [skapa security modultvilling](quickstart-create-security-twin.md) och [konfigurera säkerhetsagenter](how-to-agent-configuration.md) om du vill lära dig mer om att skapa, anpassa och konfigurera läsningen. Se [förstå modultvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) vill veta mer om begreppet modultvillingar i IoT Hub. 
 

## <a name="see-also"></a>Se också
- [ASC för förhandsversionen av IoT](overview.md)
- [Distribuera säkerhetsagenter](how-to-deploy-agent.md)
- [Security-agenten autentiseringsmetoder](concept-security-agent-authentication-methods.md)