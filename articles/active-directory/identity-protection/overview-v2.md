---
title: Vad är Azure Active Directory Identity Protection (uppdaterat)? | Microsoft Docs
description: Vad är Azure Active Directory Identity Protection (uppdaterat)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5d82f665cc837e5dbd2d7efbf16c7ae94e1b2b7
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333910"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Vad är Azure Active Directory Identity Protection (uppdaterat)?

Identitets skydds upplevelsen har uppdaterats för att bättre skydda organisationens identiteter. Den här uppdaterade upplevelsen ger:

- Omdesignad administratörs upplevelse som kretsar kring den risk som är mest relevant för dig ¬-risk och inloggnings risk
- Kraftfulla undersöknings funktioner med stöd för filtrering, sortering och smart hämtning
- Förbättrad beräkning av användar risker för att hjälpa dig att prioritera dina ansträngningar mot de användare som förmodligen är utsatta för att äventyras
- Nytt API-stöd för att ge programmatisk åtkomst till risk data
- Förenklad administratörs feedback som gör att du kan skydda dina användare direkt
- Ny övervakad maskin inlärning för att förbättra noggrannheten i riskbedömningar

Säkerhet är viktigt för organisationer idag. De flesta säkerhets överträdelser sker när angripare får åtkomst till en miljö genom att stjäla en användares identitet. Under åren har angriparen blivit alltmer effektiv i att utnyttja överträdelser från tredje part och använda avancerade phishing-attacker. Så snart angripare får till gång till till och med privilegierade användar konton, är det relativt enkelt att få åtkomst till viktiga företags resurser genom lateral förflyttning. 

För att svara på dessa hot ger Azure AD Identity Protection dig möjlighet att: 

- Förhindra att komprometterade identiteter missbrukas proaktivt 
- Minska risken automatiskt när misstänkt aktivitet identifieras 
- Undersök riskfyllda användare och inloggningar för att lösa potentiella sårbarheter  
- Bli aviserad när en användares risk når ett visst tröskelvärde 

Azure AD Identity Protection är en funktion i Azure Active Directory Premium P2 som gör att du kan konfigurera principer för att automatiskt svara när en användares identitet komprometteras eller när någon annan än konto ägaren försöker logga in med sina Autentiseringsidentitet. Dessa principer, förutom andra andra villkorliga åtkomst kontroller som tillhandahålls av Azure AD, kan antingen automatiskt blockera åtkomst eller initiera åtgärder för att lösa problemet, till exempel återställning av lösen ord eller Multi-Factor Authentication. Identitets skydd tillhandahåller dessutom övervaknings-och rapporterings funktioner för att få djupare insikt i risker och potentiella kompromisser i din organisation. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Riskhändelser

Azure AD Identity Protection identifierar följande risk händelser: 

| Typ av riskhändelse | Beskrivning | Identifieringstyp |
| --- | --- | --- |
| Ovanlig resa | Logga in från en ovanlig-plats baserat på användarens senaste inloggningar. | Offline |
| Anonym IP-adress | Logga in från en anonym IP-adress (till exempel: Tor webbläsare, Anonymizer VPN-nätverk). | Realtid |
| Obekanta inloggningsegenskaper | Logga in med egenskaper som vi inte har sett nyligen för den angivna användaren. | Realtid |
| IP-adress länkad till skadlig kod | Logga in från en länkad IP-adress med skadlig kod | Offline |
| Läckta autentiseringsuppgifter | Den här riskhändelsen risk anger att användarens giltiga autentiseringsuppgifter har avslöjats | Offline |

## <a name="types-of-risk"></a>Typer av risker 

Identitets skydd baseras på två typer av risker:

- Inloggningsrisk
- Användarrisk

### <a name="sign-in-risk"></a>Inloggningsrisk

En inloggnings risk representerar sannolikheten att en begäran om autentisering inte har behörighet av identitets ägaren.

Det finns två utvärderingar av inloggnings risken: 

