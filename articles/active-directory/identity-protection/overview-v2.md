---
title: Vad är Azure Active Directory Identity Protection (uppdateras)? | Microsoft Docs
description: Vad är Azure Active Directory Identity Protection (uppdateras)?
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 1e70c063095d682213239febdb1fb285665f81ff
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914058"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Vad är Azure Active Directory Identity Protection (uppdateras)?

Identity Protection-upplevelsen har uppdaterats för att bättre skydda din organisations identiteter. Den här uppdateras kan du:

- Omarbetad administratörsupplevelsen som är uppbyggd kring risken som är mest relevant för you¬ användarrisk och inloggningsrisk

- Kraftfulla undersökningar upplevelse med stöd för filtrering, sortering och smart nedladdningar

- Förbättrad användaren riskberäkning kan du prioritera dina åtgärder mot de användare som är mest sannolika äventyras

- Nya API: et som stöd för att aktivera Programmeringsåtkomst till Riskdata

- Förenklad admin feedbackprocessen som hjälper dig att skydda dina användare direkt

- Ny övervakade machine learning för att förbättra riskbedömningar



Säkerhet är ett viktigt mål för organisationer i dag. Flesta security överträdelser ta placera när angripare får tillgång till en miljö genom att stjäla en användares identitet. Angripare har blivit allt mer effektivt att utnyttja från tredje part överträdelser och använda avancerade nätfiskeattacker genom åren. När angripare får tillgång till ännu låg Privilegierade konton, är det relativt enkelt att få åtkomst till viktiga företagets resurser via lateral förflyttning. 

För att svara på dessa hot, hjälper Azure AD Identity Protection dig att: 

- Proaktivt förhindra komprometterade identiteter som missbrukat 

- Minska riskerna automatiskt när misstänkt aktivitet har identifierats 

- Undersöka riskabla användare och inloggningar på adressen potentiella säkerhetsproblem  

- Aviseras när en användares risk når ett angivet tröskelvärde 

 

Azure AD Identity Protection är en funktion i Azure Active Directory Premium P2 där du kan konfigurera principer för att automatiskt svarar när en användares identitet har komprometterats eller när någon annan än Kontoägare om du försöker logga in med sina identitet. Dessa principer, utöver andra kontroller för villkorlig åtkomst som tillhandahålls av Azure AD, antingen automatiskt blockera åtkomst eller initiera minskning åtgärder, till exempel återställning av lösenord eller multifaktorautentisering tvingande. Identity Protection innehåller dessutom övervakning och rapportering funktioner för att få bättre insikt i risk och potentiella kompromisser i din organisation. 



## <a name="risk-events"></a>Riskhändelser

Azure AD Identity Protection identifierar de följande riskhändelser: 

 

| Typ av riskhändelse | Beskrivning | Identifieringstyp |
| ---             | ---         | ---            |
| Ovanlig resa | Logga in från en ovanlig plats baserat på användarens senaste inloggningar. | Offline |
| Anonym IP-adress | Logga in från en anonym IP-adress (till exempel: Tor browser, anonymizer VPN). | Realtid |
| Obekanta inloggningsegenskaper | Logga in med egenskaper som vi inte har sett nyligen för den angivna användaren. | Realtid |
| IP-adress länkad till skadlig kod | Logga in från en skadlig kod länkad IP-adress | Offline |
| Läcka ut autentiseringsuppgifter | Den här riskhändelsen risk anger att användarens giltiga autentiseringsuppgifter har avslöjats | Offline |





## <a name="types-of-risk"></a>Typer av risker 

Identitetsskydd baseras på två typer av risker:

- Inloggningsrisk

- Användarrisk

### <a name="sign-in-risk"></a>Inloggningsrisk

En inloggningsrisk representerar sannolikheten att en viss autentiseringsbegäran inte är auktoriserad av identitet ägare.

Det finns två utvärderingar av inloggningsrisk: 

- **Inloggningsrisk (realtid)** -inloggningsrisk (realtid) baseras på alla i realtid identifieringar som utlöser under bearbetning av inloggningen.  

