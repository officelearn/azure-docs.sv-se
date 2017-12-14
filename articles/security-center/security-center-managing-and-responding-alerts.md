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
ms.date: 11/30/2017
ms.author: yurid
ms.openlocfilehash: 1388a351b82beb6b3e7eb61a3a0517aa90c695f5
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
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
Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Nedan beskrivs hur du gör för att se mer information om de olika aviseringarna.

1. På instrumentpanelen i Security Center hittar du panelen **Säkerhetsaviseringar**.

    ![Panelen Säkerhetsaviseringar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Klicka på panelen för att öppna **Säkerhetsaviseringar** för att se mer information om aviseringarna.

   ![Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Längst ned på sidan visas information om de olika aviseringarna. Du kan sortera listan genom att klicka på den kolumn som du vill sortera efter. Här följer en förklaring av de olika kolumnerna:

* **Beskrivning**: en kort förklaring av aviseringen.
* **Count (Antal)**: antalet problem av just den här typen som upptäckts en specifik dag
* **Detected by (Upptäcktes genom)**: den tjänst som utlöste aviseringen
* **Date (Datum)**: det datum då händelsen inträffade
* **State (Status)**: aktuell status för den här aviseringen Det finns två tillstånd:
  * **Active (Aktiv)**: Säkerhetsproblemet är upptäckt.
* **Severity (Allvarlighetsgrad)**: kan vara hög, medelhög eller låg

> [!NOTE]
> Säkerhetsvarningar som genererats av Security Center visas också under Azure-aktivitetsloggen. Mer information om hur du kommer åt Azure-aktivitetsloggen finns i [View activity logs to audit actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) (Visa aktivitetsloggar för att granska åtgärder på resurser).
>

### <a name="filtering-alerts"></a>Filtrera varningar
Aviseringarna kan filtreras efter datum, status och allvarlighetsgrad. Att filtrera kan vara bra när du vill begränsa hur många aviseringar du vill se. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

1. Klicka på **Filter** i **Säkerhetsaviseringar**. **Filter** öppnas där du kan välja datum, status och vilka allvarlighetsgrader du vill se.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar
Om du klickar på en säkerhetsavisering får du se vad det var som utlöste aviseringen och om det finns något du kan göra för att stoppa ett pågående angrepp. Säkerhetsaviseringarna är indelade i grupper efter typ och datum. När du klickar på en säkerhetsavisering öppnas en sida med en lista över de gruppindelade aviseringarna.

![Åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

I det här fallet utlöstes aviseringarna på grund av misstänkt Remote Desktop Protocol-aktivitet. Den första kolumnen visar vilka resurser som attackerades. Den andra kolumnen visar hur många gånger resursen attackerades. Den tredje kolumnen visar tidpunkten för attacken. Den fjärde kolumnen visar varningens tillstånd. Och den femte kolumnen visar attackens allvarlighetsgrad. När du har läst den här informationen klickar du på den angripna resursen.

![Förslag på vad som kan göras för att åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

I fältet **Beskrivning** finns mer information om den här händelsen. Här kan du se vad det var som utlöste säkerhetsaviseringen, vilken resurs som är angripen, eventuell IP-adress som angreppet kommer ifrån och rekommendationer om hur du kan åtgärda problemet.  I vissa fall finns ingen IP-adress till källan eftersom IP-adresser inte ingår i alla säkerhetshändelseloggar i Windows.

Vilka åtgärder som föreslås av Security Center varierar beroende på typ av säkerhetsavisering. I vissa fall måste du kanske använda andra funktioner i Azure för att utföra de rekommenderade åtgärderna. Den rekommenderade åtgärden för det här angreppet är till exempel att svartlista IP-adressen som angreppet kommer ifrån genom regler för [nätverks-ACL](../virtual-network/virtual-networks-acl.md) eller en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md). Mer information om olika typer av aviseringar finns i [Typ med säkerhetsaviseringar i Azure Security Center](security-center-alerts-type.md).

> [!NOTE]
> Security Center har för den begränsade förhandsversionen publicerat en ny uppsättning identifieringar som utnyttjar auditd-poster, ett gemensamt granskningsramverk för att upptäcka skadligt beteende på Linux-datorer. Skicka ett e-postmeddelande med dina prenumerations-id:n till [oss](mailto:ASC_linuxdetections@microsoft.com) för att ta del av förhandsversionen.


## <a name="see-also"></a>Se även
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
