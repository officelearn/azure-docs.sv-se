---
title: Azure Active Directory Identity Protection – ordlista | Microsoft Docs
description: Azure Active Directory Identity Protection Glossary
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip, ordlista
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5def46b6c12dc08c273ea7334516b63b7606477f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521949"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection Glossary
### <a name="at-risk-user"></a>I fara (användare)
En användare med en eller flera aktiva riskhändelser. 

### <a name="atypical-sign-in-location"></a>Ovanlig inloggning plats
En inloggning från en geografisk plats som inte är typiska för den specifika användaren, liknande användare eller klienten.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
En modul för maskinvarusäkerhet av Azure Active Directory som ger en samlad vy över riskhändelser och potentiella problem som påverkar organisationens identiteter.

### <a name="conditional-access"></a>Villkorlig åtkomst
En princip för att skydda åtkomsten till resurser. Regler för villkorlig åtkomst är lagrade i Azure Active Directory och utvärderas av Azure AD innan du beviljar åtkomst till resursen.  Exempel på regler är att begränsa åtkomst baserat på användarens plats eller enhetens hälsotillstånd metoden för användarautentisering.

### <a name="credentials"></a>Autentiseringsuppgifter
Information som innefattar identifiering och bekräftelse för identifiering som används för att få åtkomst till lokala resurser och nätverksresurser. Exempel på autentiseringsuppgifter är användarnamn och lösenord, smartkort och certifikat.

### <a name="event"></a>Händelse
En post för en aktivitet i Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falska positiva (riskhändelse)
En händelse Riskstatus ange manuellt av en användare med Identity Protection, som anger att riskhändelsen har undersökt och felaktigt som flaggats som en riskhändelse.

### <a name="identity"></a>Identitet
En person eller enhet som måste verifieras genom autentisering, baserat på kriterier som lösenord eller ett certifikat.

### <a name="identity-risk-event"></a>Identitet riskhändelse
AAD-händelse som har flaggats som avvikande av Identity Protection och kan tyda på att en identitet har komprometterats.

### <a name="ignored-risk-event"></a>Ignoreras (riskhändelse)
En händelse Riskstatus ange manuellt av en användare med Identity Protection, som anger att riskhändelsen har stängts utan att ta en Reparationsåtgärd.

### <a name="impossible-travel-from-atypical-locations"></a>Omöjlig resa från ovanliga platser
En riskhändelse som utlöses när två inloggningar för samma användare identifieras där minst en av dem är från en ovanlig inloggning plats och tiden mellan inloggningar är kortare än den minsta tiden det tar att fysiskt reser mellan dessa platser.  

### <a name="investigation"></a>Undersökning
Granska aktiviteter, loggar och annan relevant information som rör en riskhändelse att bestämma om reparation eller minimering steg är nödvändiga, förstå om och hur identitet har komprometterats och förstå hur komprometteras identitet användes.

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter
En riskhändelse som utlöses när den aktuella användarens autentiseringsuppgifter (användarnamn och lösenord) hittas offentligt publicerats i mörka Internet av oss att snabbare.

### <a name="mitigation"></a>Åtgärd
En åtgärd för att begränsa eller eliminera möjligheten för en angripare att utnyttja en komprometterad identitet eller en enhet utan att återställa den identitet eller enhet till säkert läge. En lösning kan inte matchas tidigare riskhändelser som är associerade med den identitet eller enhet.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
En autentiseringsmetod som kräver minst två autentiseringsmetoder, som kan innehålla något användaren har certifikatet. något användaren vet, till exempel användarnamn, lösenord eller pass fraser. fysiska attribut, till exempel ett tumavtryck; och personliga attribut, till exempel en personlig signatur.

### <a name="offline-detection"></a>Offline-identifiering
Identifiering av avvikelser och bedömning av risken för en händelse, till exempel inloggningsförsök i efterhand, för en händelse som redan har hänt.

### <a name="policy-condition"></a>Villkor
En del av en säkerhetsprincip som definierar de entiteter (grupper, användare, appar, enhetsplattformar, enhetstillstånd, IP-adressintervall, klienttyper) ingår i principen eller uteslutas från den.

### <a name="policy-rule"></a>Principregel
Del av en säkerhetsprincip som beskriver de omständigheter som ska utlösa principen och de åtgärder som vidtas när principen har utlösts.

### <a name="prevention"></a>Prevention (Skydd)
En åtgärd för att förhindra i organisationen via missbruk av en identitet eller enhet misstänks eller vet äventyras. En åtgärd som skydd skyddar inte enheten eller identiteten och matchar inte tidigare riskhändelser.

### <a name="privileged-user"></a>Privilegierad (användare)
En användare som vid tidpunkten för en riskhändelse hade permanenta eller tillfälliga administratörsbehörighet till en eller flera resurser i Azure Active Directory, exempelvis en Global administratör faktureringsadministratör, tjänstadministratör, Användaradministratör och Lösenordsadministratör. 

### <a name="real-time"></a>Realtid
Se realtidsidentifiering.

### <a name="real-time-detection"></a>Realtidsidentifiering
Identifiering av avvikelser och bedömning av risken för en händelse, till exempel inloggningsförsök innan händelsen kan fortsätta.

