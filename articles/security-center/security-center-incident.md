---
title: Hantera säkerhetsincidenter i Azure Security Center | Microsoft-dokument
description: Det här dokumentet hjälper dig att använda Azure Security Center för att hantera säkerhetsincidenter.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415672"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Hantera säkerhetsincidenter i Azure Security Center

Triage och undersöka säkerhetsvarningar kan vara tidskrävande för även de mest skickliga säkerhetsanalytiker, och för många är det svårt att ens veta var man ska börja. Med hjälp av [analys](security-center-detection-capabilities.md) för att ansluta informationen mellan olika [säkerhetsaviseringar](security-center-managing-and-responding-alerts.md), kan Security Center förse dig med en enda vy av en attackkampanj och alla relaterade aviseringar – du kan snabbt förstå vilka åtgärder angriparen vidtog och vilka resurser som har påverkats.

I det här avsnittet beskrivs incidenter i Security Center och hur du använder åtgärda deras aviseringar.

## <a name="what-is-a-security-incident"></a>Vad är en säkerhetsincident?

I Security Center är en säkerhetsincident en sammanställning av alla aviseringar för en resurs som överensstämmer med särskilda [händelsekedjemönster](alerts-reference.md#intentions). Incidenter visas i listan [Säkerhetsvarningar.](security-center-managing-and-responding-alerts.md) Klicka på en incident för att visa relaterade aviseringar, vilket gör att du kan få mer information om varje förekomst.

## <a name="managing-security-incidents"></a>Hantera säkerhetsincidenter

1. Klicka på panelen Säkerhetsvarningar på instrumentpanelen **i Säkerhetscenter.** Incidenter och aviseringar visas. Observera att säkerhetsincidentbeskrivningen har en annan ikon jämfört med andra aviseringar.

    ![Visa säkerhetsincidenter](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Klicka på en incident om du vill visa information. Bladet **säkerhetsincident som upptäckts** visar ytterligare information. Avsnittet **Allmän information** kan ge en inblick i vad som utlöste säkerhetsaviseringen. Den visar information som målresursen, käll-IP-adress (i förekommande fall), om aviseringen fortfarande är aktiv och rekommendationer om hur du åtgärdar.  

    ![Svara på säkerhetsincidenter i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Om du vill ha mer information om varje avisering klickar du på en avisering. Vilka åtgärder som föreslås av Security Center varierar beroende på typ av säkerhetsavisering.

   > [!NOTE]
   > Samma avisering kan finnas som en del av en incident, samt att vara synlig som en fristående avisering.

    ![Aviseringsinformation](./media/security-center-incident/security-center-incident-alert.png)

1. Följ de reparationssteg som anges för varje avisering.


## <a name="see-also"></a>Se även
I det här dokumentet har du fått lära dig hur du använder säkerhetsincidentfunktionen i Security Center. Relaterad information finns i följande:

* [Skydd mot hot i Azure Security Center](threat-protection.md)
* [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
* [Hantera säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)