---
title: Hantera säkerhetsaviseringar i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du hanterar säkerhetsincidenter med hjälp av funktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.openlocfilehash: 9f87edc0717631775acec315dda61323b8fa5909
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777551"
---
# <a name="handling-security-incidents-in-azure-security-center"></a>Hantera säkerhetsincidenter i Azure Security Center
Sortering och undersökning av säkerhetsaviseringar kan ta lång tid för även de mest skickliga säkerhetsanalytiker och för många är det svårt att ens veta var man ska börja. Med hjälp av [analys](security-center-detection-capabilities.md) för att ansluta informationen mellan olika [säkerhetsaviseringar](security-center-managing-and-responding-alerts.md), kan Security Center förse dig med en enda vy av en attackkampanj och alla relaterade aviseringar – du kan snabbt förstå vilka åtgärder angriparen vidtog och vilka resurser som har påverkats.

Det här dokumentet beskriver hur du använder funktionen för säkerhetsavisering i Security Center till stöd vid hantering av säkerhetsincidenter.

## <a name="what-is-a-security-incident"></a>Vad är en säkerhetsincident?
I Security Center är en säkerhetsincident en sammanställning av alla aviseringar för en resurs som överensstämmer med särskilda [händelsekedjemönster](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Incidenter visas på panelen och bladet [Säkerhetsaviseringar](security-center-managing-and-responding-alerts.md). En incident visar en lista över relaterade aviseringar så att du kan få mer information om varje förekomst.

## <a name="managing-security-incidents"></a>Hantera säkerhetsincidenter
Du kan se aktuella säkerhetsincidenter på panelen för säkerhetsaviseringar. Öppna Azure Portal och följ stegen nedan om du vill ha mer information om varje säkerhetsincident:

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.

    ![Panelen Säkerhetsaviseringar i Security Center](./media/security-center-incident/security-center-incident-fig1.png)

2. Klicka på den här panelen för att expandera den. Om en säkerhetsincident identifieras, visas den under säkerhetsaviseringsdiagrammet enligt nedan:

    ![Säkerhetsincident](./media/security-center-incident/security-center-incident-fig2.png)

3. Observera att säkerhetsincidentbeskrivningen har en annan ikon jämfört med andra aviseringar. Klicka på den för att visa mer information om den här incidenten.

    ![Säkerhetsincident](./media/security-center-incident/security-center-incident-fig3.png)

4. På **incident**-bladet visas mer information om säkerhetsincidenten, inklusive en fullständig beskrivning, allvarlighetsgrad (hög i det här fallet), nuvarande status (i det här fallet är den fortfarande *aktiv*, vilket innebär att användaren inte vidtagit någon åtgärd. Det kan göras genom att högerklicka på incidenten i bladet **Säkerhetsaviseringar**), resursen som angripits (i det här fallet *VM1*), steg för att åtgärda problemet och längst ner har du de aviseringar som finns med i den här incidenten. Om du vill få mer information om varje avisering behöver du bara klicka på den så öppnas ett annat blad, enligt nedan:

    ![Säkerhetsincident](./media/security-center-incident/security-center-incident-fig4.png)

Informationen på det här bladet varierar beroende på aviseringen. Mer information om att hantera dessa aviseringar finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Viktigt att tänka på för den här funktionen:

* Med ett nytt filter kan du anpassa vyn till ”endast incident” eller ”endast aviseringar” eller både och.
* Samma avisering kan vara del av en incident (om tillämpligt) eller visas som en fristående avisering.

## <a name="see-also"></a>Se även
I det här dokumentet har du fått lära dig hur du använder säkerhetsincidentfunktionen i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
