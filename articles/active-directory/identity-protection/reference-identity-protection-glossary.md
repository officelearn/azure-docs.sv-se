---
title: Ordlista för identitetsskydd i Azure Active Directory
description: Ordlista för identitetsskydd i Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232350"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Ordlista för identitetsskydd i Azure Active Directory

### <a name="at-risk-user"></a>I riskzonen (Användare)
En användare med en eller flera aktiva riskidentifieringar. 

### <a name="atypical-sign-in-location"></a>Atypisk inloggningsplats
En inloggning från en geografisk plats som inte är typisk för den specifika användaren, liknande användare eller klienten.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
En säkerhetsmodul i Azure Active Directory som ger en konsoliderad vy över riskidentifieringar och potentiella sårbarheter som påverkar en organisations identiteter.

### <a name="conditional-access"></a>Villkorlig åtkomst
En princip för att skydda åtkomsten till resurser. Regler för villkorlig åtkomst lagras i Azure Active Directory och utvärderas av Azure AD innan du beviljar åtkomst till resursen.  Exempel på regler är att begränsa åtkomsten baserat på användarens plats, enhetens hälsotillstånd eller användarens autentiseringsmetod.

### <a name="credentials"></a>Autentiseringsuppgifter
Information som innehåller identifiering och identifieringsbevis som används för att få tillgång till lokala resurser och nätverksresurser. Exempel på autentiseringsuppgifter är användarnamn och lösenord, smartkort och certifikat.

### <a name="event"></a>Händelse
En post för en aktivitet i Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Falskt positivt (riskdetektering)
En status för riskidentifiering som ställts in manuellt av en identity protection-användare, vilket indikerar att riskidentifieringen undersöktes och har flaggats felaktigt som en riskidentifiering.

### <a name="identity"></a>Identitet
En person eller enhet som måste verifieras med hjälp av autentisering, baserat på kriterier som lösenord eller ett certifikat.

### <a name="identity-risk-detection"></a>Identifiering av identitetsrisker
Azure AD-händelse som har flaggats som avvikande av Identity Protection och kan tyda på att en identitet har komprometterats.

### <a name="ignored-risk-detection"></a>Ignoreras (riskidentifiering)
En status för riskidentifiering som ställts in manuellt av en identity protection-användare, vilket indikerar att riskidentifieringen är stängd utan att vidta en åtgärd.

### <a name="impossible-travel-from-atypical-locations"></a>Omöjlig resa från atypiska platser
En riskidentifiering som utlöses när två inloggningar för samma användare upptäcks, där minst en av dem kommer från en atypisk inloggningsplats och där tiden mellan inloggningarna är kortare än den minsta tid det skulle ta att fysiskt resa mellan dessa Platser.  

### <a name="investigation"></a>Undersökning
Processen att granska aktiviteter, loggar och annan relevant information om en riskidentifiering för att avgöra om reparations- eller begränsningsåtgärder är nödvändiga, förstå om och hur identiteten komprometterades och förstå hur den komprometterade identitet användes.

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter
En riskidentifiering utlöses när aktuella användaruppgifter (användarnamn och lösenord) hittas publiceras offentligt i Dark webben av våra forskare.

### <a name="mitigation"></a>Åtgärd
En åtgärd för att begränsa eller eliminera en angripares förmåga att utnyttja en komprometterad identitet eller enhet utan att återställa identiteten eller enheten till ett säkert tillstånd. En begränsning löser inte tidigare riskidentifieringar som är associerade med identiteten eller enheten.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
En autentiseringsmetod som kräver två eller flera autentiseringsmetoder, som kan innehålla något som användaren har, ett sådant certifikat. något som användaren känner till, till exempel användarnamn, lösenord eller lösenfraser. fysiska attribut, till exempel ett tumavtryck. och personliga attribut, till exempel en personlig signatur.

### <a name="offline-detection"></a>Identifiering offline
Identifiering av avvikelser och utvärdering av risken för en händelse, till exempel inloggningsförsök i efterhand, för en händelse som redan har inträffat.

### <a name="policy-condition"></a>Principvillkor
En del av en säkerhetsprincip som definierar de entiteter (grupper, användare, appar, enhetsplattformar, enhetstillstånd, IP-intervall, klienttyper) som ingår i principen eller som inte ingår i den.

### <a name="policy-rule"></a>Principregel
Den del av en säkerhetsprincip som beskriver de omständigheter som skulle utlösa principen och de åtgärder som vidtas när principen utlöses.

### <a name="prevention"></a>Prevention (Skydd)
En åtgärd för att förhindra skador på organisationen genom missbruk av en identitet eller enhet som misstänks eller vet att äventyras. En förebyggande åtgärd skyddar inte enheten eller identiteten och löser inte tidigare riskidentifieringar.

### <a name="privileged-user"></a>Privilegierad (användare)
En användare som vid tidpunkten för en riskidentifiering hade permanenta eller tillfälliga administratörsbehörigheter till en eller flera resurser i Azure Active Directory, till exempel en global administratör, faktureringsadministratör, tjänstadministratör, användaradministratör och lösenord Administratör. 

### <a name="real-time"></a>Realtid
Se Identifiering i realtid.

### <a name="real-time-detection"></a>Identifiering i realtid
Identifiering av avvikelser och utvärdering av risken för en händelse, till exempel inloggningsförsök innan händelsen tillåts fortsätta.