### <a name="remediated-risk-event"></a>Åtgärdad (riskhändelse)
En händelse Riskstatus in automatiskt av Identity Protection, som anger att riskhändelsen har åtgärdats med Reparationsåtgärd som standard för den här typen av riskhändelse. När användarens lösenord återställs åtgärdas exempelvis automatiskt många riskhändelser som indikerar att det tidigare lösenordet har komprometterats.

### <a name="remediation"></a>Åtgärd
En åtgärd för att skydda en identitet eller en enhet som har tidigare eller misstänks äventyras. En Reparationsåtgärd återställer identitet eller enhet till säkert läge och löser tidigare riskhändelser som är associerade med den identitet eller enhet.

### <a name="resolved-risk-event"></a>Löst (riskhändelse)
En händelse Riskstatus anges manuellt av en användare som Identity Protection, som anger att användaren har gjort en lämpliga avhjälpande åtgärd utanför identitetsskydd och riskhändelsen bör övervägas stängd.

### <a name="risk-event-status"></a>Händelsen Riskstatus
En egenskap för en riskhändelse, som anger om händelsen är aktiv, och stängts, orsak för att stänga den.

### <a name="risk-event-type"></a>Typ av riskhändelse
En kategori för riskhändelsen som anger vilken typ av avvikelser som orsakade händelsen för att anses vara riskfyllda.

### <a name="risk-level-risk-event"></a>Risknivå (riskhändelse)
En indikation (hög, medel eller låg) på allvarlighetsgraden för riskhändelsen att hjälpa användare att Identity Protection prioritera åtgärderna som de vidtar för att minska risken för organisationen. 

### <a name="risk-level-sign-in"></a>Risknivå (logga in)
En indikation (hög, medel eller låg) på sannolikheten att för en specifik inloggning, någon annan försöker använda användarens identitet.

### <a name="risk-level-user-compromise"></a>Risknivå (användaren kompromettering)
En indikation (hög, medel eller låg) på sannolikheten att en identitet har komprometterats.

### <a name="risk-level-vulnerability"></a>Risknivå (säkerhetsproblem)
En indikation (hög, medel eller låg) på allvarlighetsgraden för de problem att hjälpa användare att Identity Protection prioritera åtgärderna som de vidtar för att minska risken för organisationen.

### <a name="secure-identity"></a>Skydda (identitet)
Ta Reparationsåtgärd, till exempel en ändring av lösenord eller en dator som återställningen av avbildningen för att återställa en potentiellt komprometterade identitet till ett kompromisslös tillstånd.

### <a name="security-policy"></a>Säkerhetsprincip
En samling regler och villkor. En princip kan tillämpas på enheter som användare, grupper, appar, enheter, enhetsplattformar, enhetstillstånd, IP-intervall och Auth2.0 klienttyper. När en princip är aktiverad utvärderas när en entitet som ingår i principen är ut en token för en resurs.

### <a name="sign-in-v"></a>Logga in (v)
Att autentisera till en identitet i Azure Active Directory.

### <a name="sign-in-n"></a>Logga in (n)
Processen eller åtgärd för att autentisera en identitet i Azure Active Directory och den händelse som samlar in den här åtgärden.

### <a name="sign-in-from-anonymous-ip-address"></a>Logga in från anonyma IP-adress
En riskhändelse Utlöses efter en lyckad inloggning från IP-adress som har identifierats som en anonym proxy IP-adress.

### <a name="sign-in-from-infected-device"></a>Inloggning från angripen enhet
En riskhändelse som utlöses när en inloggning som kommer från en IP-adress som är känd som ska användas av en eller flera komprometterade enheter som aktivt försöker kommunicera med en bot-server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Logga in från IP-adress med misstänkt aktivitet
En riskhändelse utlöses när en lyckad inloggning från en IP-adress med ett stort antal misslyckade inloggningsförsök för flera användarkonton under en kort tidsperiod.

### <a name="sign-in-from-unfamiliar-location"></a>Logga in från okänd plats
En riskhändelse som utlöses när en användare loggar in från en ny plats (IP, latitud/longitud och ASN) har.

### <a name="sign-in-risk"></a>Inloggningsrisk
Se Risk nivå (logga in)

### <a name="sign-in-risk-policy"></a>Riskprincip för inloggning
En princip för villkorlig åtkomst som utvärderar risken för angrepp på en specifik inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="user-compromise-risk"></a>Användarrisk för kompromettering
Se Risk nivå (användaren kompromettering)

### <a name="user-risk"></a>Användarrisk
Se Risk nivå (användaren kompromettering).

### <a name="user-risk-policy"></a>Riskprincip för användare
En princip för villkorlig åtkomst som tar hänsyn till inloggningen och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="users-flagged-for-risk"></a>Användare som har flaggats för risk
Användare som har riskhändelser som är aktiva eller åtgärdade

### <a name="vulnerability"></a>Sårbarhet
En konfiguration eller ett villkor i Azure Active Directory, vilket gör att katalogen mottaglig för säkerhetsrisker eller hot.

## <a name="see-also"></a>Se också
* [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)

