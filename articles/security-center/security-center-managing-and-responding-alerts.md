---
title: Hantera säkerhetsaviseringar i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: 203e3718cedca693dc029a4ba20a86ca50ab3e32
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603857"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Hantera och svara på säkerhets aviseringar i Azure Security Center

Det här avsnittet visar hur du visar och bearbetar aviseringar som du har tagit emot för att skydda dina resurser. 

* Information om olika typer av aviseringar finns i [säkerhets aviserings typer](security-center-alerts-overview.md#security-alert-types).
* En översikt över hur Security Center genererar aviseringar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

> [!NOTE]
> För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](tutorial-security-policy.md). Mer information finns under [priser för Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

> [!NOTE]
> Mer information om hur Security Center identifierings funktioner fungerar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Hantera dina säkerhets aviseringar

1. På instrument panelen för Security Center går du till panelen **hot skydd** för att visa och översikt över aviseringarna.

    ![Panelen Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Klicka på panelen om du vill ha mer information om aviseringarna.

   ![Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Filtrera aviseringarna som visas genom att klicka på **filter**och på bladet **filter** som öppnas väljer du de filter alternativ som du vill använda. Listan uppdateras enligt det valda filtret. Filtrering kan vara användbart. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar

1. Klicka på en säkerhets avisering i listan **säkerhets aviseringar** . De resurser som berörs och de steg som du måste vidta för att åtgärda ett angrepp visas.

    ![Åtgärda säkerhetsaviseringar](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. När du har granskat informationen klickar du på en resurs som har angrips.

    ![Förslag på vad du kan göra om säkerhets aviseringar](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Avsnittet **allmän information** kan ge en insikt om vad som utlöste säkerhets aviseringen. Den visar information som mål resursen, källans IP-adress (när det är tillämpligt), om aviseringen fortfarande är aktiv och rekommendationer om hur du kan åtgärda detta.  

    > [!NOTE]
    >I vissa fall är käll-IP-adressen inte tillgänglig, vissa säkerhets händelser i Windows-säkerhetshändelser inkluderar inte IP-adressen.

1. De reparations steg som föreslås av Security Center varierar beroende på säkerhets aviseringen. Följ dem för varje avisering. I vissa fall kan du behöva använda andra Azure-kontroller eller-tjänster för att kunna åtgärda en varning om hot identifiering. 

    Följande avsnitt vägleder dig genom de olika aviseringarna, beroende på resurs typer:
    
    * [Aviseringar om virtuella datorer och servrar i IaaS](security-center-alerts-iaas.md)
    * [Interna beräknings aviseringar](security-center-alerts-compute.md)
    * [Aviseringar för data tjänster](security-center-alerts-data-services.md)
    
    I följande avsnitt förklaras hur Security Center använder olika telemetri som samlas in från integrering med Azure-infrastrukturen för att kunna använda ytterligare skydds lager för resurser som distribueras i Azure:
    
    * [Aviseringar för service nivå](security-center-alerts-service-layer.md)
    * [Hot identifiering för Azure WAF och Azure DDoS Protection](security-center-alerts-integration.md)
    
## <a name="see-also"></a>Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Säkerhets aviseringar i Azure Security Center](security-center-alerts-overview.md).
* [Hantera säkerhets incidenter](security-center-incident.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)