- **Inloggningsrisk (aggregering)** -inloggningsrisk (aggregering) är den totala risken för en inloggning. Det beräknas genom en maskininlärningsmodell som tar hänsyn till:

    - I realtid identifieringar (beskrivs ovan)
    
    - Offline identifieringar (som utlöses när inloggningen har ägt rum) 
    
    - Alla andra funktioner i inloggningen


### <a name="user-risk"></a>Användarrisk

En användarrisk representerar sannolikheten att en viss identitet har komprometterats. 

Användaren risken beräknas genom att fundera över alla risker som är associerade med användaren:

- Alla riskfyllda inloggningar
- Alla riskhändelser som inte har länkats till en inloggning
- Den aktuella användarrisk
- Alla risker reparation eller avskedande åtgärder som utförs på användaren till datum



## <a name="how-identity-protection-detects-risk"></a>Hur Identity Protection identifierar risker  

Azure AD använder maskininlärning att identifiera avvikelser och misstänkta aktiviteter, med hjälp av båda signaler som identifierats i realtid under inloggningar samt som signalerar till icke-realtid relaterade till användare och deras inloggningsaktiviteter. Identity Protection beräknar använder dessa data, ett i realtid inloggningsrisk varje gång en användare autentiseras samt fastställa en övergripande risknivån för räckvidd användare. Identity Protection kan du vidta åtgärder automatiskt för dessa risker identifieringar genom att konfigurera Identity Protection användarrisk och Inloggningsrisk principer.  

 

Det finns två viktiga begrepp för att förstå hur Identity Protection identifierar risken: användarrisk och inloggningsrisk. Inloggningsrisk beaktas sannolikheten att en viss autentiseringsbegäran inte är auktoriserad av identitet ägare. Det finns två typer av inloggning risker: i realtid och Totalt antal. I realtid inloggningsrisk identifieras vid tidpunkten för den angivna inloggningsförsök (till exempel inloggningar från anonyma IP-adresser). Totalt antal inloggningsrisk är summan av identifierade i realtid inloggning risker samt eventuell efterföljande real time riskhändelser som är associerade med användarens inloggningar (t.ex omöjlig resa). Användarrisk visar övergripande sannolikheten att en obehörig har komprometterats en viss identitet. Användarrisk innehåller alla risker aktiviteter för en viss användare, inklusive:

- I realtid inloggningsrisk
- Efterföljande inloggningsrisk
- Riskfylld användare identifieringar.   

 

 
 ![Flöde](./media/overview-v2/01.png)
 

 

Baslinje-flödet för Identity Protection riskidentifieringen och svar för alla angivna inloggning summeras i ovanstående bild.  

 

 

 

## <a name="common-scenarios"></a>Vanliga scenarier 

Nu ska vi titta på exempel på Sarah, anställd hos Contoso. 

1. Sarah försöker logga in på Exchange Online från Tor-webbläsaren. Vid tidpunkten för inloggning upptäcker Azure AD att i realtid riskhändelser. 

2. Azure AD upptäcker att Sarah loggar in från en anonym IP-adress, utlösa en inloggning Medelrisk-nivå. 

3. Sarah är på samma en MFA-prompt eftersom Contosos IT-administratören konfigurerat principen för villkorlig åtkomst inloggningsrisk Identity Protection. Principen kräver MFA för en risk för medelstora eller högre. 

4. Sarah skickar MFA fråga och får åtkomst till Exchange Online och Sarahs risknivån ändras inte. 

Vad hände med bakom kulisserna? Försök logga in från Tor webbläsaren utlöses en i realtid inloggningsrisk i Azure AD för anonyma IP-adress. Eftersom Azure AD bearbetade förfrågan, tillämpas principen inloggningsrisk som konfigurerats i Identity Protection eftersom Sarahs inloggning risknivå uppfyllt tröskelvärdet (Medium). Eftersom Sarah tidigare hade registrerats för MFA, kunde hon svara på och skicka MFA-kontrollen. Hon har möjlighet att skicka MFA-kontrollen har signalerats till Azure AD att hon var ett troligt legitima identitet ägare och hennes risknivån ökar inte. 


