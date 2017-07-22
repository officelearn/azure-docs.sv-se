---
title: "Hantera säkerhetsaviseringar i Azure Security Center | Microsoft Docs"
description: "I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: bf3aa51b4b1224840ae6897ff7df86cf3d2b3477
ms.contentlocale: sv-se
ms.lasthandoff: 04/18/2017

---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Hantera och åtgärda säkerhetsaviseringar i Azure Security Center
Det här dokumentet beskriver hur du använder Azure Security Center för att hantera och svara på säkerhetsvarningar.

> [!NOTE]
> För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. En kostnadsfri 60-dagars utvärderingsversion är tillgänglig. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](security-center-policies.md). Mer information finns under [priser för Azure Security Center](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.


> [!NOTE]
> Mer information om hur identifieringsfunktionerna i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Hantera säkerhetsaviseringar
Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Öppna Azure Portal och följ stegen nedan om du vill ha mer information om varje typ av varning:

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.

    ![Panelen Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Om du klickar på rutan öppnas bladet **Security alerts (Säkerhetsaviseringar)** med mer information om aviseringarna så som visas på bilden nedan.

   ![Bladet Säkerhetsvarningar i Säkerhetscenter](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Längst ned i bladet visas information om de olika aviseringarna. Du kan sortera listan genom att klicka på den kolumn som du vill sortera efter. Här följer en förklaring av de olika kolumnerna:

* **Beskrivning**: en kort förklaring av aviseringen.
* **Count (Antal)**: antalet problem av just den här typen som upptäckts en specifik dag
* **Detected by (Upptäcktes genom)**: den tjänst som utlöste aviseringen
* **Date (Datum)**: det datum då händelsen inträffade
* **State (Status)**: aktuell status för den här aviseringen Det finns två tillstånd:
  * **Active (Aktiv)**: Säkerhetsproblemet är upptäckt.
* **Severity (Allvarlighetsgrad)**: kan vara hög, medelhög eller låg

### <a name="filtering-alerts"></a>Filtrera varningar
Aviseringarna kan filtreras efter datum, status och allvarlighetsgrad. Att filtrera kan vara bra när du vill begränsa hur många aviseringar du vill se. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

1. Klicka på **Filter** på bladet **Security Alerts (Säkerhetsaviseringar)**. Bladet **Filter** öppnas där du kan välja datum, status och vilka allvarlighetsgrader du vill se.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar
Om du klickar på en säkerhetsavisering får du se vad det var som utlöste aviseringen och om det finns något du kan göra för att stoppa ett pågående angrepp. Säkerhetsaviseringarna är indelade i grupper efter typ och datum. När du klickar på en säkerhetsavisering öppnas ett blad med en lista över de gruppindelade aviseringarna.

![Åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

I det här fallet utlöstes aviseringarna på grund av misstänkt Remote Desktop Protocol-aktivitet. Den första kolumnen visar vilka resurser som attackerades. Den andra kolumnen visar hur många gånger resursen attackerades. Den tredje kolumnen visar tidpunkten för attacken. Den fjärde kolumnen visar varningens tillstånd. Och den femte kolumnen visar attackens allvarlighetsgrad. När du har granskat den här informationen klickar du på den resurs som angreps så öppnas ett nytt blad.

![Förslag på vad som kan göras för att åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

I fältet **Beskrivning** på det här bladet finns mer information om händelsen. Här kan du se vad det var som utlöste säkerhetsaviseringen, vilken resurs som är angripen, eventuell IP-adress som angreppet kommer ifrån och rekommendationer om hur du kan åtgärda problemet.  I vissa fall finns ingen IP-adress till källan eftersom IP-adresser inte ingår i alla säkerhetshändelseloggar i Windows.

Vilka åtgärder som rekommenderas varierar beroende på typ av säkerhetsavisering. I vissa fall måste du kanske använda andra funktioner i Azure för att utföra de rekommenderade åtgärderna. Den rekommenderade åtgärden för det här angreppet är till exempel att svartlista IP-adressen som angreppet kommer ifrån genom regler för [nätverks-ACL](../virtual-network/virtual-networks-acl.md) eller en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> Mer information om olika typer av aviseringar finns i [Typ med säkerhetsaviseringar i Azure Security Center](security-center-alerts-type.md).
>
>

## <a name="see-also"></a>Se även
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

