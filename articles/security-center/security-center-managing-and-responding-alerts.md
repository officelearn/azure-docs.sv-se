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
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415684"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Hantera och besvara säkerhetsaviseringar i Azure Security Center

I det här avsnittet visas hur du visar och bearbetar aviseringar som du har fått för att skydda dina resurser. 

* Mer information om de olika typerna av aviseringar finns i [Säkerhetsvarningstyper](alerts-reference.md).
* En översikt över hur Security Center genererar aviseringar finns i [Hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md).

> [!NOTE]
> För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](tutorial-security-policy.md). Mer information finns under [priser för Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

> [!NOTE]
> Mer information om hur identifieringsfunktioner för Security Center fungerar finns i [Hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Hantera dina säkerhetsaviseringar

1. På instrumentpanelen i Säkerhetscenter kan du se panelen **Skydd mot hot** om du vill visa och översikt över aviseringarna.

    ![Panelen Säkerhetsaviseringar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Klicka på panelen om du vill se mer information om aviseringarna.

   ![Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Om du vill filtrera de aviseringar som visas klickar du på **Filtrera**och väljer de filteralternativ som du vill använda från **filterbladet** som öppnas. Listan uppdateras enligt det valda filtret. Filtrering kan vara till stor hjälp. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar

1. Klicka på en säkerhetsavisering i listan **Säkerhetsvarningar.** De resurser som är inblandade och de åtgärder du behöver vidta för att åtgärda en attack visas.

    ![Åtgärda säkerhetsaviseringar](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. När du har granskat informationen klickar du på en resurs som attackerades.

    ![Förslag på vad du ska göra med säkerhetsvarningar](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Avsnittet **Allmän information** kan ge en inblick i vad som utlöste säkerhetsaviseringen. Den visar information som målresursen, käll-IP-adress (i förekommande fall), om aviseringen fortfarande är aktiv och rekommendationer om hur du åtgärdar.  

    > [!NOTE]
    >I vissa fall är käll-IP-adressen inte tillgänglig, vissa Windows-säkerhetshändelser loggar inte innehåller IP-adressen.

1. Vilka åtgärdssteg som föreslås av Security Center varierar beroende på typen av säkerhetsavisering. Följ dem för varje avisering. 

    I vissa fall, för att minska en säkerhetsavisering, kan du behöva använda andra Azure-kontroller eller tjänster för att implementera den rekommenderade reparationen. 

## <a name="see-also"></a>Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md).
* [Hantering av säkerhetsincidenter](security-center-incident.md)