Men vad händer om Sarah inte var ett försök att logga in? 

1. En skadliga aktörer med autentiseringsuppgifter för Sarahs försöker logga in till Sarah's Exchange Online-konto från Tor-webbläsare eftersom de försöker att dölja sina IP-adress. 

2. Azure AD upptäcker att försök logga in från en anonym IP-adress, utlösa en i realtid inloggningsrisk. 

3. Skadliga aktören anropas av en MFA-prompt eftersom Contosos IT-administratören konfigurerat Identity Protection inloggning Risk principen för villkorlig åtkomst för att kräva MFA när inloggningsrisk är medium eller högre. 

4. Skadliga aktören misslyckas MFA-kontrollen och det går inte att komma åt Sarah's Exchange Online-konto. 

5. Misslyckade MFA prompten utlöses en riskhändelse att höja Sarahs användarrisk för framtida inloggningar. 

Nu när en skadliga aktörer försökte få åtkomst till Sarahs konto, ska vi se vad som händer vid nästa Sarah försöker logga in. 

1. Sarah försöker logga in på Exchange Online från Outlook. Vid tidpunkten för inloggning upptäcker Azure AD att i realtid riskhändelser samt eventuella tidigare användarrisk. 

2. Azure AD identifierar inte någon i realtid inloggningsrisk, men identifierar hög användarrisk på grund av den senaste riskfyllda aktiviteten i föregående scenarier.  

3. Sarah är på samma Kommandotolken för återställning av lösenord eftersom Contoso är IT-administratören har konfigurerat riskprincip för Identity Protection-användare för att kräva ändring av lösenord när en användare med hög risk som loggar in. 

4. Eftersom Sarah är registrerad för SSPR och MFA, återställer hon har sitt lösenord. 

5. Genom att återställa sina lösenord, Sarahs autentiseringsuppgifter inte längre har komprometterats och användarens identitet återgår till säkert läge. 

6. Sarahs tidigare riskhändelser har åtgärdats och hennes risknivån återställs automatiskt som ett svar till att minimera autentiseringsuppgifter kompromettering. 

## <a name="how-do-i-configure-identity-protection"></a>Hur konfigurerar jag Identity Protection? 

Om du vill komma igång med Identity Protection måste du först konfigurera en princip för användarrisk och en princip för inloggningsrisk. När dessa principer konfigureras och används till en testgrupp, kan du simulera riskhändelser för att förstå hur Identity Protection kommer att besvara i din miljö. Den nedan Snabbstart guider ger en genomgång om hur du definierar de tidigare nämnda principerna och testa i din miljö. 

 

Identity Protection stöder 3 roller i Azure AD för att balansera hanteringsaktiviteter runt distributionen: 

| Roll | Kan göra | Det går inte att göra |
| --- | --- | --- |
| Global administratör | Fullständig åtkomst till Identity Protection, publicera Identity Protection | |
| Säkerhetsadministratör | Fullständig åtkomst till Identity Protection | Publicera Identity Protection kan återställa lösenord för en användare |
| Säkerhetsläsare | Skrivskyddad åtkomst till Identity Protection | Publicera Identity Protection, åtgärda användare, konfigurera principer, återställa lösenord| 

Mer information finns i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>Licensiering

>[!NOTE]
> Allmänt tillgängliga förhandsversionen av Identity Protection (uppdateras) har endast Azure AD Premium P2-kunder åtkomst till riskabla användare rapport och rapporten över riskfyllda inloggningar.



| Funktion | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/kostnadsfritt |
| --- | --- | --- | --- |
| Riskprincip för användare | Ja | Nej | Nej |
| Riskprincip för inloggning | Ja | Nej | Nej |
| Rapport om riskanvändare | Fullständig åtkomst | Begränsad Information | Begränsad Information |
| Rapport över riskfyllda inloggningar | Fullständig åtkomst | Begränsad Information | Begränsad Information |
| MFA-registreringsprincip | Ja | Nej | Nej |







## <a name="next-steps"></a>Nästa steg 

Kom igång med Identity Protection, se [Konfigurera princip för inloggningsrisk](quickstart-sign-in-risk-policy.md). 






