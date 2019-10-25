---
title: Vad är risk? Azure AD Identity Protection
description: Att förklara risker i Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd0b562ae0570917c9127e301964d089d49b087
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887031"
---
# <a name="what-is-risk"></a>Vad är risk?

Risk identifieringar i Azure AD Identity Protection innehåller identifierade misstänkta åtgärder relaterade till användar konton i katalogen.

Identitets skydd ger organisationer till gång till kraftfulla resurser för att kunna se och svara snabbt på dessa misstänkta åtgärder. 

![Säkerhets översikt som visar riskfyllda användare och inloggningar](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Risk typer och identifiering

Det finns två typer av risk **användare** och **inloggning** och två typer av identifiering eller beräkning i **real tid** och **offline**.

### <a name="user-risk"></a>Användar risk

En användar risk representerar sannolikheten att en specifik identitet eller ett konto har komprometterats. 

Dessa risker beräknas offline med Microsofts interna och externa hot informations källor, inklusive säkerhets forskare, lag verk ställande personal, säkerhets team på Microsoft och andra betrodda källor.

| Identifiering av risker | Beskrivning |
| --- | --- |
| Läckta autentiseringsuppgifter | Den här typen av risk identifiering anger att användarens giltiga autentiseringsuppgifter har läckts. När cyberbrottslingar kompromettera giltiga lösen ord för legitima användare delar de ofta dessa autentiseringsuppgifter. Den här delningen görs vanligt vis genom att publicera offentligt på den mörka webbplatsen, klistra in webbplatser eller genom handel och sälja autentiseringsuppgifterna på den svarta marknaden. När tjänsten Microsoft läcker autentiseringsuppgifter hämtar användarautentiseringsuppgifter från den mörka webben, klistra in webbplatser eller andra källor, kontrol leras de mot Azure AD-användares aktuella giltiga autentiseringsuppgifter för att hitta giltiga matchningar. |
| Azure AD Threat Intelligence | Den här typen av risk identifiering indikerar användar aktivitet som är ovanlig för den aktuella användaren eller som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot informations källor. |

### <a name="sign-in-risk"></a>Inloggnings risk

En inloggnings risk representerar sannolikheten att en begäran om autentisering inte har behörighet av identitets ägaren. 

Dessa risker kan beräknas i real tid eller beräknas offline med hjälp av Microsofts interna och externa hot informations källor, till exempel säkerhets forskare, juridiska tekniker, säkerhets team på Microsoft och andra betrodda källor.

| Identifiering av risker | Identifierings typ | Beskrivning |
| --- | --- | --- |
| Anonym IP-adress | Real tids | Den här typen av risk identifiering indikerar inloggningar från en anonym IP-adress (till exempel Tor webbläsare eller anonym VPN). Dessa IP-adresser används vanligt vis av aktörer som vill dölja sin login telemetri (IP-adress, plats, enhet osv.) för potentiellt skadliga avsikter. |
| Ovanlig-resa | Anslutningen | Den här typen av risk identifiering identifierar två inloggningar som härstammar från geografiskt avlägsna platser, där minst en av platserna också kan vara ovanlig för användaren, med hänsyn till tidigare beteende. Bland flera andra faktorer tar den här Machine Learning-algoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma klientautentiseringsuppgifter. <br><br> Algoritmen ignorerar uppenbara "falska positiva identifieringar" som bidrar till de omöjliga rese villkoren, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärnings period på tidigast 14 dagar eller 10 inloggningar, under vilken den lär sig en ny användares inloggnings beteende. |
| Länkad IP-adress för skadlig kod | Anslutningen | Den här typen av risk identifiering indikerar inloggningar från IP-adresser som har infekterats med skadlig kod som är känt för att aktivt kommunicera med en bot-Server. Den här identifieringen bestäms genom att IP-adresserna för användarens enhet korreleras mot IP-adresser som var i kontakt med en bot-server medan bot-servern var aktiv. |
| Okända inloggnings egenskaper | Real tids | Den här typen av risk identifiering förväntar sig tidigare inloggnings historik (IP, latitud/longitud och ASN) för att söka efter avvikande inloggningar. Systemet lagrar information om tidigare platser som används av en användare och betraktar dessa "välkända" platser. Identifieringen av risker utlöses när inloggningen sker från en plats som inte redan finns i listan över välbekanta platser. Nyskapade användare är i "inlärnings läge" under en tids period då okända inloggnings egenskaper risk identifieringar kommer att inaktive ras medan algoritmerna lär sig användarens beteende. Varaktigheten för utbildnings läge är dynamisk och beror på hur lång tid det tar för algoritmen att samla in tillräckligt med information om användarens inloggnings mönster. Den minsta varaktigheten är fem dagar. En användare kan gå tillbaka till inlärnings läget efter en lång tids inaktivitet. Systemet ignorerar också inloggningar från välbekanta enheter och platser som är geografiskt nära en bekant plats. <br><br> Vi kör även den här identifieringen för grundläggande autentisering (eller äldre protokoll). Eftersom dessa protokoll inte har moderna egenskaper, t. ex. klient-ID, finns det begränsad telemetri för att minska antalet falska positiva identifieringar. Vi rekommenderar våra kunder att gå över till modern autentisering. |
| Administratören bekräftade att användaren har komprometterats | Anslutningen | Den här identifieringen anger att en administratör har valt "bekräfta användare som har komprometterats" i användar gränssnittet för riskfyllda användare eller med riskyUsers-API. Om du vill se vilken administratör som har bekräftat att den här användaren har fått problem, kontrollerar du användarens risk historik (via UI eller API). |
| Skadlig IP-adress | Anslutningen | Den här identifieringen indikerar inloggning från en skadlig IP-adress. En IP-adress betraktas som skadlig baserat på hög fel frekvens på grund av ogiltiga autentiseringsuppgifter som tagits emot från IP-adressen eller andra IP-ryktes källor. |

### <a name="other-risk-detections"></a>Andra risk identifieringar

| Identifiering av risker | Identifierings typ | Beskrivning |
| --- | --- | --- |
| Ytterligare risk upptäckt | I real tid eller offline | Den här identifieringen anger att ett av ovanstående Premium-identifieringar upptäcktes. Eftersom Premium-identifieringar endast är synliga för Azure AD Premium P2-kunder kallas de "ytterligare risk upptäckt" för kunder utan Azure AD Premium P2-licenser. |

## <a name="next-steps"></a>Nästa steg

- [Principer som är tillgängliga för att minimera risker](concept-identity-protection-policies.md)

- [Säkerhetsöversikt](concept-identity-protection-security-overview.md)
