---
title: Hantera säkerhets incidenter i Azure Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att använda Azure Security Center för att hantera säkerhets incidenter.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 2a22039b3499f1ba0f2d7acb2e86a1991d35a380
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202130"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Hantera säkerhets incidenter i Azure Security Center

Prioritering och undersöker säkerhets aviseringar kan ta lång tid för även de mest erfarna säkerhetsanalytikerna och för många är det svårt att till och med veta var du ska börja. Med hjälp av [analys](security-center-detection-capabilities.md) för att ansluta informationen mellan olika [säkerhetsaviseringar](security-center-managing-and-responding-alerts.md), kan Security Center förse dig med en enda vy av en attackkampanj och alla relaterade aviseringar – du kan snabbt förstå vilka åtgärder angriparen vidtog och vilka resurser som har påverkats.

I det här avsnittet beskrivs incidenter i Security Center och hur du använder dem för att åtgärda deras aviseringar.

## <a name="what-is-a-security-incident"></a>Vad är en säkerhetsincident?

I Security Center är en säkerhetsincident en sammanställning av alla aviseringar för en resurs som överensstämmer med särskilda [händelsekedjemönster](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Incidenter visas i listan [säkerhets aviseringar](security-center-managing-and-responding-alerts.md) . Klicka på en incident om du vill visa de relaterade aviseringarna, vilket gör att du kan få mer information om varje förekomst.

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

För ytterligare information om aviseringar, [Hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md).

Följande avsnitt vägleder dig genom de olika aviseringarna, beroende på resurs typer:

* [Aviseringar om virtuella datorer och servrar i IaaS](security-center-alerts-iaas.md)
* [Interna beräknings aviseringar](security-center-alerts-compute.md)
* [Aviseringar för data tjänster](security-center-alerts-data-services.md)

I följande avsnitt förklaras hur Security Center använder olika telemetri som samlas in från integrering med Azure-infrastrukturen för att kunna använda ytterligare skydds lager för resurser som distribueras i Azure:

* [Aviseringar för service nivå](security-center-alerts-service-layer.md)
* [Integrering med Azures säkerhets produkter](security-center-alerts-integration.md)

## <a name="see-also"></a>Se också
I det här dokumentet har du fått lära dig hur du använder säkerhetsincidentfunktionen i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Säkerhets aviseringar i Azure Security Center](security-center-alerts-overview.md).
* [Hantera säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
