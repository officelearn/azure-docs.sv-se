---
title: Azure Active Directory-riskidentifieringar | Microsoft-dokument
description: Denna artice ger dig en detaljerad översikt över vilka riskidentifieringar är.
services: active-directory
keywords: azure active directory identity protection, säkerhet, risk, risknivå, sårbarhet, säkerhetsprincip
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383964"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory-riskidentifieringar

De allra flesta säkerhetsöverträdelser äger rum när angripare får tillgång till en miljö genom att stjäla en användares identitet. Att upptäcka komprometterade identiteter är ingen lätt uppgift. Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas **en riskidentifiering**.

Det finns två platser där du granskar rapporterade riskidentifieringar:

 - **Azure AD-rapportering** – Riskidentifieringar är en del av Azure AD:s säkerhetsrapporter. Mer information finns i [säkerhetsrapporten för användare i riskzonen](concept-user-at-risk.md) och [säkerhetsrapporten för riskfyllda inloggningar](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** - Riskidentifieringar är också en del av rapporteringsfunktionerna i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Dessutom kan du använda [API:et för identitetsskyddsriskidentifiering för](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) att få programmatisk åtkomst till säkerhetsidentifieringar med Hjälp av Microsoft Graph. Mer information finns i [Komma igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/graph-get-started.md). 

Azure Active Directory identifierar för närvarande sex typer av riskidentifieringar:

- [Användare med läckta autentiseringsuppgifter](#leaked-credentials) 
- [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) 
- [Omöjliga resor till ovanliga platser](#impossible-travel-to-atypical-locations) 
- [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) 
- [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) 

![Riskdetektering](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Ibland kan du hitta en riskidentifiering utan motsvarande [inloggningspost i inloggningsrapporten](concept-sign-ins.md). Detta beror på att Identity Protection utvärderar risken för både **interaktiva** och **icke-interaktiva** inloggningar, medan inloggningsrapporten bara visar interaktiva inloggningar.

Den insikt du får för en identifierad riskidentifiering är knuten till din Azure AD-prenumeration. 

* Med **Azure AD Premium P2-utgåvan**får du den mest detaljerade informationen om alla underliggande identifieringar. 
* Med **Azure AD Premium P1-utgåvan**omfattas inte avancerade identifieringar (till exempel okända inloggningsegenskaper) av din licens och visas under namnet **Logga in med ytterligare risk upptäckt**. Dessutom döljs fälten risknivå och riskdetaljer.

Identifiering av riskidentifieringar utgör redan en viktig aspekt för att skydda dina identiteter, men du har också möjlighet att antingen manuellt åtgärda dem eller implementera automatiska svar genom att konfigurera principer för villkorlig åtkomst. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Typer av riskidentifiering

Egenskapen **riskidentifieringstyp** är en identifierare för den misstänkta åtgärd som en riskidentifieringspost har skapats för.

Microsofts kontinuerliga investeringar i identifieringsprocessen leder till:

- Förbättringar av detekteringsnoggrannheten hos befintliga riskidentifieringar 
- Nya typer av riskdetektering som kommer att läggas till i framtiden

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

När cyberbrottslingar äventyrar giltiga lösenord för legitima användare delar de ofta dessa autentiseringsuppgifter. Detta görs vanligtvis genom att publicera dem offentligt på den mörka webben eller klistra in webbplatser eller genom handel eller sälja referenser på den svarta marknaden. Microsoft läckte autentiseringsuppgifter tjänsten förvärvar användarnamn / lösenord par genom att övervaka offentliga och mörka webbplatser och genom att arbeta med:

- Forskare
- Brottsbekämpande
- Säkerhetsteam på Microsoft
- Andra tillförlitliga källor 

När tjänsten förvärvar användarnamn / lösenordspar, kontrolleras de mot AAD-användares aktuella giltiga autentiseringsuppgifter. När en matchning hittas betyder det att en användares lösenord har komprometterats och en **läckt riskidentifiering av autentiseringsuppgifter** skapas.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Den här riskidentifieringstypen identifierar användare som har loggat in från en IP-adress som har identifierats som en anonym proxy-IP-adress. Dessa proxyservrar används av personer som vill dölja enhetens IP-adress och kan användas för illvilligt uppsåt.

### <a name="impossible-travel-to-atypical-locations"></a>Omöjliga resor till ovanliga platser

Den här riskidentifieringstypen identifierar två inloggningar som kommer från geografiskt avlägsna platser, där minst en av platserna också kan vara atypisk för användaren, med tanke på tidigare beteende. Bland flera andra faktorer tar den här maskininlärningsalgoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma Autentiseringsuppgifter.

Algoritmen ignorerar uppenbara "falska positiva" som bidrar till de omöjliga resevillkoren, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärningsperiod på 14 dagar under vilken det lär sig en ny användares inloggningsbeteende. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Den här riskidentifieringstypen tar hänsyn till tidigare inloggningsplatser (IP, Latitude/ Longitud och ASN) för att fastställa nya/okända platser. Systemet lagrar information om tidigare platser som används av en användare och tar hänsyn till dessa "välbekanta" platser. Riskidentifieringen utlöses när inloggningen sker från en plats som inte redan finns i listan över välbekanta platser. Systemet har en inledande inlärningsperiod på 30 dagar, under vilken det inte flaggar några nya platser som okända platser. Systemet ignorerar också inloggningar från välbekanta enheter och platser som ligger geografiskt nära en välbekant plats. 

Identity Protection identifierar inloggningar från okända platser även för grundläggande autentisering / äldre protokoll. Eftersom dessa protokoll inte har moderna välbekanta funktioner som klient-ID finns det inte tillräckligt med telemetri för att minska falska positiva identifieringar. Om du vill minska antalet identifierade riskidentifieringar bör du gå över till modern autentisering.   

> [!NOTE]
> Om inloggningsanvändarnamnet och lösenordet inte stämmer överens kommer inloggningen att misslyckas och riskidentifieringen sker inte. Inloggning från okända platsriskidentifieringar utlöses endast vid lyckade inloggningar.

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här riskidentifieringstypen identifierar inloggningar från enheter som är infekterade med skadlig kod, som är kända för att aktivt kommunicera med en bot-server. Detta bestäms av korrelera IP-adresser för användarens enhet mot IP-adresser som var i kontakt med en bot-server. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet
Den här riskidentifieringstypen identifierar IP-adresser från vilka ett stort antal misslyckade inloggningsförsök sågs, över flera användarkonton, under en kort tidsperiod. Detta matchar trafikmönster för IP-adresser som används av angripare och är en stark indikator på att konton antingen redan eller är på väg att komprometteras. Det här är en maskininlärningsalgoritm som ignorerar uppenbara falskt positiva identifieringar, till exempel IP-adresser som regelbundet används av andra användare i organisationen.  Systemet har en inledande inlärningsperiod på 14 dagar där det lär sig inloggningsbeteendet för en ny användare och ny klientorganisation.

## <a name="detection-type"></a>Typ av identifiering

Egenskapen identifieringstyp är en indikator (**Realtid** eller **Offline**) för identifieringstidmen för en riskidentifiering. För närvarande identifieras de flesta riskidentifieringar offline i en efterbearbetning efter att riskidentifieringen har inträffat.

I följande tabell visas hur lång tid det tar för en identifieringstyp att visas i en relaterad rapport:

| Identifieringstyp | Rapportering svarstid |
| --- | --- |
| Realtid | 5 till 10 minuter |
| Offline | 2 till 4 timmar |


För riskidentifieringstyperna som Azure Active Directory identifierar är identifieringstyperna:

| Typ av riskidentifiering | Identifieringstyp |
| :-- | --- | 
| [Användare med läckta autentiseringsuppgifter](#leaked-credentials) | Offline |
| [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) | Realtid |
| [Omöjliga resor till ovanliga platser](#impossible-travel-to-atypical-locations) | Offline |
| [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) | Realtid |
| [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) | Offline |
| [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Risknivå

Risknivåegenskapen för en riskdetektering är en indikator (**Hög,** **Medel**eller **Låg**) för allvarlighetsgrad och förtroende för en riskdetektering. Den här egenskapen hjälper dig att prioritera de åtgärder du måste vidta. 

Riskdetekteringens allvarlighetsgrad representerar signalens styrka som en prediktor för identitetskompromiss. Förtroendet är en indikator på möjligheten till falska positiva resultat. 

Exempel: 

* **Hög**: Hög konfidens och hög allvarlighetsgrad riskdetektering. Dessa händelser är starka indikatorer på att användarens identitet har komprometterats, och alla användarkonton som påverkas bör åtgärdas omedelbart.

* **Medel:** Hög allvarlighetsgrad, men lägre konfidensriskdetektering, eller vice versa. Dessa händelser är potentiellt riskfyllda och alla användarkonton som påverkas bör åtgärdas.

* **Låg**: Lågt självförtroende och låg allvarlighetsgrad riskdetektering. Den här händelsen kanske inte kräver omedelbara åtgärder, men i kombination med andra riskidentifieringar kan det ge en stark indikation på att identiteten äventyras.

![Risknivå](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

Riskidentifieringar av läckta autentiseringsuppgifter klassificeras som **en hög**, eftersom de ger en tydlig indikation på att användarnamnet och lösenordet är tillgängliga för en angripare.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Risknivån för den här riskidentifieringstypen är **Medel** eftersom en anonym IP-adress inte är en stark indikation på en kontokompromiss. Vi rekommenderar att du omedelbart kontaktar användaren för att kontrollera om de använde anonyma IP-adresser.


### <a name="impossible-travel-to-atypical-locations"></a>Omöjliga resor till ovanliga platser

Omöjlig resa är vanligtvis en bra indikator på att en hackare framgångsrikt kunde logga in. Falskt positiva fel kan dock uppstå när en användare reser med en ny enhet eller använder ett VPN som vanligtvis inte används av andra användare i organisationen. En annan källa till falskt positiva identifieringar är program som felaktigt skickar server-IPs som klient-IPs, vilket kan ge intryck av inloggningar som äger rum från datacentret där programmets serverdel finns (ofta är dessa Microsoft-datacenter, vilket kan ge intryck av inloggningar som äger rum från Microsoft-ägda IP-adresser). Som ett resultat av dessa falska positiva är risknivån för denna riskdetektering **medel**.

> [!TIP]
> Du kan minska mängden rapporterade falskt positiva identifieringstyper för den här riskidentifieringstypen genom att konfigurera [namngivna platser](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Okända platser kan ge en stark indikation på att en angripare kan använda en stulen identitet. Falskt positiva kan uppstå när en användare reser, provar en ny enhet eller använder ett nytt VPN. Som ett resultat av dessa falska positiva identifieringar är risknivån för den här **händelsetypen Medel**.

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här riskidentifieringen identifierar IP-adresser, inte användarenheter. Om flera enheter är bakom en enda IP-adress, och endast vissa styrs av en bot nätverk, inloggningar från andra enheter min utlösa denna händelse i onödan, varför denna riskdetektering klassificeras som **låg**.  

Vi rekommenderar att du kontaktar användaren och söker igenom alla användarens enheter. Det är också möjligt att en användares personliga enhet är infekterad eller att någon annan använde en infekterad enhet från samma IP-adress som användaren. Infekterade enheter är ofta infekterade av skadlig kod som ännu inte har identifierats av antivirusprogram, och kan också indikera eventuella dåliga användarvanor som kan ha orsakat att enheten blir infekterad.

Mer information om hur du åtgärdar skadliga programinfektioner finns i [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet

Vi rekommenderar att du kontaktar användaren för att kontrollera om de verkligen loggat in från en IP-adress som har markerats som misstänkt. Risknivån för den här händelsetypen är "**Medium**" eftersom flera enheter kan ligga bakom samma IP-adress, medan endast vissa kan vara ansvariga för den misstänkta aktiviteten. 


## <a name="next-steps"></a>Efterföljande moment

* [Säkerhetsrapport för användare i riskzonen](concept-user-at-risk.md)
* [Säkerhetsrapport för riskfyllda inloggningar](concept-risky-sign-ins.md)
* [Azure AD-identitetsskydd](../active-directory-identityprotection.md).
