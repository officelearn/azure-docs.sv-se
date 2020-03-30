---
title: Vad är risk? Azure AD Identity Protection
description: Förklara risker i Azure AD Identity Protection
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
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253694"
---
# <a name="what-is-risk"></a>Vad är risk?

Riskidentifieringar i Azure AD Identity Protection inkluderar alla identifierade misstänkta åtgärder relaterade till användarkonton i katalogen.

Identity Protection ger organisationer åtkomst till kraftfulla resurser för att snabbt se och svara på dessa misstänkta åtgärder. 

![Säkerhetsöversikt som visar riskfyllda användare och inloggningar](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Risktyper och upptäckt

Det finns två typer av **riskanvändare** och **inloggning** och två typer av identifiering eller beräkning **i realtid** och **offline**.

### <a name="user-risk"></a>Användarrisk

En användarrisk representerar sannolikheten för att en viss identitet eller ett visst konto äventyras. 

Dessa risker beräknas offline med hjälp av Microsofts interna och externa hotinformationskällor, inklusive säkerhetsforskare, brottsbekämpande personal, säkerhetsteam på Microsoft och andra betrodda källor.

| Riskdetektering | Beskrivning |
| --- | --- |
| Läckta autentiseringsuppgifter | Den här riskidentifieringstypen anger att användarens giltiga autentiseringsuppgifter har läckt ut. När cyberbrottslingar äventyrar giltiga lösenord för legitima användare delar de ofta dessa autentiseringsuppgifter. Denna delning görs vanligtvis genom att publicera offentligt på den mörka webben, klistra in webbplatser, eller genom handel och försäljning av referenser på den svarta marknaden. När tjänsten Microsoft leaked credentials hämtar användaruppgifter från den mörka webben, klistrar in webbplatser eller andra källor kontrolleras de mot Azure AD-användares aktuella giltiga autentiseringsuppgifter för att hitta giltiga matchningar. |
| Azure AD-hotinformation | Den här riskidentifieringstypen anger användaraktivitet som är ovanlig för den givna användaren eller överensstämmer med kända attackmönster baserat på Microsofts interna och externa hotinformationskällor. |

### <a name="sign-in-risk"></a>Inanmälningsrisk

En inloggningsrisk representerar sannolikheten för att en viss autentiseringsbegäran inte godkänns av identitetsägaren. 

Dessa risker kan beräknas i realtid eller beräknas offline med hjälp av Microsofts interna och externa hotinformationskällor, inklusive säkerhetsforskare, brottsbekämpande personal, säkerhetsteam på Microsoft och andra tillförlitliga källor.

| Riskdetektering | Typ av identifiering | Beskrivning |
| --- | --- | --- |
| Anonym IP-adress | Realtid | Den här riskidentifieringstypen anger inloggningar från en anonym IP-adress (till exempel Tor-webbläsare eller anonym VPN). Dessa IP-adresser används vanligtvis av aktörer som vill dölja sin inloggningstelemetri (IP-adress, plats, enhet, etc.) för potentiellt skadliga avsikter. |
| Atypiska resor | Offline | Den här riskidentifieringstypen identifierar två inloggningar som kommer från geografiskt avlägsna platser, där minst en av platserna också kan vara atypisk för användaren, med tanke på tidigare beteende. Bland flera andra faktorer tar den här maskininlärningsalgoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma Autentiseringsuppgifter. <br><br> Algoritmen ignorerar uppenbara "falska positiva" som bidrar till de omöjliga resevillkoren, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärningsperiod på tidigaste 14 dagar eller 10 inloggningar, under vilken det lär sig en ny användares inloggningsbeteende. |
| Skadlig kod länkad IP-adress | Offline | Den här riskidentifieringstypen anger inloggningar från IP-adresser infekterade med skadlig kod som är kända för att aktivt kommunicera med en bot-server. Den här identifieringen bestäms genom att IP-adresser för användarens enhet korrelerades mot IP-adresser som var i kontakt med en bot-server medan bot-servern var aktiv. |
| Okända inloggningsegenskaper | Realtid | Den här riskidentifieringstypen anser att tidigare inloggningshistorik (IP, Latitude/ Longitud och ASN) letar efter avvikande inloggningar. Systemet lagrar information om tidigare platser som används av en användare och tar hänsyn till dessa "välbekanta" platser. Riskidentifieringen utlöses när inloggningen sker från en plats som inte redan finns i listan över välbekanta platser. Nyligen skapade användare kommer att vara i "inlärningsläge" under en tid då okända inloggningsegenskaper riskidentifieringar kommer att stängas av medan våra algoritmer lära sig användarens beteende. Inlärningslägets varaktighet är dynamisk och beror på hur lång tid det tar för algoritmen att samla in tillräckligt med information om användarens inloggningsmönster. Minimitiden är fem dagar. En användare kan gå tillbaka till inlärningsläge efter en lång period av inaktivitet. Systemet ignorerar också inloggningar från välbekanta enheter och platser som ligger geografiskt nära en välbekant plats. <br><br> Vi kör även den här identifieringen för grundläggande autentisering (eller äldre protokoll). Eftersom dessa protokoll inte har moderna egenskaper som klient-ID, finns det begränsad telemetri för att minska falska positiva identifieringar. Vi rekommenderar våra kunder att gå över till modern autentisering. |
| Administratören bekräftade användaren komprometterad | Offline | Den här identifieringen indikerar att en administratör har valt "Bekräfta att användaren komprometterats" i användargränssnittet för riskfyllda användare eller med hjälp av riskyUsers API. Om du vill se vilken administratör som har bekräftat att användaren har komprometterats kontrollerar du användarens riskhistorik (via användargränssnittet eller API:et). |
| Skadlig IP-adress | Offline | Den här identifieringen indikerar inloggning från en skadlig IP-adress. En IP-adress anses vara skadlig baserat på höga felfrekvenser på grund av ogiltiga autentiseringsuppgifter som tas emot från IP-adressen eller andra IP-rykteskällor. |
| Regler för misstänkt manipulation av inkorg | Offline | Den här identifieringen identifieras av [MICROSOFT Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Den här identifieringen profilerar din miljö och utlöser aviseringar när misstänkta regler som tar bort eller flyttar meddelanden eller mappar anges i en användares inkorg. Detta kan tyda på att användarens konto äventyras, att meddelanden avsiktligt döljs och att postlådan används för att distribuera skräppost eller skadlig kod i organisationen. |
| Omöjlig resa | Offline | Den här identifieringen identifieras av [MICROSOFT Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Den här identifieringen identifierar två användaraktiviteter (är en eller flera sessioner) som kommer från geografiskt avlägsna platser inom en tidsperiod som är kortare än den tid det skulle ha tagit användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma autentiseringsuppgifter. |

### <a name="other-risk-detections"></a>Andra riskdetekteringar

| Riskdetektering | Typ av identifiering | Beskrivning |
| --- | --- | --- |
| Ytterligare risk upptäckt | Realtid eller offline | Den här identifieringen indikerar att en av ovanstående premiumidentifieringar upptäcktes. Eftersom premiumidentifieringarna endast är synliga för Azure AD Premium P2-kunder har de titeln "ytterligare riskidentifierat" för kunder utan Azure AD Premium P2-licenser. |

## <a name="next-steps"></a>Nästa steg

- [Policyer tillgängliga för att minska riskerna](concept-identity-protection-policies.md)

- [Säkerhetsöversikt](concept-identity-protection-security-overview.md)
