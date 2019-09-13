---
title: Förstå Azure Security Center för IoT-kostnader | Microsoft Docs
description: Lär dig mer om kostnaderna som är associerade med Azure Security Center för IoT och hur du styr dem.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: e742848df56e4e2be1b9edf42586e642f0c938cb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933793"
---
# <a name="pricing-and-associated-costs"></a>Prissättning och associerade kostnader

Den här artikeln beskriver Azure Security Center för IoT-prismodellen, sammanfattar alla associerade kostnader och förklarar hur du hanterar dem.

## <a name="pricing"></a>Prissättning

Azure Security Center för IoT-prismodellen består av två delar och faktureras när en IoT Hub [aktive ras](quickstart-onboard-iot-hub.md) i Azure Security Center för IoT:

- Kostnad per enhet – inbyggda säkerhetsfunktioner baserat på analys av IoT Hub loggar.

- Kostnad per meddelande – utökade säkerhetsfunktioner baserat på säkerhets meddelanden från IoT Edge eller löv enheter.

  >[!Note]
  > Säkerhets meddelanden kommer också att ha kvot förbrukning på IoT Hub.

Mer information finns i [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Tillhör ande kostnader

Azure Security Center for IoT har två typer av associerade kostnader som inte ingår i direkt prissättningen:

- IoT Hub kvot förbrukning

- Log Analytics lagrings kostnader

Du kan minska kostnaderna genom att väljer bort från vissa funktioner genom att ändra dina inställningar.

Ändra inställningarna:

1. Öppna IoT Hub.

2. Klicka på **Översikt**under **säkerhet**.

3. Klicka på **Inställningar**.

Följande tabell innehåller en sammanfattning av de associerade kostnaderna och konsekvenserna av varje alternativ.

|     | Användning | Kommentar |
| --- | --- | --- |
| **IoT Hub kvot förbrukning** |  |
| [Exportera enhets](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) jobb (dubbel export) | En gång om dagen | Inaktivera _insamling av dubbla metadata_ |
| **Log Analytics lagring** |  |
| Enhets rekommendation och aviseringar| Säkerhets rekommendationer och aviseringar som genererats av tjänsten | Inte valfritt |
| Rå säkerhets data| Rå säkerhets data från IoT-enheter som samlas in av säkerhets agenter | Inaktivera _säkerhets händelser för lagring av rå enheter_ |
|

>[!Important]
> Väljer out har allvarliga följder för att Azure Security Center för tillgänglighet för IoT-säkerhetsfunktioner. 
  
| Avanmäl | Effekter |
| --- | --- |
| _Samling med dubbla metadata_ | Inaktivera [anpassade aviseringar](quickstart-create-custom-alerts.md) |
| | Inaktivera rekommendationer för IoT Edge manifest |
| | Inaktivera rekommendationer och aviseringar för enhets identitet |
| _Lagra säkerhets händelser för RAW-enhet_ | Information om bas linje rekommendationer för enhets operativ system är inte tillgänglig |
| | Information om [varnings](concept-security-alerts.md) -och [rekommendations](concept-recommendations.md) undersökningar är inte tillgängliga |
|


## <a name="see-also"></a>Se också

- Få åtkomst till dina [rå säkerhets data](how-to-security-data-access.md)
- [Undersök en enhet](how-to-investigate-device.md)
- Förstå och utforska [säkerhets rekommendationer](concept-recommendations.md)
- Förstå och utforska [säkerhets aviseringar](concept-security-alerts.md)
