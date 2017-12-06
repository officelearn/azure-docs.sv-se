---
title: "Azure Active Directory-riskhändelser | Microsoft Docs"
description: "Det här avsnittet ger en detaljerad översikt över riskhändelser är."
services: active-directory
keywords: "Azure active directory identitetsskydd, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip"
author: MarkusVi
manager: femila
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ff0bcf9bdf9eaf1b4f0084acf9e5ee6ccfeba19
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory-riskhändelser

Det stora flertalet av säkerhetsintrång sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Identifiering av avslöjade identiteter finns ingen enkel aktivitet. Azure Active Directory använder anpassningsbar maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användarkonton. Alla upptäckta misstänkta åtgärd lagras i en post som kallas *risk händelsen*.

För närvarande identifierar Azure Active Directory sex typer av riskhändelser:

- [Användare med läckta autentiseringsuppgifter](#leaked-credentials) 
- [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) 
- [Omöjligt att resa till ovanliga platser](#impossible-travel-to-atypical-locations) 
- [Inloggningar från infekterade enheter](#sign-ins-from-infected-devices) 
- [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) (exklusivt för **Azure Active Directory Premium P2** versioner)

![Risk händelse](./media/active-directory-reporting-risk-events/91.png)

Det här avsnittet får du en detaljerad översikt över vilka riskhändelser är och hur du kan använda dem för att skydda din Azure AD-identiteter.


## <a name="risk-event-types"></a>Typer av riskhändelser

Egenskapen risk händelse typ är en identifierare för åtgärden misstänkta en händelsepost för risk har skapats för.  
Microsofts kontinuerliga investeringar i identifieringsprocessen leda till:

- Förbättringar av noggrannhet identifiering av befintliga riskhändelser 
- Ny risk händelsetyper som ska läggas till i framtiden

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

När andra nätkriminella utgör ett giltigt lösenord för behöriga användare, dela kriminella ofta dessa autentiseringsuppgifter. Detta görs normalt genom att publicera dem offentligt på mörka platser för webb- eller klistra in eller genom att kompromissa eller sälja autentiseringsuppgifter på den svarta marknaden. Microsoft läcka ut autentiseringsuppgifter service hämtar användarnamn / lösenord par genom att övervaka offentliga och mörkt webbplatser och genom att arbeta med:

- Forskare
- Polis och rättsväsende
- Säkerhet team på Microsoft
- Andra tillförlitliga källor 

När tjänsten får användarnamn / lösenord par de kontrolleras mot AAD användarnas aktuella giltiga autentiseringsuppgifter. När en matchning hittas, innebär det att en användares lösenord har komprometterats, och en *läcka ut autentiseringsuppgifter risk händelse* skapas.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Den här typen av risk händelse identifierar användare som har loggat in från en IP-adress har identifierats som en anonym proxyserver IP-adress. Dessa proxyservrar används av personer som du vill dölja enhetens IP-adress och kan användas för skadliga åtgärder.


### <a name="impossible-travel-to-atypical-locations"></a>Omöjligt att resa till ovanliga platser

Den här typen av risk händelse identifierar två inloggningar som sker från geografiskt avlägsna platser, där minst en av platserna kanske också onormal för användaren, anges tidigare beteende. Bland flera andra faktorer tar machine learning algoritmen hänsyn till tiden mellan de två inloggningarna och den tid som det skulle ha tagit för användaren att förflytta sig från den första platsen till den andra, som anger att en annan användare använder samma autentiseringsuppgifter.

Algoritmen ignorerar uppenbara ”falska positiva identifieringar” bidrar till omöjligt att resa-villkor, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande learning-period på 14 dagar då den lär sig en ny användare loggar in beteende. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Den här typen av risk händelse anser tidigare inloggning platser (IP, latitud / longitud och ASN) att fastställa nya / okända platser. Systemet lagrar information om tidigare platser som används av en användare så att dessa ”bekant” platser. Risk-händelsen utlöses när inloggningen sker från en plats som inte är redan i listan över välkända platser. Systemet har en inledande learning-period på 30 dagar då inte flaggas några nya platser som okända platser. Inloggningar från bekant enheter och platser som är geografiskt nära en bekant plats ignoreras också. 

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här typen av risk händelse identifierar inloggningar från enheter som infekterats med skadlig kod, som är kända aktivt kommunicera med en botserver. Detta bestäms genom att sammanföra IP-adresser för användarens enhet mot IP-adresser som varit i kontakt med en botserver. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet
Den här typen av risk händelse identifierar IP-adresser som ett stort antal misslyckade inloggningsförsök visades, för flera användarkonton under en kort tidsperiod. Detta matchar trafikmönster för IP-adresser som används av angripare och är en starkt indikator att konton är antingen redan eller håller på att äventyras. Det här är en maskininlärningsalgoritmen som ignorerar uppenbara ”*FALSKT positiva*”, till exempel IP-adresser som regelbundet används av andra användare i organisationen.  Systemet har en inledande learning-period på 14 dagar där den lär sig inloggning beteendet för en ny användare och nya innehavaren.


## <a name="detection-type"></a>Identifieringstyp

Egenskapen type identifiering är en indikator (realtid eller Offline) för identifiering tidsramen för en händelse för risk.  
För närvarande kan identifieras de flesta riskhändelser offline i en åtgärd för efterbearbetning när risken händelse har inträffat.

I följande tabell visas hur lång tid det tar för en typ av identifiering till visas i en relaterad rapport:

| Identifiering av typen | Rapportering svarstid |
| --- | --- |
| Realtid | 5-10 minuter |
| Offline | 2-4 timmar |


För risk händelsetyper Azure Active Directory identifierar, är identifiering-typer:

| Risk händelsetyp | Identifiering av typen |
| :-- | --- | 
| [Användare med läckta autentiseringsuppgifter](#leaked-credentials) | Offline |
| [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) | Realtid |
| [Omöjligt att resa till ovanliga platser](#impossible-travel-to-atypical-locations) | Offline |
| [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) | Realtid |
| [Inloggningar från infekterade enheter](#sign-ins-from-infected-devices) | Offline |
| [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Risknivå

Egenskapen risk nivå för en händelse för risk är en indikator (hög, medel eller låg) för allvarlighetsgrad och förtroende för en händelse för risk. Den här egenskapen hjälper dig att prioritera vilka åtgärder du måste utföra. 

Allvarlighetsgrad för händelsen risk representerar styrkan hos signalen som en ge prognoser för identitet har komprometterats.  
Förtroende är en indikator för falska positiva identifieringar. 

Exempel: 

* **Hög**: hög exakthet och hög allvarlighetsgrad risk händelse. Dessa händelser är starkt indikatorer som användarens identitet har komprometterats och alla användarkonton som påverkas bör åtgärdas omedelbart.

* **Medel**: Hög allvarlighetsgrad, men lägre förtroende risk händelse, och vice versa. Dessa händelser är potentiellt riskfyllda och alla användarkonton som påverkas bör åtgärdas.

* **Låg**: låg förtroende och låg angelägenhetsgrad risk händelse. Den här händelsen kan inte kräva en omedelbar åtgärd, men när den kombineras med andra riskhändelser kan ger en stark indikation på att identiteten äventyras.

![Risknivå](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

Läcka ut autentiseringsuppgifter riskhändelser klassificeras som en **hög**, eftersom de ger en tydlig indikation på att användarnamn och lösenord är tillgängliga för en angripare.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Risknivå för den här typen av risk händelse är **medel** eftersom en anonym IP-adress inte är en stark indikation på ett konto har komprometterats.  
Vi rekommenderar att du omedelbart kontaktar användaren för att kontrollera om de använder anonym IP-adresser.


### <a name="impossible-travel-to-atypical-locations"></a>Omöjligt att resa till ovanliga platser

Omöjlig resa är vanligtvis en bra indikator som en hackare har kunnat har inloggning. FALSKT positiva kan dock uppstå när en användare reser med hjälp av en ny enhet eller en VPN-anslutning som normalt inte används av andra användare i organisationen. En annan källa för falskt positiva är program som skickar felaktigt server IP-adresser som klient IP-adresser, som kan ge ut av inloggningar äger rum från datacentret där programmet har backend-värd (det är ofta Microsoft datacenter, vilket kan ge intryck av inloggningar tar placera från Microsoft som ägs av IP-adresser). På grund av dessa FALSKT positiva risknivå för den här risken händelsen är **medel**.

> [!TIP]
> Du kan minska mängden rapporterade false-positves för den här typen av risk händelsen genom att konfigurera [med namnet platser](active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Okända platser kan ge en stark indikation på att en angripare kan använda en stulen identitet. FALSE-positiva identifieringar kan uppstå när en användare reser, testar en ny enhet eller använder en ny VPN-anslutning. På grund av dessa falska positiva identifieringar risknivå för den här händelsen är **medel**.

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här risken händelsen identifierar IP-adresser, inte användarenheter. Om flera enheter som finns bakom en IP-adress och endast vissa är styrs av ett bot nätverk, inloggningar från andra enheter min utlösaren händelsen i onödan, vilket är orsaken till att klassificera risk händelsen som **låg**.  

Vi rekommenderar att du kontakta användaren och söka igenom alla användares enheter. Det är också möjligt att en användares personliga enhet har infekterats eller som tidigare nämnts som någon annan har använt en infekterad enhet från samma IP-adress som användare. Infekterade enheter är ofta smittats av skadlig kod som ännu inte identifierats av antivirusprogram och kan också indikera som felaktiga användaren vanor som kan ha orsakat enheten infekteras.

Mer information om hur du adress infekteras av skadlig kod finns på [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet

Vi rekommenderar att du kontaktar användaren för att kontrollera om de faktiskt loggas in från en IP-adress som har markerats som misstänkt. Risknivå för den här händelsen är ”**medelstor**” eftersom flera enheter kan vara bakom samma IP-adress, men endast vissa kan vara ansvarig för misstänkt aktivitet. 


 
## <a name="next-steps"></a>Nästa steg

Riskhändelser är grunden för att skydda din Azure AD identiteter. Azure AD kan för närvarande identifieras sex riskhändelser: 


| Risk händelsetyp | Risknivå | Identifiering av typen |
| :-- | --- | --- |
| [Användare med läckta autentiseringsuppgifter](#leaked-credentials) | Hög | Offline |
| [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) | Medel | Realtid |
| [Omöjligt att resa till ovanliga platser](#impossible-travel-to-atypical-locations) | Medel | Offline |
| [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) | Medel | Realtid |
| [Inloggningar från infekterade enheter](#sign-ins-from-infected-devices) | Låg | Offline |
| [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) | Medel | Offline|

Där kan du hitta riskhändelser som har identifierats i din miljö?
Det finns två platser där du kan granska rapporterade riskhändelser:

 - **Azure AD-rapportering** -riskhändelser är en del av Azure AD-säkerhetsgrupp rapporter. Mer information finns i [användare på risk säkerhetsrapporten](active-directory-reporting-security-user-at-risk.md) och [riskfyllda inloggningar säkerhetsrapporten](active-directory-reporting-security-risky-sign-ins.md).

 - **Azure AD Identity Protection** -riskhändelser är också en del av [Azure Active Directory Identity Protection](active-directory-identityprotection.md) rapporteringsfunktioner.
    

När identifieringen av riskhändelser redan representerar en viktig del av skydda identiteter, har också möjlighet att behandla dem manuellt eller även implementera automatiska svar genom att konfigurera principer för villkorlig åtkomst. Mer information finns i [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 
