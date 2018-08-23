---
title: Azure Active Directory-riskhändelser | Microsoft Docs
description: Den här artice ger dig en detaljerad översikt över riskhändelser är.
services: active-directory
keywords: identitetsskydd för Azure active directory, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
author: priyamohanram
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/14/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fe5bcce06ef02bae75bf9fdaf5702a99f485885c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42058451"
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory-riskhändelser

Merparten av säkerhetsbrott sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Identifiera komprometterade identiteter är ingen enkelt uppgift. Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas *riskhändelsen*.

För närvarande kan identifierar Azure Active Directory sex typer av riskhändelser:

- [Användare med läckta autentiseringsuppgifter](#leaked-credentials) 
- [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) 
- [Omöjlig resa till ovanliga platser](#impossible-travel-to-atypical-locations) 
- [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) 
- [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) 


![Riskhändelse](./media/concept-risk-events/91.png)

Den information som du får en identifierad riskhändelse är kopplad till din Azure AD-prenumeration. Med Azure AD Premium P2-versionen får du den mest detaljerade informationen om alla underliggande identifieringar. Med Azure AD Premium P1-versionen identifieringar som inte omfattas av din licens visas som riskhändelsen **inloggning med identifierad ytterligare risk**.


Den här artikeln får du en detaljerad översikt över vilka riskhändelser är och hur du kan använda dem för att skydda din Azure AD-identiteter.


## <a name="risk-event-types"></a>Typer av riskhändelser

Risk event type-egenskapen är en identifierare för åtgärden misstänkta en händelsepost för risker som har skapats för.

Microsofts kontinuerlig investeringar i identifieringsprocessen leda till:

- Förbättringar av identifieringen av riskhändelser 
- Nya typer av riskhändelser som ska läggas till i framtiden

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

När andra nätkriminella kompromettering giltig lösenorden för behöriga användare, dela kriminella ofta dessa autentiseringsuppgifter. Detta görs normalt genom att publicera dem offentligt på mörk webb- eller klistra in platser eller av handel eller sälja autentiseringsuppgifter på svarta marknaden. Microsofts läcka ut autentiseringsuppgifter tjänsten hämtar användarnamn / lösenord kombinationer genom att övervaka offentliga och mörka webbplatser och genom att arbeta med:

- Forskare
- Brottsbekämpande myndigheter
- Security Team på Microsoft
- Andra betrodda källor 

När tjänsten hämtar användarnamn / lösenord-par som, de kontrolleras mot AAD användarnas aktuella giltiga autentiseringsuppgifter. När en matchning hittas, innebär det att en användares lösenord har komprometterats, och en *läcka ut autentiseringsuppgifter riskhändelse* har skapats.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Den här riskhändelsetyp identifierar användare som har loggat in från en IP-adress som har identifierats som en anonym proxy IP-adress. Dessa proxyservrar används av personer som vill dölja sina enheters IP-adress och kan användas i skadligt syfte.


### <a name="impossible-travel-to-atypical-locations"></a>Omöjliga resor till ovanliga platser

Den här riskhändelsetyp identifierar två inloggningar från geografiskt avlägsna platser, där kanske minst en av platserna som också är ovanlig för användaren, får de senaste beteende. Bland flera andra faktorer tar den här maskininlärningsalgoritmen hänsyn till tiden mellan två inloggningar och hur lång tid det skulle ha tagit för användaren att färdas från den första platsen till den andra, som anger att en annan användare använder samma autentiseringsuppgifter.

Algoritmen ignorerar uppenbara ”FALSKT positiva resultat” bidrar till omöjlig resa villkor, till exempel virtuella privata nätverk och platser som ofta används av andra användare i organisationen. Systemet har en inledande inlärningsperiod på 14 dagar under vilken den lär sig en ny användare logga in beteende. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Den här riskhändelsetyp tar hänsyn till de senaste inloggning platser (IP, latitud / longitud och ASN) att fastställa nya / okända platser. Systemet lagrar information om tidigare platser som används av en användare och så att platserna ”välbekanta”. Riskhändelsen utlöses när inloggningen sker från en plats som inte redan finns i listan med välbekanta platser. Systemet har en inledande inlärningsperiod på 30 dagar, då det inte flaggar några nya platser som okända platser. Systemet ignorerar också inloggningar från välbekanta enheter och platser som ligger geografiskt nära en bekant plats. 

Identity Protection identifierar inloggningar från okända platser också för grundläggande autentisering / äldre protokoll. Eftersom dessa protokoll inte har modern välkända funktioner, till exempel klient-id, finns det inte tillräckligt med telemetri för att minska falska positiva identifieringar. För att minska antalet identifierade riskhändelserna, bör du flytta till modern autentisering.   

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här riskhändelsetyp identifierar inloggningar från enheter som infekterats med skadlig kod, som är kända för att aktivt kommunicera med en bot-server. Detta fastställs genom att IP-adresserna för användarens enhet mot IP-adresser som varit i kontakt med en bot-server. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet
Den här riskhändelsetyp identifierar IP-adresser som ett stort antal misslyckade inloggningsförsök visades, för flera användarkonton under en kort tidsperiod. Detta matchar trafikmönster för IP-adresser som används av angripare och är en stark indikator att konton är antingen redan eller håller på att äventyras. Det här är en maskininlärningsalgoritm som ignorerar uppenbara ”*falskpositiva resultat*”, t.ex. IP-adresser som regelbundet som används av andra användare i organisationen.  Systemet har en inledande inlärningsperiod på 14 dagar där den lär sig beteendet inloggning för en ny användare och en ny klient.


## <a name="detection-type"></a>Identifieringstyp

Typegenskapen identifiering är en indikator (i realtid eller Offline) för identifiering av tidsramen för en riskhändelse. För närvarande kan har de flesta riskhändelser identifierats offline i en efterbearbetning åtgärd när riskhändelsen har inträffat.

I följande tabell visas hur lång tid det tar för en identifieringstyp visas i en relaterad rapport:

| Identifieringstyp | Rapporteringstiden |
| --- | --- |
| Realtid | 5 – 10 minuter |
| Offline | 2-4 timmar |


För de typer av riskhändelser Azure Active Directory identifierar, är identifieringstyper:

| Typ av riskhändelse | Identifieringstyp |
| :-- | --- | 
| [Användare med läckta autentiseringsuppgifter](#leaked-credentials) | Offline |
| [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) | Realtid |
| [Omöjlig resa till ovanliga platser](#impossible-travel-to-atypical-locations) | Offline |
| [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) | Realtid |
| [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) | Offline |
| [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Risknivå

Egenskapen risk nivå för en riskhändelse är en indikator (hög, medel eller låg) för allvarlighetsgrad och förtroendet hos en riskhändelse. Den här egenskapen hjälper dig att prioritera vilka åtgärder du måste utföra. 

Allvarlighetsgraden för riskhändelsen representerar styrkan hos signalen som en ge säkrare prognoser om identitet har komprometterats. Du kan lita på är en indikator för risken att falska positiva identifieringar. 

Exempel: 

* **Hög**: hög exakthet och hög allvarlighetsgrad riskhändelsen. Dessa händelser är starkt indikatorer som användarens identitet har komprometterats och alla användarkonton som påverkas bör åtgärdas omedelbart.

* **Medel**: Hög allvarlighetsgrad, men med lägre förtroende riskhändelse eller vice versa. Dessa händelser är potentiellt riskabla och alla användarkonton som påverkas bör åtgärdas.

* **Låg**: låg förtroende och låg allvarlighetsgrad riskhändelsen. Den här händelsen kan inte kräva en omedelbar åtgärd, men när de kombineras med andra riskhändelser som kan ge en stark indikation på att identiteten har komprometterats.

![Risknivå](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

Läcka ut autentiseringsuppgifter riskhändelser klassificeras som en **hög**eftersom de innehåller en tydlig indikation på att användarnamn och lösenord är tillgängliga för en angripare.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Risknivå för denna typ av riskhändelse är **medel** eftersom en anonym IP-adress inte är en stark indikation på en konto-kompromiss. Vi rekommenderar att du omedelbart kontakta användaren och kontrollera om de använder anonyma IP-adresser.


### <a name="impossible-travel-to-atypical-locations"></a>Omöjliga resor till ovanliga platser

Omöjlig resa är vanligtvis en god indikator som en hackare kunde logga in. Falskpositiva resultat kan dock uppstå när en användare åker med hjälp av en ny enhet eller en VPN-anslutning som vanligtvis inte används av andra användare i organisationen. En annan källa till falskpositiva resultat är program som felaktigt skicka server IP-adresser som klienten IP-adresser, som kan ge utseende över inloggningar ägde rum från datacentret där programmet är backend-server är värd för (det är ofta Microsofts datacenter som kan ge utseende över inloggningar äger rum från Microsoft ägs IP-adresser). Till följd av de här falskpositiva resultat, för den här riskhändelsen är **medel**.

> [!TIP]
> Du kan minska mängden rapporterade falskpositiva resultat för den här riskhändelsetyp genom att konfigurera [namngivna platser](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logga in från okända platser

Okända platser kan ge en stark indikation på att en angripare ska kunna använda en stulen identitet. Falskpositiva resultat kan uppstå när en användare åker, testa en ny enhet eller använder en ny VPN-anslutning. Till följd av dessa falska positiva identifieringar för den här händelsetypen är **medel**.

### <a name="sign-ins-from-infected-devices"></a>Inloggningar från angripna enheter

Den här riskhändelsen identifierar IP-adresser, inte användarenheter. Om flera enheter som är bakom en enda IP-adress och endast vissa är styrs av ett bot-nätverk, inloggningar från andra enheter min utlösare den här händelsen i onödan, vilket är orsaken till att klassificera denna riskhändelse som **låg**.  

Vi rekommenderar att du kontakta användaren och genomsöka alla användarens enheter. Det går också att en användares personliga enhet har infekterats eller som tidigare nämnts som någon annan har använt en infekterad enhet från samma IP-adress som användaren. Infekterade enheter är ofta smittade av skadlig kod som ännu inte identifierats av antivirusprogram och kan också vara som felaktiga användaren vanor som kan ha orsakat enheten infekteras.

Mer information om hur du adressen infekteras av skadlig kod finns i den [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inloggningar från IP-adresser med misstänkt aktivitet

Vi rekommenderar att du kontaktar användaren för att kontrollera om de faktiskt loggat in från en IP-adress som har markerats som misstänkt. För den här händelsetypen är ”**medel**” eftersom flera enheter kanske bakom samma IP-adress, medan endast vissa vara ansvariga för den misstänkta aktiviteten. 


 
## <a name="next-steps"></a>Nästa steg

Riskhändelser är grunden för att skydda din Azure AD-identiteter. Azure AD kan för närvarande identifiera sex riskhändelser: 


| Typ av riskhändelse | Risknivå | Identifieringstyp |
| :-- | --- | --- |
| [Användare med läckta autentiseringsuppgifter](#leaked-credentials) | Hög | Offline |
| [Inloggningar från anonyma IP-adresser](#sign-ins-from-anonymous-ip-addresses) | Medel | Realtid |
| [Omöjlig resa till ovanliga platser](#impossible-travel-to-atypical-locations) | Medel | Offline |
| [Inloggningar från okända platser](#sign-in-from-unfamiliar-locations) | Medel | Realtid |
| [Inloggningar från angripna enheter](#sign-ins-from-infected-devices) | Låg | Offline |
| [Inloggningar från IP-adresser med misstänkt aktivitet](#sign-ins-from-ip-addresses-with-suspicious-activity) | Medel | Offline|

Var hittar du de riskhändelser som har identifierats i din miljö?
Det finns två platser där du kan granska rapporterade riskhändelser:

 - **Azure AD-rapportering** -riskhändelser är en del av Azure AD-säkerhetsgrupper rapporter. Mer information finns i den [användare i farozonen](concept-user-at-risk.md) och [säkerhetsrapporten för riskfyllda inloggningar](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** -riskhändelser är också en del av [Azure Active Directory Identity Protection's](../active-directory-identityprotection.md) rapporteringsfunktioner.
    

När identifieringen av riskhändelser redan representerar en viktig aspekt av att skydda dina identiteter, har också möjlighet att antingen manuellt åtgärda dem eller även implementera automatiska svar genom att konfigurera principer för villkorlig åtkomst. Mer information finns i [Azure Active Directory Identity Protection's](../active-directory-identityprotection.md).
 
