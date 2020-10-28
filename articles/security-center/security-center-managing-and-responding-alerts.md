---
title: Hantera säkerhetsaviseringar i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 75ca0438336825bf8d4bbdc6e08eca109f430fde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785926"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Hantera och besvara säkerhetsaviseringar i Azure Security Center

Det här avsnittet visar hur du visar och bearbetar aviseringar som du har tagit emot för att skydda dina resurser. 

* Information om olika typer av aviseringar finns i [säkerhets aviserings typer](alerts-reference.md).
* En översikt över hur Security Center genererar aviseringar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md).

> [!NOTE]
> Aktivera Avancerad identifiering genom att aktivera Azure Defender. Det finns en kostnadsfri utvärderingsversion. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](tutorial-security-policy.md). Mer information finns under [priser för Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

> [!NOTE]
> Mer information om hur Security Center identifierings funktioner fungerar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Hantera dina säkerhets aviseringar

1. På instrument panelen för Security Center går du till panelen  **hot skydd** för att visa och översikt över aviseringarna.

    ![Panelen Säkerhetsaviseringar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Klicka på panelen om du vill ha mer information om aviseringarna.

   ![Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Filtrera aviseringarna som visas genom att klicka på **filter** och på bladet **filter** som öppnas väljer du de filter alternativ som du vill använda. Listan uppdateras enligt det valda filtret. Filtrering kan vara användbart. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar

1. Klicka på en säkerhets avisering i listan **säkerhets aviseringar** . De resurser som berörs och de steg som du måste vidta för att åtgärda ett angrepp visas.

    ![Åtgärda säkerhetsaviseringar](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. När du har granskat informationen klickar du på en resurs som har angrips.

    Den vänstra rutan på sidan säkerhets avisering visar information på hög nivå om säkerhets aviseringen: rubrik, allvarlighets grad, status, aktivitets tid, beskrivning av den misstänkta aktiviteten och den berörda resursen. Tillsammans med den berörda resursen är de Azure-taggar som är relevanta för resursen. Använd dessa för att härleda resursens organisatoriska kontext vid utredning av aviseringen.

    Den högra rutan innehåller fliken **aviserings information** som innehåller mer information om aviseringen för att hjälpa dig att undersöka problemet: IP-adresser, filer, processer med mera.
     
    ![Förslag på vad du kan göra om säkerhets aviseringar](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    I den högra rutan är fliken **vidta åtgärd** . Använd den här fliken för att vidta ytterligare åtgärder för säkerhets aviseringen. Åtgärder som:
    - *Minimera hotet* – ger manuella reparations steg för denna säkerhets avisering
    - *Förhindra framtida attacker* – ger säkerhets rekommendationer för att minska attack ytan, öka säkerheten position och därmed förhindra framtida attacker
    - *Utlös automatiserat svar* – ger möjlighet att utlösa en Logic app som ett svar på denna säkerhets avisering
    - *Ignorera liknande aviseringar* – ger möjlighet att ignorera framtida aviseringar med liknande egenskaper om aviseringen inte är relevant för din organisation

    ![Vidta åtgärd-fliken](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Se även

I det här dokumentet har du lärt dig hur du visar säkerhets aviseringar. Se följande sidor för relaterat material:

- [Konfigurera regler för att utelämna varningar](alerts-suppression-rules.md)
- [Automatisera svar på Security Center utlösare](workflow-automation.md)
