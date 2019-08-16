---
title: Hantera säkerhetsaviseringar i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516104"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Hantera och åtgärda säkerhetsaviseringar i Azure Security Center
Det här dokumentet beskriver hur du använder Azure Security Center för att hantera och svara på säkerhetsvarningar.

> [!NOTE]
> För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. Det finns en kostnadsfri utvärderingsversion. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](tutorial-security-policy.md). Mer information finns under [priser för Azure Security Center](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.


> [!NOTE]
> Mer information om hur identifieringsfunktionerna i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Hantera säkerhetsaviseringar
Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)** . Nedan beskrivs hur du gör för att se mer information om de olika aviseringarna.

1. På instrumentpanelen i Security Center hittar du panelen **Säkerhetsaviseringar**.

    ![Panelen Säkerhetsaviseringar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Klicka på panelen för att öppna **Säkerhetsaviseringar** för att se mer information om aviseringarna.

   ![Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Längst ned på sidan visas information om de olika aviseringarna. Du kan sortera listan genom att klicka på den kolumn som du vill sortera efter. Här följer en förklaring av de olika kolumnerna:

* **Beskrivning**: En kort förklaring av aviseringen.
* **Antal**: En lista över alla aviseringar för den här typen som har identifierats på en angiven dag.
* **Identifierat av**: Tjänsten som ansvarar för att utlösa aviseringen.
* **Datum**: Det datum då händelsen inträffade.
* **Tillstånd**: Aktuellt tillstånd för den aviseringen. Det finns två tillstånd:
  * **Aktiv**: Säkerhets aviseringen har identifierats.
  * Avstängt: Säkerhets aviseringen har avslagits av användaren. Den här statusen används vanligt vis för aviseringar som har undersökts och minimerats eller visat sig inte vara ett faktiskt angrepp.
* **Allvarlighets grad**: Allvarlighets graden, som kan vara hög, medel eller låg.

> [!NOTE]
> Säkerhetsvarningar som genererats av Security Center visas också under Azure-aktivitetsloggen. Mer information om hur du kommer åt Azure-aktivitetsloggen finns i [View activity logs to audit actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) (Visa aktivitetsloggar för att granska åtgärder på resurser).
>


### <a name="alert-severity"></a>Allvarlighetsgrad för avisering

-   **Hög**: Det finns en hög sannolikhet för att din resurs komprometteras. Du bör titta på det direkt. Security Center har hög exakthet i både den skadliga avsikten och i de resultat som används för att utfärda aviseringen. Till exempel en avisering som identifierar körningen av ett känt skadligt verktyg, till exempel Mimikatz, ett vanligt verktyg som används för stöld av autentiseringsuppgifter. 
-   **Medel**: Detta är troligen en misstänkt aktivitet som kan tyda på att en resurs komprometteras.
Security Center tillförlitlighet i analys eller sökning är medel och säkerheten för den skadliga avsikten är medel hög till hög. Detta brukar vara maskin inlärning eller avvikande identifieringar. Till exempel ett inloggnings försök från en avvikande plats.
-   **Låg**: Detta kan vara ett ofarligt positivt eller blockerat angrepp. 
    - Security Center är inte tillräckligt tryggt att avsikten är skadlig och att aktiviteten kan vara Innocent. Logg rensning är till exempel en åtgärd som kan inträffa när en angripare försöker dölja sina spår, men i många fall är en rutin åtgärd som utförs av administratörer.
    - Security Center meddelar dig normalt inte när attacker blockeras, om det inte är ett intressant ärende som vi föreslår att du tittar på. 
-   **Information**: Endast informations aviseringar visas när du ökar detalj nivån i en säkerhets incident, eller om du använder REST API med ett angivet aviserings-ID. En incident består vanligt vis av ett antal aviseringar, varav vissa kan visas på egen hand som endast information, men i samband med de andra aviseringarna kan det vara betrodd av en närmare titt.  

> [!NOTE]
> Om du använder **2015-06-01-Preview API-** versionen, finns det skillnader i vilka typer av larm allvarlighets grader som tillämpas på vilka scenarier, från vad som anges ovan.  

### <a name="filtering-alerts"></a>Filtrera varningar
Aviseringarna kan filtreras efter datum, status och allvarlighetsgrad. Att filtrera kan vara bra när du vill begränsa hur många aviseringar du vill se. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

1. Klicka på **Filter** i **Säkerhetsaviseringar**. **Filter** öppnas där du kan välja datum, status och vilka allvarlighetsgrader du vill se.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar
Om du klickar på en säkerhetsavisering får du se vad det var som utlöste aviseringen och om det finns något du kan göra för att stoppa ett pågående angrepp. Säkerhetsaviseringarna är indelade i grupper efter typ och datum. När du klickar på en säkerhetsavisering öppnas en sida med en lista över de gruppindelade aviseringarna.

![Åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

I det här fallet utlöstes aviseringarna på grund av misstänkt Remote Desktop Protocol-aktivitet. Den första kolumnen visar vilka resurser som attackerades. Den andra kolumnen visar hur många gånger resursen attackerades. Den tredje kolumnen visar tidpunkten för attacken. Den fjärde kolumnen visar varningens tillstånd. Och den femte kolumnen visar attackens allvarlighetsgrad. När du har läst den här informationen klickar du på den angripna resursen.

![Förslag på vad som kan göras för att åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

I fältet **Beskrivning** hittar du mer information om den här händelsen. Här kan du se vad det var som utlöste säkerhetsaviseringen, vilken resurs som är angripen, eventuell IP-adress som angreppet kommer ifrån och rekommendationer om hur du kan åtgärda problemet.  I vissa fall finns ingen IP-adress till källan eftersom IP-adresser inte ingår i alla säkerhetshändelseloggar i Windows.

Vilka åtgärder som föreslås av Security Center varierar beroende på typ av säkerhetsavisering. I vissa fall måste du kanske använda andra funktioner i Azure för att utföra de rekommenderade åtgärderna. Till exempel är reparationen av det här angreppet att inte tillåta IP-adressen som genererar detta angrepp genom att använda en [nätverks-ACL](../virtual-network/virtual-networks-acl.md) eller en regel för [nätverks säkerhets grupper](../virtual-network/security-overview.md#security-rules) . Mer information om de olika typerna av aviseringar finns i [säkerhets aviserings typer](security-center-alerts-overview.md#security-alert-types).

> [!NOTE]
> Security Center har för den begränsade förhandsversionen publicerat en ny uppsättning identifieringar som utnyttjar auditd-poster, ett gemensamt granskningsramverk för att upptäcka skadligt beteende på Linux-datorer. Skicka ett e-postmeddelande med dina prenumerations-id:n till [oss](mailto:ASC_linuxdetections@microsoft.com) för att ta del av förhandsversionen.


## <a name="see-also"></a>Se också
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
