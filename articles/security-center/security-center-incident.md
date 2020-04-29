---
title: Hantera säkerhets incidenter i Azure Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att använda Azure Security Center för att hantera säkerhets incidenter.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415672"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Hantera säkerhets incidenter i Azure Security Center

Prioritering och undersöker säkerhets aviseringar kan ta lång tid för även de mest erfarna säkerhetsanalytikerna och för många är det svårt att till och med veta var du ska börja. Med hjälp av [analys](security-center-detection-capabilities.md) för att ansluta informationen mellan olika [säkerhetsaviseringar](security-center-managing-and-responding-alerts.md), kan Security Center förse dig med en enda vy av en attackkampanj och alla relaterade aviseringar – du kan snabbt förstå vilka åtgärder angriparen vidtog och vilka resurser som har påverkats.

I det här avsnittet beskrivs incidenter i Security Center och hur du använder dem för att åtgärda deras aviseringar.

## <a name="what-is-a-security-incident"></a>Vad är en säkerhetsincident?

I Security Center är en säkerhetsincident en sammanställning av alla aviseringar för en resurs som överensstämmer med särskilda [händelsekedjemönster](alerts-reference.md#intentions). Incidenter visas i listan [säkerhets aviseringar](security-center-managing-and-responding-alerts.md) . Klicka på en incident om du vill visa de relaterade aviseringarna, vilket gör att du kan få mer information om varje förekomst.

## <a name="managing-security-incidents"></a>Hantera säkerhetsincidenter

1. På instrument panelen Security Center klickar du på panelen **säkerhets aviseringar** . Incidenter och aviseringar visas. Observera att säkerhetsincidentbeskrivningen har en annan ikon jämfört med andra aviseringar.

    ![Visa säkerhets incidenter](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Om du vill visa detaljer klickar du på en incident. Bladet **identifierad säkerhets incident** visar ytterligare information. Avsnittet **allmän information** kan ge en insikt om vad som utlöste säkerhets aviseringen. Den visar information som mål resursen, källans IP-adress (när det är tillämpligt), om aviseringen fortfarande är aktiv och rekommendationer om hur du kan åtgärda detta.  

    ![Svara på säkerhets incidenter i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Klicka på en avisering om du vill ha mer information om varje avisering. Vilka åtgärder som föreslås av Security Center varierar beroende på typ av säkerhetsavisering.

   > [!NOTE]
   > Samma avisering kan finnas som en del av en incident, och visas som en fristående avisering.

    ![Aviseringsinformation](./media/security-center-incident/security-center-incident-alert.png)

1. Följ de reparations steg som har angetts för varje avisering.


## <a name="see-also"></a>Se även
I det här dokumentet har du fått lära dig hur du använder säkerhetsincidentfunktionen i Security Center. Relaterad information finns i följande avsnitt:

* [Skydd mot hot i Azure Security Center](threat-protection.md)
* [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
* [Hantera säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)