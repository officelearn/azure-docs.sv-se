---
title: Referens för Azure Active Directory Identity Protection risk händelser | Microsoft Docs
description: Referens för Azure Active Directory Identity Protection risk händelser.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d2247aab872a71f250bd0b4b52714e402d2102d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905200"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Referens för Azure Active Directory Identity Protection risk händelser

De flesta säkerhets överträdelser sker när angripare får åtkomst till en miljö genom att stjäla en användares identitet. Det är ingen enkel uppgift att identifiera komprometterade identiteter. Azure Active Directory använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användar konton. Varje identifierad misstänkt åtgärd lagras i en post som kallas risk händelse.

## <a name="anonymous-ip-address"></a>Anonym IP-adress

**Identifierings typ:** Real tids  
**Gammalt namn:** Inloggningar från anonym IP-adress

Den här typen av risk händelse indikerar inloggningar från en anonym IP-adress (till exempel Tor webbläsare, Anonymizer VPN).
Dessa IP-adresser används vanligt vis av aktörer som vill dölja sin login telemetri (IP-adress, plats, enhet osv.) för potentiellt skadliga avsikter.

## <a name="atypical-travel"></a>Ovanlig resa

**Identifierings typ:** Offline  
**Gammalt namn:** Omöjligt att resa till ovanliga platser

Den här typen av risk händelser identifierar två inloggningar som härstammar från geografiskt avlägsna platser, där minst en av platserna också kan vara ovanlig för användaren, med hänsyn till tidigare beteende. Bland flera andra faktorer tar den här Machine Learning-algoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma klientautentiseringsuppgifter.

Algoritmen ignorerar uppenbara "falska positiva identifieringar" som bidrar till de omöjliga rese villkoren, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärnings period på tidigast 14 dagar eller 10 inloggningar, under vilken den lär sig en ny användares inloggnings beteende.

## <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

**Identifierings typ:** Offline  
**Gammalt namn:** Används med läckta autentiseringsuppgifter

Den här risk händelse typen indikerar att användarens giltiga autentiseringsuppgifter har läckts.
När cyberbrottslingar kompromettera giltiga lösen ord för legitima användare delar cyberbrottslingarna ofta dessa autentiseringsuppgifter. Detta görs vanligt vis genom att publicera dem offentligt på de mörka webb-eller Inklistrings platserna eller genom handel eller sälja de autentiseringsuppgifter som finns på den svarta marknaden. Tjänsten Microsoft läcker autentiseringsuppgifter hämtar användar namn/lösen ord genom att övervaka offentliga och mörka webbplatser och genom att arbeta med:

- Forskare
- Juridisk tillämpning
- Säkerhets team på Microsoft
- Andra betrodda källor

När tjänsten hämtar autentiseringsuppgifter från den mörka webben, klistrar in webbplatser eller ovanstående källor, kontrol leras de mot Azure AD-användares aktuella giltiga autentiseringsuppgifter för att hitta giltiga matchningar.

## <a name="malware-linked-ip-address"></a>IP-adress länkad till skadlig kod

**Identifierings typ:** Offline  
**Gammalt namn:** Inloggningar från angripna enheter

Den här typen av risk händelse indikerar inloggningar från IP-adresser som är infekterade med skadlig kod som är kända för att kommunicera med en bot-Server. Detta bestäms genom att IP-adresserna för användarens enhet korreleras mot IP-adresser som var i kontakt med en bot-server medan bot-servern var aktiv.

## <a name="unfamiliar-sign-in-properties"></a>Okända inloggnings egenskaper

**Identifierings typ:** Real tids  
**Gammalt namn:** Inloggningar från okända platser

Den här risk händelse typen tar hänsyn till tidigare inloggnings historik (IP, latitud/longitud och ASN) för att söka efter avvikande inloggningar. Systemet lagrar information om tidigare platser som används av en användare och betraktar dessa "välkända" platser. Risk händelsen utlöses när inloggningen sker från en plats som inte redan finns i listan över välkända platser. Nyligen skapade användare är i "inlärnings läge" under en tids period då okända inloggnings egenskaper risk händelser inaktive ras medan algoritmerna lär sig användarens beteende. Varaktigheten för utbildnings läge är dynamisk och beror på hur lång tid det tar för algoritmen att samla in tillräckligt med information om användarens inloggnings mönster. Den minsta varaktigheten är fem dagar. En användare kan gå tillbaka till inlärnings läget efter en lång tids inaktivitet. Systemet ignorerar också inloggningar från välbekanta enheter och platser som är geografiskt nära en bekant plats. 

Vi kör även den här identifieringen för grundläggande autentisering (eller äldre protokoll). Eftersom dessa protokoll inte har moderna egenskaper, t. ex. klient-ID, finns det begränsad telemetri för att minska antalet falska positiva identifieringar. Vi rekommenderar våra kunder att gå över till modern autentisering.

## <a name="azure-ad-threat-intelligence"></a>Azure AD hotinformation

**Identifierings typ:** Offline <br>
**Gammalt namn:** Den här identifieringen visas i de äldre Azure AD Identity Protections rapporterna (användare som har flaggats för risk, risk händelser) som "användare med läckta autentiseringsuppgifter"

Den här typen av risk händelse indikerar användar aktivitet som är ovanlig för den aktuella användaren eller som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot informations källor.

## <a name="admin-confirmed-user-compromised"></a>Administratörsbekräftad användare komprometterad

**Identifierings typ:** Offline <br>
Den här identifieringen anger att en administratör har valt "bekräfta användare som har komprometterats" i användar gränssnittet för riskfyllda användare eller med riskyUsers-API. Om du vill se vilken administratör som har bekräftat att den här användaren har fått problem, kontrollerar du användarens risk historik (via UI eller API).
