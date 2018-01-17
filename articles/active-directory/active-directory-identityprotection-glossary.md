---
title: "Azure Active Directory Identity Protection – ordlista | Microsoft Docs"
description: Azure Active Directory Identity Protection Glossary
services: active-directory
keywords: "Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprinciper och ordlista"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 30cf3911d0f22e2d9351fc606cd6697ef437e452
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection Glossary
### <a name="at-risk-user"></a>Risk (användare)
En användare med en eller flera aktiva riskhändelser. 

### <a name="atypical-sign-in-location"></a>Ovanlig inloggning plats
En inloggning från en geografisk plats som inte är typiska för specifika användare, liknande användare eller klienten.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
En säkerhetsmodul för Azure Active Directory som tillhandahåller en samlad vy riskhändelser och potentiella säkerhetsproblem som påverkar en organisations identiteter.

### <a name="conditional-access"></a>Villkorlig åtkomst
En princip för att skydda åtkomsten till resurser. Villkorliga tillgångsregler lagras i Azure Active Directory och utvärderas av Azure AD innan du beviljar åtkomst till resursen.  Exempel regler är att begränsa åtkomst baserat på plats, enhet hälsa eller user autentiseringsmetod.

### <a name="credentials"></a>Autentiseringsuppgifter
Information om identifiering och bekräftelse för identifiering som används för att få åtkomst till lokala resurser och nätverksresurser. Exempel på autentiseringsuppgifter är användarnamn och lösenord, smartkort och certifikat.

### <a name="event"></a>Händelse
En post för en aktivitet i Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falska positiva (risk händelse)
En risk händelsestatus ange manuellt av en Identity Protection-användare, som anger att händelsen risk undersöktes och felaktigt har flaggats som en händelse för risk.

### <a name="identity"></a>Identitet
En person eller enhet som måste verifieras genom autentisering, baserat på kriterier, till exempel lösenord eller ett certifikat.

### <a name="identity-risk-event"></a>Identitet risk händelse
AAD-händelse som har flaggats som avvikande av Identity Protection och kan tyda på att en identitet har komprometterats.

### <a name="ignored-risk-event"></a>Ignoreras (risk händelse)
En risk händelsestatus ange manuellt av en Identity Protection-användare, som anger att händelsen risk är stängd utan att ta en Reparationsåtgärd.

### <a name="impossible-travel-from-atypical-locations"></a>Omöjlig resa från ovanliga platser
En risk händelsen som utlöses när två inloggningar för samma användare identifieras där minst en av dem är från en ovanlig inloggning plats, och där tiden mellan inloggningarna är kortare än den minimitid som det tar att fysiskt färdas mellan dessa platser.  

### <a name="investigation"></a>Undersökning
Granska aktiviteter, loggar och annan relevant information som rör en händelse för risk att bestämma om reparation eller minimering steg är nödvändiga, förstå om och hur identitet har drabbats och förstå hur komprometteras identitet användes.

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter
En risk händelsen som utlöses när den aktuella användarens autentiseringsuppgifter (användarnamn och lösenord) hittas offentligt anslagits i webb-mörkt av våra forskare.

### <a name="mitigation"></a>Åtgärd
En åtgärd för att begränsa eller ta bort en angripares möjlighet att utnyttja en komprometterad identitet eller en enhet utan att återställa identitet eller enhet till ett säkert tillstånd. En lösning kan inte matchas tidigare riskhändelser som associeras med identiteten eller enhet.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
En autentiseringsmetod som kräver två eller flera autentiseringsmetoder, som kan innehålla något användaren har certifikatet. något användaren vet, till exempel användarnamn, lösenord eller pass fraser. fysiska attribut, till exempel ett tumavtryck; och personliga attribut, till exempel en personlig signatur.

### <a name="offline-detection"></a>Offline-identifiering
Identifiering av avvikelser och utvärdering av risken för en händelse som inloggning försök efteråt för en händelse som redan har skett.

### <a name="policy-condition"></a>Villkor
En del av en säkerhetsprincip som definierar entiteter (grupper, användare, appar, enhetsplattformar, enhetstillstånden, IP-adressintervall, klienttyper) ingår i principen eller uteslutas från den.

### <a name="policy-rule"></a>Principregel
Del av en säkerhetsprincip som beskriver de omständigheter som ska utlösa principen och de åtgärder som vidtas när principen utlöses.

### <a name="prevention"></a>Prevention (Skydd)
En åtgärd för att förhindra att organisationen via missbruk av en identitet eller enhet misstänkt eller vet äventyras. En åtgärd för att förhindra skydda inte enheten eller identitet och matchar inte tidigare riskhändelser.

### <a name="privileged-user"></a>Privilegierad (användare)
En användare som vid tidpunkten för en risk händelse hade permanenta eller tillfälliga administratörsbehörigheter till minst en resurs i Azure Active Directory, exempelvis en Global administratör faktureringsadministratör, administratör, Användaradministratör och Lösenordsadministratör. 

### <a name="real-time"></a>Realtid
Finns i realtid identifiering.

### <a name="real-time-detection"></a>Realtid identifiering
Identifiering av avvikelser och utvärdering av risken för en händelse som inloggning försök innan händelsen kan fortsätta.