- **Inloggnings risk (real tid)** – inloggnings risken (real tid) baseras på alla real tids identifieringar som utlöses under bearbetningen av inloggningen.  
- **Inloggnings risk (agg regering)** – inloggnings risken (aggregatet) är den totala risken för en inloggning. Den beräknas av en maskin inlärnings modell som beaktar:
   - Real tids identifieringar (beskrivs ovan)
   - Offline-identifiering (den brand efter att inloggningen har ägt rum) 
   - Alla andra funktioner i inloggningen

### <a name="user-risk"></a>Användarrisk

En användar risk representerar sannolikheten för att en specifik identitet komprometteras. 

Användar risken beräknas genom att ta hänsyn till alla risker som är associerade med användaren:

- Alla riskfyllda inloggningar
- Alla risk händelser som inte är länkade till en inloggning
- Den aktuella användar risken
- Eventuella risk reparationer eller avstängnings åtgärder som utförs på användaren fram till datumet

## <a name="how-identity-protection-detects-risk"></a>Så här identifierar identitets skydd risk  

Azure AD använder Machine Learning för att identifiera avvikelser och misstänkt aktivitet, med hjälp av båda signaler som upptäckts i real tid under inloggningar samt för icke-real tids signaler relaterade till användare och deras inloggnings aktiviteter. Med hjälp av dessa data beräknar identitets skyddet en real tids inloggnings risk varje gången en användare autentiseras, samt att fastställa en övergripande användar risk nivå för varje användare. Med Identity Protection kan du automatiskt vidta åtgärder för dessa risk identifieringar genom att konfigurera användar risker för identitets skydd och inloggnings risk principer.  

För att förstå hur identitets skydd identifierar risker finns det två viktiga begrepp: användar risk och inloggnings risk. Inloggnings risken visar sannolikheten för att en begäran inte auktoriseras av identitets ägaren. Det finns två typer av inloggnings risker: real tids och total. Inloggnings risk i real tid identifieras vid tidpunkten för det tilldelade inloggnings försöket (till exempel inloggningar från anonyma IP-adresser). Total inloggnings risk är summan av identifierade risker i real tid samt eventuella efterföljande risk händelser som inte är i real tid som är associerade med användarens inloggningar (t. ex. omöjlig resa). Användar risken återspeglar den övergripande sannolikheten att en felaktig aktör har komprometterat en specifik identitet. Användar risken innehåller alla risk aktiviteter för en specifik användare, inklusive:

- Inloggnings risk i real tid
- Efterföljande inloggnings risker
- Riskfyllda användar identifieringar.   

 ![Flöde](./media/overview-v2/01.png)

Bas flödet för identifiering och svar för identitets skydds risker för ett angivet inloggnings objekt sammanfattas i bilden ovan.  

## <a name="common-scenarios"></a>Vanliga scenarier 

Nu ska vi titta på exemplet på en anställd på contoso. 

1. En anställd försöker logga in på Exchange Online från Tor webbläsare. Vid tidpunkten för inloggning identifierar Azure AD real tids risk händelser. 
2. Azure AD känner av att den anställda loggar in från en anonym IP-adress, vilket utlöser en medelhög inloggnings risk nivå. 
3. Medarbetaren ifrågasätts av en MFA-prompt, eftersom Contosos IT-administratör har konfigurerat den villkorliga åtkomst principen för inloggnings risker för identitets skydd. Principen kräver MFA för en inloggnings risk på medel eller högre nivå. 
4. Medarbetaren skickar MFA-prompten och använder Exchange Online, och deras användar risk nivå ändras inte. 

Vad hände i bakgrunden? Inloggnings försöket från Tor webbläsare utlöste en inloggnings risk i real tid i Azure AD för anonym IP-adress. När Azure AD bearbetade begäran tillämpades den inloggnings risk princip som kon figurer ATS i identitets skydd eftersom den anställdas inloggnings risk nivå uppfyllde tröskelvärdet (medel). Eftersom den anställda tidigare hade registrerats för MFA kunde de svara på och skicka MFA-utmaningen. Deras förmåga att lyckas klara MFA-utmaningen som har signaler ATS till Azure AD, att de förmodligen var legitim identitets ägare och att deras användar risk nivå inte ökar. 

Men vad händer om det inte gick att logga in på en anställd? 

