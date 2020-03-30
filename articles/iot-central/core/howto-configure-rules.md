---
title: Konfigurera regler och åtgärder i Azure IoT Central | Microsoft Docs
description: Den här how-to-artikeln visar dig, som byggare, hur du konfigurerar telemetribaserade regler och åtgärder i ditt Azure IoT Central-program.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158491"
---
# <a name="configure-rules"></a>Konfigurera regler



*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Regler i IoT Central fungerar som ett anpassningsbart svarsverktyg som utlöser på aktivt övervakade händelser från anslutna enheter. I följande avsnitt beskrivs hur regler utvärderas.

## <a name="select-target-devices"></a>Välj målenheter

Använd avsnittet målenheter för att välja på vilken typ av enheter den här regeln ska tillämpas. Med filter kan du ytterligare förfina vilka enheter som ska ingå. Filtren använder egenskaper på enhetsmallen för att filtrera ned uppsättningen enheter. Filter själva utlöser inte en åtgärd. I följande skärmdump, de enheter som är riktade är av enhetsmalltyp **Kylskåp**. Filtret anger att regeln endast ska innehålla **kylskåp** där egenskapen **Manufactured State** är lika med **Washington**.

![Villkor](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Använd flera villkor

Villkor är vilka regler som utlöser. När du lägger till flera villkor i en regel är de logiskt och tillsammans. Med andra ord måste alla villkor vara uppfyllda för att regeln ska kunna utvärderas som sann.  

I följande skärmdump kontrollerar förhållandena när temperaturen är&deg; större än 70 F och luftfuktigheten är mindre än 10. När båda dessa satser är sanna utvärderas regeln till true och utlöser en åtgärd.

![Villkor](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Använda aggregerad fönster

Regler utvärderar aggregerade tidsfönster som tumlande fönster. I skärmbilden nedan är tidsfönstret fem minuter. Var femte minut utvärderar regeln på de senaste fem minuterna av data. Data utvärderas bara en gång i fönstret som de motsvarar.

![Tumlande fönster](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Använda regler med IoT Edge-moduler

En begränsning gäller för regler som tillämpas på IoT Edge-moduler. Regler för telemetri från olika moduler utvärderas inte som giltiga regler. Ta följande som exempel. Det första villkoret för regeln är på en temperaturtelemetri från modul A. Det andra villkoret för regeln är på en fuktighetstelemetri på modul B. Eftersom de två villkoren är från olika moduler är detta en ogiltig uppsättning villkor. Regeln är inte giltig och kommer att kasta ett fel på att försöka spara regeln.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar en regel i ditt Azure IoT Central-program kan du:

> [!div class="nextstepaction"]
> [Analysera dina data i farten](howto-create-analytics.md)