### <a name="remediated-risk-event"></a>Åtgärdad (risk händelse)
En risk händelsestatus anges automatiskt av identitetsskydd, som anger att händelsen risk har reparerats använder standard Reparationsåtgärd för den här typen av risk händelse. Till exempel åtgärdas automatiskt många riskhändelser som indikerar att det tidigare lösenordet har drabbats när användarens lösenord återställs.

### <a name="remediation"></a>Åtgärd
En åtgärd för att skydda en identitet eller en enhet som har tidigare eller misstänks vara hotad. En Reparationsåtgärd återställer identitet eller enhet till ett säkert tillstånd och löser tidigare riskhändelser som associeras med identiteten eller enhet.

### <a name="resolved-risk-event"></a>Löst (risk händelse)
En risk händelsestatus anges manuellt av en användare som identitetsskydd, som anger att användaren har gjort en lämplig Reparationsåtgärd utanför identitetsskydd och att risk händelsen ska betraktas som stängd.

### <a name="risk-event-status"></a>Risk händelsestatus
En egenskap för en risk händelse som anger om händelsen är aktiv och om stängda orsaken till varför den.

### <a name="risk-event-type"></a>Typ av riskhändelse
En kategori för händelsen risk som anger vilken typ av avvikelseidentifiering som orsakade händelsen anses vara riskfyllda.

### <a name="risk-level-risk-event"></a>Risknivå (risk händelse)
Uppgift (hög, medel eller låg) om allvarlighetsgrad för händelsen risken att hjälpa användare att identitetsskydd prioritera åtgärderna som de vidtar för att minska risken för organisationen. 

### <a name="risk-level-sign-in"></a>Risknivå (inloggning)
Uppgift (hög, medel eller låg) av sannolikheten att för en specifik inloggning, någon annan försöker använda användarens identitet.

### <a name="risk-level-user-compromise"></a>Risknivå (användare kompromettering)
Uppgift (hög, medel eller låg) av sannolikheten att en identitet har komprometterats.

### <a name="risk-level-vulnerability"></a>Risknivå (säkerhetsproblem)
Uppgift (hög, medel eller låg) om allvarlighetsgraden säkerhetsproblem att hjälpa användare att identitetsskydd prioritera åtgärderna som de vidtar för att minska risken för organisationen.

### <a name="secure-identity"></a>Skydda (identitet)
Ta Reparationsåtgärd t.ex ändra lösenordet eller datorn återställning av avbildning för att återställa en potentiellt komprometterade identitet till en kompromisslös tillstånd.

### <a name="security-policy"></a>Säkerhetsprincip
En uppsättning regler och villkor. En princip kan tillämpas på enheter, till exempel användare, grupper, appar, enheter, enhetsplattformar, enhetstillstånden, IP-adressintervall och Auth2.0 klienttyper. När en princip är aktiverad utvärderas när en enhet som ingår i principen utfärdas en token för en resurs.

### <a name="sign-in-v"></a>Logga in (v)
Att autentisera till en identitet i Azure Active Directory.

### <a name="sign-in-n"></a>Logga in (n)
Processen eller åtgärd för att autentisera en identitet i Azure Active Directory och den händelse som samlar in den här åtgärden.

### <a name="sign-in-from-anonymous-ip-address"></a>Logga in från anonyma IP-adress
En risk händelse utlöses efter en lyckad inloggning från IP-adress som har identifierats som en anonym proxyserver IP-adress.

### <a name="sign-in-from-infected-device"></a>Logga in från infekterade enheter
En risk händelsen som utlöses när en inloggning som kommer från en IP-adress som är känd som ska användas av en eller flera avslöjade enheter som aktivt försöker kommunicera med en botserver.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Logga in från IP-adresser med misstänkt aktivitet
En risk händelsen utlöses när en lyckad inloggning från en IP-adress med ett stort antal misslyckade inloggningsförsök för flera användarkonton under en kort tidsperiod.

### <a name="sign-in-from-unfamiliar-location"></a>Logga in från okänd plats
En risk händelsen som utlöses när en användare loggar in från en ny plats (IP, latitud/longitud och ASN) har.

### <a name="sign-in-risk"></a>Inloggningsrisk
Finns Risk nivå (inloggning)

### <a name="sign-in-risk-policy"></a>Riskprincip för inloggning
En villkorlig åtkomstprincip som utvärderar risken för en specifik inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="user-compromise-risk"></a>Användaren har komprometterats risk
Finns Risk nivå (användare kompromettering)

### <a name="user-risk"></a>Användarrisk
Finns Risk nivå (användare kompromettering).

### <a name="user-risk-policy"></a>Riskprincip för användare
En villkorlig åtkomstprincip som överväger logga in och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="users-flagged-for-risk"></a>Användare som har flaggats för risk
Användare som har riskhändelser som är aktiva eller reparerade

### <a name="vulnerability"></a>Sårbarhet
En konfiguration eller villkor i Azure Active Directory som gör att katalogen känsligt för kryphål eller hot.

## <a name="see-also"></a>Se också
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