1. En skadlig aktör med medarbetarens autentiseringsuppgifter försöker logga in på sitt Exchange Online-konto från Tor webbläsare, eftersom de försöker dölja sin IP-adress. 
2. Azure AD upptäcker att inloggnings försöket kommer från en anonym IP-adress, vilket utlöser en real tids inloggnings risk. 
3. Den skadliga aktören ifrågasätts av en MFA-prompt eftersom Contosos IT-administratör har konfigurerat den villkorliga åtkomst principen för identitets skydd som kräver MFA när inloggnings risken är medel eller högre. 
4. Den skadliga aktören Miss lyckas MFA-utmaningen och kan inte komma åt medarbetares Exchange Online-konto. 
5. Den misslyckade MFA-prompten utlöste en risk händelse som skulle registreras, vilket höjer användar risken för framtida inloggningar. 

Nu när en skadlig aktör försökte få åtkomst till Sarah-kontot kan vi se vad som händer nästa gång medarbetaren försöker logga in. 

1. Medarbetaren försöker logga in på Exchange Online från Outlook. Vid tidpunkten för inloggning identifierar Azure AD händelser i real tid och eventuella tidigare användar risker. 
2. Azure AD identifierar inte någon real tids inloggnings risk, men identifierar hög användar risk på grund av den tidigare riskfyllda aktiviteten i föregående scenarier.  
3. Medarbetaren angrips av en uppmaning om lösen ords återställning, eftersom Contosos IT-administratör har konfigurerat användar risk principen för identitets skydd som kräver lösen ords ändring när en användare med hög risk loggar in. 
4. Eftersom medarbetaren har registrerats för SSPR och MFA har de återställt sitt lösen ord. 
5. Genom att återställa sitt lösen ord komprometteras den anställdas autentiseringsuppgifter inte längre och deras identitet återgår till ett säkert tillstånd. 
6. Den anställdas tidigare risk händelser löses och deras användar risk nivå återställs automatiskt som ett svar för att minska risken för att autentiseringsuppgifterna skadas. 

## <a name="how-do-i-configure-identity-protection"></a>Hur gör jag för att konfigurerar du identitets skydd? 

För att komma igång med Identity Protection måste du först konfigurera en användar risk princip och en princip för inloggnings risk. När dessa principer har kon figurer ATS och tillämpats på en test grupp kan du simulera risk händelser för att förstå hur identitets skyddet kommer att svara i din miljö. Följande snabb start guider ger en genom gång av hur du konfigurerar de tidigare principerna och testar i din miljö. 

Identitets skydd stöder tre roller i Azure AD för att balansera hanterings aktiviteterna kring distributionen: 

| Role | Kan göra | Kan inte göra |
| --- | --- | --- |
| Global administratör | Fullständig åtkomst till identitets skydd, inbyggt identitets skydd | |
| Säkerhetsadministratör | Fullständig åtkomst till identitets skydd | Återställa identitets skydd, återställa lösen ord för en användare |
| Säkerhetsläsare | Skrivskyddad åtkomst till identitets skydd | Identifiera skydd av identiteter, reparera användare, konfigurera principer, återställa lösen ord| 

Mer information finns i [tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
 
## <a name="licensing"></a>Licenser

>[!NOTE]
> Under den allmänt tillgängliga för hands versionen av identitets skydd (uppdateras), kommer endast Azure AD Premium P2-kunder att ha åtkomst till rapporten riskfyllda användare och riskfyllda inloggningar.

| Funktion | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/kostnads fri |
| --- | --- | --- | --- |
| Riskprincip för användare | Ja | Nej | Nej |
| Riskprincip för inloggning | Ja | Nej | Nej |
| Rapport om riskanvändare | Fullständig åtkomst | Begränsad information | Begränsad information |
| Rapport över riskfyllda inloggningar | Fullständig åtkomst | Begränsad information | Begränsad information |
| MFA-registreringsprincip | Ja | Nej | Nej |

## <a name="next-steps"></a>Nästa steg 

Information om hur du kommer igång med identitets skydd finns i [Konfigurera en princip för inloggnings risker](quickstart-sign-in-risk-policy.md). 