### <a name="remediated-risk-detection"></a>Åtgärdad (riskdetektering)
En status för riskidentifiering som automatiskt anges av Identity Protection, vilket indikerar att riskidentifieringen åtgärdades med hjälp av standardåtgärden för att upptäcka risker för den här typen av riskidentifiering. När användarlösenordet till exempel återställs åtgärdas många riskidentifieringar som indikerar att det tidigare lösenordet komprometterades automatiskt.

### <a name="remediation"></a>Åtgärder
En åtgärd för att skydda en identitet eller en enhet som tidigare misstänktes eller var känd för att vara komprometterade. En reparationsåtgärd återställer identiteten eller enheten till ett säkert tillstånd och löser tidigare riskidentifieringar som är associerade med identiteten eller enheten.

### <a name="resolved-risk-detection"></a>Löst (riskidentifiering)
En status för riskidentifiering som ställts in manuellt av en identitetsskyddsanvändare, vilket anger att användaren vidtog en lämplig åtgärd utanför Identitetsskydd och att riskidentifieringen bör betraktas som stängd.

### <a name="risk-detection-status"></a>Status för riskidentifiering
En egenskap för en riskidentifiering som anger om händelsen är aktiv och om den är stängd, orsaken till att den stängs.

### <a name="risk-detection-type"></a>Typ av riskidentifiering
En kategori för riskidentifiering, som anger vilken typ av avvikelse som orsakade händelsen som ska betraktas som riskabel.

### <a name="risk-level-risk-detection"></a>Risknivå (riskdetektering)
En indikation (Hög, Medel eller Låg) av allvarlighetsgraden för riskidentifiering för att hjälpa Identity Protection-användare att prioritera de åtgärder de vidtar för att minska risken för deras organisation. 

### <a name="risk-level-sign-in"></a>Risknivå (inloggning)
En indikation (Hög, Medel eller Låg) om sannolikheten för att någon annan försöker använda användarens identitet för en viss inloggning.

### <a name="risk-level-user-compromise"></a>Risknivå (kompromant för användare)
En indikation (hög, medel eller låg) på sannolikheten för att en identitet har komprometterats.

### <a name="risk-level-vulnerability"></a>Risknivå (sårbarhet)
En indikation (Hög, Medel eller Låg) av hur allvarlig säkerhetsproblemet är för att hjälpa identity protection-användare att prioritera de åtgärder de vidtar för att minska risken för deras organisation.

### <a name="secure-identity"></a>Säker (identitet)
Vidta åtgärder för reparation, till exempel en lösenordsändring eller ombildning av datorn för att återställa en potentiellt komprometterad identitet till ett kompromisslöst tillstånd.

### <a name="security-policy"></a>Säkerhetsprincip
En samling policyregler och villkor. En princip kan tillämpas på entiteter som användare, grupper, appar, enheter, enhetsplattformar, enhetstillstånd, IP-intervall och Auth2.0-klienttyper. När en princip är aktiverad utvärderas den när en entitet som ingår i principen utfärdas en token för en resurs.

### <a name="sign-in-v"></a>Logga in (v)
Så här autentiserar du till en identitet i Azure Active Directory.

### <a name="sign-in-n"></a>Logga in (n)
Processen eller åtgärden att autentisera en identitet i Azure Active Directory och händelsen som fångar den här åtgärden.

### <a name="sign-in-from-anonymous-ip-address"></a>Logga in från anonym IP-adress
En riskidentifiering utlöses efter en lyckad inloggning från IP-adress som har identifierats som en anonym proxy-IP-adress.

### <a name="sign-in-from-infected-device"></a>Logga in från infekterad enhet
En riskidentifiering som utlöses när en inloggning kommer från en IP-adress, som är känd för att användas av en eller flera komprometterade enheter, som aktivt försöker kommunicera med en bot-server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Logga in från IP-adress med misstänkt aktivitet
En riskidentifiering som utlöses efter en lyckad inloggning från en IP-adress med ett stort antal misslyckade inloggningsförsök på flera användarkonton under en kort tidsperiod.

### <a name="sign-in-from-unfamiliar-location"></a>Logga in från okänd plats
En riskidentifiering utlöses när en användare loggar in från en ny plats (IP, Latitude/Longitud och ASN).

### <a name="sign-in-risk"></a>Inanmälningsrisk
Se Risknivå (inloggning)

### <a name="sign-in-risk-policy"></a>Riskpolicy för inloggning
En princip för villkorlig åtkomst som utvärderar risken för en viss inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="user-compromise-risk"></a>Risk för kompromettering av användare
Se Risknivå (kompromettering av användare)

### <a name="user-risk"></a>Användarrisk
Se Risknivå (användarkompromiss).

### <a name="user-risk-policy"></a>Policy för användarrisk
En princip för villkorlig åtkomst som tar hänsyn till inloggningen och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="users-flagged-for-risk"></a>Användare som har flaggats för risk
Användare som har riskidentifieringar, som antingen är aktiva eller åtgärdade

### <a name="vulnerability"></a>Säkerhetsrisk
En konfiguration eller ett villkor i Azure Active Directory, vilket gör katalogen känslig för kryphål eller hot.

## <a name="see-also"></a>Se även

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
