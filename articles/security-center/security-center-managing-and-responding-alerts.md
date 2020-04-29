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
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 8e44ce594375deeac47f037515d96c57d15c8359
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398410"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Hantera och besvara säkerhetsaviseringar i Azure Security Center

Det här avsnittet visar hur du visar och bearbetar aviseringar som du har tagit emot för att skydda dina resurser. 

* Information om olika typer av aviseringar finns i [säkerhets aviserings typer](alerts-reference.md).
* En översikt över hur Security Center genererar aviseringar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md).

> [!NOTE]
> För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](tutorial-security-policy.md). Mer information finns under [priser för Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

> [!NOTE]
> Mer information om hur Security Center identifierings funktioner fungerar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Hantera dina säkerhets aviseringar

1. På instrument panelen för Security Center går du till panelen **hot skydd** för att visa och översikt över aviseringarna.

    ![Panelen Säkerhetsaviseringar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

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

1. Vilka åtgärdssteg som föreslås av Security Center varierar beroende på typen av säkerhetsavisering. Följ dem för varje avisering. 

    I vissa fall kan du behöva använda andra Azure-kontroller eller-tjänster för att kunna åtgärda en säkerhets avisering. 

## <a name="see-also"></a>Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

- [Microsoft Learn-modulen för att använda aviserings funktionerna i Azure Security Center för att se och svara på hot](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
* [Säkerhets aviseringar i Azure Security Center](security-center-alerts-overview.md).
* [Hantera säkerhets incidenter](security-center-incident.md)