---
title: Azure Active Directory risk identifieringar | Microsoft Docs
description: Den här Artice ger dig en detaljerad översikt över vilka risk identifieringar som är.
services: active-directory
keywords: Azure Active Directory Identity Protection, säkerhet, risk, risknivå, sårbarhet, säkerhets princip
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
ms.openlocfilehash: e1f3755d61b5fa082665cfdb9aa91d1e31e2d4e4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014470"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory risk identifieringar

De flesta säkerhets överträdelser sker när angripare får åtkomst till en miljö genom att stjäla en användares identitet. Det är ingen enkel uppgift att identifiera komprometterade identiteter. Azure Active Directory använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användar konton. Varje misstänkt misstänkt åtgärd lagras i en post som kallas för **identifiering av risker**.

Det finns två platser där du kan granska rapporterade risk identifieringar:

 - **Azure AD-rapportering** – risk identifieringar ingår i Azure Ads säkerhets rapporter. Mer information finns i säkerhets rapporten [användare vid risk säkerhet](concept-user-at-risk.md) och [säkerhets rapporten för riskfyllda inloggningar](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** -risk identifieringar är också en del av rapporterings funktionerna i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Dessutom kan du använda [identitets skyddets identifierings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) för att få program mässig åtkomst till säkerhets identifieringar med hjälp av Microsoft Graph. Mer information finns i [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/graph-get-started.md). 

För närvarande identifierar Azure Active Directory sex typer av risk identifieringar:

- [Användare med läckta autentiseringsuppgifter](#leaked-credentials) 
- [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) 
- [Omöjlig resa till ovanlig platser](#impossible-travel-to-atypical-locations) 
- [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) 
- [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) 

![Identifiering av risker](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Ibland kan du hitta en risk identifiering utan motsvarande inloggnings post i [inloggnings rapporten](concept-sign-ins.md). Detta beror på att identitets skyddet utvärderar risker för både **interaktiva** och **icke-interaktiva** inloggningar, medan inloggnings rapporten endast visar interaktiva inloggningar.

De insikter som du får för en identifierad risk identifiering är knutna till din Azure AD-prenumeration. 

* Med **Azure AD Premium P2-versionen**får du den mest detaljerade informationen om alla underliggande identifieringar. 
* Med **Azure AD Premium P1-versionen**omfattas inte avancerade identifieringar (till exempel okända inloggnings egenskaper) av din licens och visas under namn **inloggningen med ytterligare risk upptäckt**. Dessutom är fälten risk nivå och risk detalj dolda.

Även om identifieringen av risk identifiering redan representerar en viktig aspekt av att skydda dina identiteter, kan du också välja att antingen manuellt adressera dem eller implementera automatiska svar genom att konfigurera principer för villkorlig åtkomst. Mer information finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Typer av risk identifiering

Egenskapen **typ av risk identifiering** är en identifierare för den misstänkta åtgärd som en risk identifierings post har skapats för.

Microsofts kontinuerliga investeringar i identifierings processen leder till:

- Förbättringar av identifierings precisionen för befintliga risk identifieringar 
- Nya risk identifierings typer som kommer att läggas till i framtiden

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

När cyberbrottslingar kompromettera giltiga lösen ord för legitima användare delar de ofta dessa autentiseringsuppgifter. Detta görs vanligt vis genom att publicera dem offentligt på de mörka webb-eller Inklistrings platserna eller genom handel eller sälja de autentiseringsuppgifter som finns på den svarta marknaden. Tjänsten Microsoft läcker autentiseringsuppgifter hämtar användar namn/lösen ord genom att övervaka offentliga och mörka webbplatser och genom att arbeta med:

- Forskare
- Juridisk tillämpning
- Säkerhets team på Microsoft
- Andra betrodda källor 

När tjänsten hämtar användar namn/lösen ord-par kontrol leras de mot AAD-användarens aktuella giltiga autentiseringsuppgifter. När en matchning hittas innebär det att en användares lösen ord har komprometterats och att **identifiering av läckta autentiseringsuppgifter** har skapats.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Den här typen av risk identifiering identifierar användare som har loggat in från en IP-adress som har identifierats som en anonym proxy-IP-adress. Dessa proxyservrar används av personer som vill dölja sina enheters IP-adress och kan användas i skadligt syfte.

### <a name="impossible-travel-to-atypical-locations"></a>Omöjliga resor till ovanliga platser

Den här typen av risk identifiering identifierar två inloggningar som härstammar från geografiskt avlägsna platser, där minst en av platserna också kan vara ovanlig för användaren, med hänsyn till tidigare beteende. Bland flera andra faktorer tar den här Machine Learning-algoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma klientautentiseringsuppgifter.

Algoritmen ignorerar uppenbara "falska positiva identifieringar" som bidrar till de omöjliga rese villkoren, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärnings period på 14 dagar under vilken den lär sig en ny användares inloggnings beteende. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Den här typen av risk identifiering betraktar tidigare inloggnings platser (IP, latitud/longitud och ASN) för att fastställa nya/okända platser. Systemet lagrar information om tidigare platser som används av en användare och betraktar dessa "välkända" platser. Identifieringen av risker utlöses när inloggningen sker från en plats som inte redan finns i listan över välbekanta platser. Systemet har en inledande inlärnings period på 30 dagar, där den inte flaggar några nya platser som okända platser. Systemet ignorerar också inloggningar från välbekanta enheter och platser som är geografiskt nära en bekant plats. 

Identitets skyddet identifierar inloggningar från okända platser även för grundläggande autentisering/äldre protokoll. Eftersom dessa protokoll inte har moderna välbekanta funktioner, till exempel klient-ID, finns det inte tillräckligt med telemetri för att minska antalet falska positiva identifieringar. För att minska antalet identifierade risk identifieringar bör du flytta till modern autentisering.   

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här typen av risk identifiering identifierar inloggningar från enheter som har infekterats av skadlig kod, som är kända för att kommunicera med en bot-Server. Detta bestäms genom att korrelera IP-adresserna för användarens enhet mot IP-adresser som var i kontakt med en bot-Server. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet
Den här typen av risk identifiering identifierar IP-adresser från vilka ett stort antal misslyckade inloggnings försök setts, över flera användar konton under en kort tids period. Detta matchar trafik mönster i IP-adresser som används av angripare och är en stark indikator som konton antingen redan eller är på väg att komprometteras. Det här är en Machine Learning-algoritm som ignorerar uppenbara falskt-positiva, till exempel IP-adresser som används regelbundet av andra användare i organisationen.  Systemet har en inledande inlärnings period på 14 dagar där den lär sig inloggnings beteendet för en ny användare och ny klient.

## <a name="detection-type"></a>Identifierings typ

Egenskapen identifierings typ är en indikator (i**real tid** eller **offline**) för identifierings ramen för en risk identifiering. För närvarande identifieras de flesta risk identifieringar offline i en efter bearbetnings åtgärd efter det att risken har inträffat.

I följande tabell visas hur lång tid det tar för en identifierings typ som visas i en relaterad rapport:

| Identifierings typ | Rapport svars tid |
| --- | --- |
| Real tids | 5 till 10 minuter |
| Offline | 2 till 4 timmar |


För identifierings typerna Azure Active Directory identifieras:

| Typ av risk identifiering | Identifierings typ |
| :-- | --- | 
| [Användare med läckta autentiseringsuppgifter](#leaked-credentials) | Offline |
| [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) | Real tids |
| [Omöjlig resa till ovanlig platser](#impossible-travel-to-atypical-locations) | Offline |
| [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) | Real tids |
| [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) | Offline |
| [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Risk nivå

Egenskapen risk nivå för en risk identifiering är en indikator (**hög**, **medel**eller **låg**) för allvarlighets grad och förtroende för en risk identifiering. Med den här egenskapen kan du prioritera de åtgärder som du måste vidta. 

Risk identifieringens allvarlighets grad representerar styrkan hos signalen som en förväntare av identitets kompromisser. Förtroendet är en indikator för möjligheten att falska positiva identifieringar. 

Exempel: 

* **Hög**: hög exakthet och risk identifiering med hög allvarlighets grad. Dessa händelser är starka indikatorer på att användarens identitet har komprometterats och att alla användar konton som påverkas bör åtgärdas omedelbart.

* **Medel**: hög allvarlighets grad, men lägre risk identifiering eller vice versa. Dessa händelser är potentiellt riskfyllda och eventuella användar konton som påverkas bör åtgärdas.

* **Låg**: låg exakthet och låg allvarlighets grad för risk identifiering. Den här händelsen kan inte kräva en omedelbar åtgärd, men när den kombineras med andra risk identifieringar kan det vara en stark indikation på att identiteten har komprometterats.

![Risk nivå](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

Läckta identifieringar av autentiseringsuppgifter klassificeras som **höga**, eftersom de ger en tydlig indikation på att användar namnet och lösen ordet är tillgängliga för en angripare.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Risk nivån för den här typen av risk identifiering är **medels Tor** eftersom en anonym IP-adress inte är en stark indikation på en konto kompromiss. Vi rekommenderar att du omedelbart kontaktar användaren för att kontrol lera om de använde anonyma IP-adresser.


### <a name="impossible-travel-to-atypical-locations"></a>Omöjliga resor till ovanliga platser

Omöjlig resa är vanligt vis en korrekt indikator att en hackare kunde logga in. Falska positiva identifieringar kan dock inträffa när en användare reser med en ny enhet eller använder ett VPN som vanligt vis inte används av andra användare i organisationen. En annan källa med falskt-positiva objekt är program som felaktigt skickar Server-IP-adresser som klient-IP-adresser, vilket kan ge dig en del av inloggningar från data centret där programmets backend är värdbaserad (ofta är dessa Microsoft-datacenter, som kan ge utseendet på inloggningar som sker från Microsofts ägda IP-adresser). Som ett resultat av dessa falska positiva identifieringar är risk nivån **medium**.

> [!TIP]
> Du kan minska mängden rapporterade falska positiva identifieringar för den här typen av risk identifiering genom att konfigurera [namngivna platser](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Okända platser kan ge en stark indikation på att en angripare kan använda en stulen identitet. Falska positiva identifieringar kan uppstå när en användare reser, försöker med en ny enhet eller använder en ny VPN-anslutning. Som ett resultat av dessa falska positiva identifieringar är risk nivån för den här händelse typen **medel**.

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här identifieringen av risker identifierar IP-adresser, inte användar enheter. Om flera enheter finns bakom en enda IP-adress och endast vissa styrs av ett bot nätverk, utlöser inloggningen från andra enheter den här händelsen i onödan, vilket är orsaken till att den här risk identifieringen klassificeras som **låg**.  

Vi rekommenderar att du kontaktar användaren och genomsöker alla användarens enheter. Det är också möjligt att en användares personliga enhet är infekterad eller att någon annan använde en infekterad enhet från samma IP-adress som användaren. Infekterade enheter är ofta infekterade av skadlig kod som ännu inte har identifierats av antivirus program, och det kan också indikera eventuella dåliga användar vanor som kan ha orsakat att enheten skulle infekteras.

Mer information om hur du tar itu med skadlig kod finns i [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet

Vi rekommenderar att du kontaktar användaren för att kontrol lera om de faktiskt har loggat in från en IP-adress som marker ATS som misstänkt. Risk nivån för den här händelse typen är "**medel**" eftersom flera enheter kan finnas bakom samma IP-adress, medan bara vissa kan vara ansvariga för den misstänkta aktiviteten. 


## <a name="next-steps"></a>Nästa steg

* [Säkerhets rapport över användare i risk](concept-user-at-risk.md)
* [Säkerhets rapport för riskfyllda inloggningar](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
