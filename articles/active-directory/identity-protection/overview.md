---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur Azure AD Identity Protection kan du begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller en enhet och skyddar en identitet eller en enhet som har tidigare eller misstänks äventyras.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 06e3a596b60bf96319071fff68b0bf1655869559
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003805"
---
# <a name="azure-active-directory-identity-protection"></a>Identitetsskydd för Azure Active Directory

Azure Active Directory Identity Protection är en funktion i Azure AD Premium P2-versionen som hjälper dig att:

- Identifiera potentiella problem som påverkar organisationens identiteter

- Konfigurera automatiska svar till identifierade misstänkta åtgärder som är relaterade till din organisations identiteter  

- Undersöka misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem   


## <a name="getting-started"></a>Komma igång

Microsoft har skyddat molnbaserade identiteter för fler än tio. Med Azure Active Directory Identity Protection använda i din miljö kan du samma skyddssystem som Microsoft använder för att skydda identiteter.

Merparten av säkerhetsbrott sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Angripare har blivit allt mer effektivt att utnyttja från tredje part överträdelser och använda avancerade nätfiskeattacker genom åren. När en angripare får åtkomst till och med låg Privilegierade konton, är det relativt enkelt att få åtkomst till viktiga företagets resurser via lateral förflyttning.

Till följd av detta måste du:

- Skydda alla identiteter oavsett deras behörighetsnivå

- Proaktivt förhindra komprometterade identiteter som missbrukat

Identifiera komprometterade identiteter är ingen enkelt uppgift. Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera avvikelser och misstänkta incidenter som tyder på potentiellt komprometterade identiteter. Med dessa data genererar Identity Protection rapporter och aviseringar så att du kan utvärdera upptäckta problem och vidta lämpliga mitigerings- eller åtgärder.

Azure Active Directory Identity Protection är mer än en övervakning och rapportering verktyget. För att skydda din organisations identiteter kan konfigurera du riskbaserade principer som automatiskt svarar på identifierade problem när en angiven risknivå har uppnåtts. Dessa principer, utöver andra kontroller för villkorlig åtkomst som tillhandahålls av Azure Active Directory och EMS, kan automatiskt blockera eller initiera anpassningsbar åtgärder inklusive lösenordsåterställning och multifaktorautentisering tvingande.


#### <a name="identity-protection-capabilities"></a>Identity Protection-funktioner

**Identifiera säkerhetsrisker och riskfyllda konton:**  

* Att tillhandahålla anpassade rekommendationer för att förbättra övergripande säkerhetspositionen genom att markera sårbarheter
* Beräkna risknivåer för inloggning
* Beräkna risknivåer för användare


**Undersöka riskhändelser:**

* Skicka meddelanden för riskhändelser
* Undersöka riskhändelser med relevant och sammanhangsbaserad information
* Att tillhandahålla grundläggande arbetsflöden för att spåra undersökningar
* Enkel åtkomst till åtgärder som till exempel återställning av lösenord

**Principer för riskbaserad villkorlig åtkomst:**

* Princip för att minimera riskfyllda inloggningar genom att blockera inloggningar eller att kräva multifaktorautentisering utmaningar
* För att blockera eller säker riskfyllda användarkonton
* Princip som kräver att användare registrerar sig för multifaktorautentisering



## <a name="identity-protection-roles"></a>Identity Protection roller

För att belastningsutjämna hanteringsaktiviteter runt implementeringen Identity Protection kan du tilldela flera roller. Azure AD Identity Protection har stöd för 3 katalogroller:

| Roll                         | Kan göra                          | Det går inte att göra
| :--                          | ---                                |  ---   |
| Global administratör         | Fullständig åtkomst till Identity Protection, publicera Identity Protection| |
| Säkerhetsadministratör       | Fullständig åtkomst till Identity Protection | Publicera Identity Protection kan återställa lösenord för en användare |
| Säkerhetsläsare              | Skrivskyddad åtkomst till Identity Protection | Publicera Identity Protection, remidiate användare, konfigurera principer, återställa lösenord |




Mer information finns i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Detection (Identifiering)

### <a name="vulnerabilities"></a>Sårbarheter

Azure Active Directory Identity Protection analyserar din konfiguration och identifierar sårbarheter som kan påverka din användaridentitet. Mer information finns i [sårbarheter som identifieras av Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Riskhändelser

Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användares identiteter. Systemet skapar en post för varje identifierad misstänkt åtgärd. Dessa poster kallas även riskhändelser.  
Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Undersökning
Resan via Identity Protection börjar vanligtvis med Identity Protection-instrumentpanelen.

![Reparation](./media/overview/1000.png "reparation")

Instrumentpanelen ger dig åtkomst till:

* Rapporter som **användare som har flaggats för risk**, **riskhändelser** och **sårbarheter**
* Inställningar, till exempel konfigurationen av din **säkerhetsprinciper**, **meddelanden** och **registrering för multifaktorautentisering**

Det är vanligtvis din startpunkt för undersökningar, som du kan granska aktiviteter, loggar och annan relevant information som rör en riskhändelse att bestämma om reparation eller minimering steg är nödvändiga, och hur identiteten har drabbade och förstå hur komprometterade identiteten har använts.

Du kan koppla din undersökning aktiviteter till den [meddelanden](notifications.md) Azure Active Directory Protection skickar per e-postmeddelande.

I följande avsnitt får du mer information och de steg som är relaterade till en undersökning.  


## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

Azure Active Directory identifierar [riskhändelsetyper](../reports-monitoring/concept-risk-events.md#risk-event-types) i realtid och offline. Varje riskhändelsen har upptäckts för en inloggning för en användare bidrar till ett logiskt koncept som kallas riskfyllda inloggningen. En riskfylld inloggning är du en indikator för en inloggningsförsök som inte kanske har utförts av är tillförlitligt ägare för ett användarkonto.


### <a name="sign-in-risk-level"></a>Risknivå för inloggning

En nivå för inloggningsrisk är en indikation (hög, medel eller låg) på sannolikheten att en inloggningsförsök inte har utförts av är tillförlitligt ägare för ett användarkonto.

### <a name="mitigating-sign-in-risk-events"></a>Minimera riskhändelser som inloggning

En lösning är en åtgärd för att begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller en enhet utan att återställa den identitet eller enhet till säkert läge. En lösning kan inte matchas tidigare inloggningsrisk händelser som är associerade med den identitet eller enhet.

Du kan konfigurera säkerhetsprinciper för inloggningsrisk för att minimera riskfyllda inloggningar automatiskt. Med dessa principer kan du överväga att risknivån för användaren eller logga in att blockera riskfyllda inloggningar eller kräva att användaren att utföra multifaktorautentisering. De här åtgärderna kan hindra en angripare utnyttjar en stulen identitet för att skada och kan ge dig tid att skydda identitet.

### <a name="sign-in-risk-security-policy"></a>Säkerhetsprincip för inloggningsrisk
En princip för inloggningsrisk är en villkorlig åtkomstprincip som utvärderar risken för angrepp på en specifik inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

![Riskprincip för inloggning](./media/overview/1014.png "inloggning riskprincipen")

Azure AD Identity Protection hjälper dig att hantera minskning av riskfyllda inloggningar genom att:

* Ange de användare och grupper som principen gäller för:

    ![Riskprincip för inloggning](./media/overview/1015.png "inloggning riskprincipen")
* Ange inloggningsrisk nivå tröskelvärdet (låg, medel eller hög) som utlöser principen:

    ![Riskprincip för inloggning](./media/overview/1016.png "inloggning riskprincipen")
* Ange kontroller som ska tillämpas när principen utlöser:  

    ![Riskprincip för inloggning](./media/overview/1017.png "inloggning riskprincipen")
* Växla tillståndet för din princip:

    ![MFA-registrering](./media/overview/403.png "MFA-registrering")
* Granska och utvärdera effekten av en ändring innan du aktiverar det:

    ![Riskprincip för inloggning](./media/overview/1018.png "inloggning riskprincipen")

#### <a name="what-you-need-to-know"></a>Vad du behöver veta
Du kan konfigurera en säkerhetsprincip för inloggningsrisk för att kräva multifaktorautentisering:

![Riskprincip för inloggning](./media/overview/1017.png "inloggning riskprincipen")

Men av säkerhetsskäl fungerar den här inställningen bara för användare som redan har registrerats för multifaktorautentisering. Om villkoret för att kräva multifaktorautentisering uppfylls för en användare som ännu inte har registrerats för multifaktorautentisering, är användaren blockerad.

Som bästa praxis, om du vill kräva multifaktorautentisering för riskfyllda inloggningar, bör du:

1. Aktivera den [registreringsprincip för multi-Factor authentication](#multi-factor-authentication-registration-policy) för de berörda användarna.
2. Kräv att berörda användare att logga in i en icke-riskfyllda session för att utföra en MFA-registrering

Gör så här säkerställer att multifaktorautentisering krävs för en riskfylld inloggning.

#### <a name="best-practices"></a>Bästa praxis
Välja en **hög** tröskelvärdet minskar antalet gånger som en princip har utlösts och minimerar påverkan för användare.  

Men det omfattar inte **låg** och **medel** inloggningar som har flaggats för risk från principen som inte blockerar en angripare utnyttjar en komprometterad identitet.

När du ställer in principen

* Undanta användare som inte / kan inte ha multifaktorautentisering
* Exkludera användare i nationella inställningar där aktiverar principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)
* Exkludera användare som sannolikt inte kommer att skapa ett stort antal falskpositiva resultat (utvecklare, säkerhetsanalytiker)
* Använd en **hög** tröskelvärde under inledande skala ut, eller om du måste minimera utmaningar som setts av slutanvändare.
* Använd en **låg** tröskelvärdet om din organisation kräver ökad säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att få en balans mellan användbarhet och säkerhet.

Princip för inloggningsrisk är:

* Tillämpas på alla webbläsartrafik och inloggningar som använder modern autentisering.
* Inte användas av program med äldre säkerhetsprotokoll genom att inaktivera den WS-Trust-slutpunkten på federerade IDP: N, till exempel AD FS.

Den **riskhändelser** i Identity Protection-konsolen visar en lista över alla händelser:

* Den här principen har tillämpats på
* Du kan granska aktiviteten och fastställa om åtgärden var rätt eller inte

En översikt över relaterade användarupplevelsen finns:

* [Riskfylld inloggning återställning](flows.md#risky-sign-in-recovery)
* [Riskfylld inloggning blockerad](flows.md#risky-sign-in-blocked)  
* [Logga in upplevelser med Azure AD Identity Protection](flows.md)  

**Att öppna dialogrutan tillhörande konfigurationer**:

- På den **Azure AD Identity Protection** bladet i den **konfigurera** klickar du på **inloggning riskprincipen**.

    ![Princip för användarrisk](./media/overview/1014.png "riskprincip för användare")



## <a name="users-flagged-for-risk"></a>Användare som har flaggats för risk

Alla aktiva [riskhändelser](../reports-monitoring/concept-risk-events.md) som identifierades av Azure Active Directory för en användare bidra till ett logiskt koncept som kallas användarrisk. En användare som flaggats för risk är en indikator för ett användarkonto som kan ha komprometterats.

![Användare som har flaggats för risk](./media/overview/1200.png)


### <a name="user-risk-level"></a>Användarisknivå

En nivå för användarrisk är en indikation (hög, medel eller låg) på sannolikheten att användarens identitet har komprometterats. Det beräknas baserat på de riskhändelser för användaren som är associerade med en användares identitet.

Status för en riskhändelse är antingen **Active** eller **stängd**. Endast riskhändelser som är **Active** bidra till den nivå riskberäkning för användaren.

Risknivån beräknas med hjälp av följande indata:

* Aktiva riskhändelser som påverkas av användaren
* Risknivån för dessa händelser
* Om eventuella åtgärder har vidtagits

![Användaren risker](./media/overview/1031.png "användaren risker")

Du kan använda risknivåer för användare för att skapa principer för villkorlig åtkomst som blockerar riskabla användare från att logga in eller tvinga dem på ett säkert sätt ändra sina lösenord.

### <a name="closing-risk-events-manually"></a>Stänga riskhändelser manuellt

I de flesta fall ska vi ta åtgärder, till exempel ett säkert lösenord för att automatiskt stänga riskhändelser. Men kanske det inte alltid är möjligt.  
Detta gäller, till exempel när:

* En användare med aktiva riskhändelser har tagits bort
* En undersökningen visar att en rapporterade riskhändelse utför av behöriga användare

Eftersom riskhändelser som är **Active** bidra till användaren riskberäkning, du kan behöva manuellt Sänk en risknivå genom att stänga riskhändelser manuellt.  
Under loppet av undersökning, kan du vidta någon av dessa åtgärder för att ändra status för en riskhändelse:

![Åtgärder](./media/overview/34.png "åtgärder")

* **Lösa** – om när du undersöker en riskhändelse, du har gjort en lämpliga avhjälpande åtgärd utanför Identity Protection och du tror att riskhändelsen bör övervägas stängd, markera händelsen som löst. Matcha händelser som anger den riskhändelsen status till stängd och riskhändelsen kommer inte längre att bidra till användarrisk.
* **Markera som FALSKT positiva** – i vissa fall kan du undersöka en riskhändelse och identifiera att det var felaktigt som flaggats som en riskfylld. Du kan minska antalet sådana händelser genom att markera riskhändelsen som FALSKT positiva. På så sätt maskininlärningsalgoritmer för att förbättra klassificeringen av liknande händelser i framtiden. Status för falsk-positiva händelser är att **stängd** och kommer inte längre att bidra till användarrisk.
* **Ignorera** – om du inte har vidtagit några Reparationsåtgärd, men vill riskhändelsen som ska tas bort från listan över aktiva, kan du markera en riskhändelse Ignorera och händelsestatus för kommer att stängas. Ignorerade händelser bidrar inte till användarrisk. Det här alternativet bör endast användas under ovanliga omständigheter.
* **Återaktivera** -riskhändelser som stängdes manuellt (genom att välja **lösa**, **falsklarm**, eller **Ignorera**) kan återaktiveras, ställa in händelsen tillbaka till **Active**. Återaktiverade riskhändelser bidra till den nivå riskberäkning för användaren. Riskhändelser stängda genom reparation (som en säker lösenordsåterställning) kan inte aktiveras.

**Att öppna dialogrutan tillhörande konfigurationer**:

1. På den **Azure AD Identity Protection** bladet under **Undersök**, klickar du på **riskhändelser**.

    ![Återställning av lösenord för manuell](./media/overview/1002.png "manuell för lösenordsåterställning")
2. I den **riskhändelser** klickar du på en risk.

    ![Återställning av lösenord för manuell](./media/overview/1003.png "manuell för lösenordsåterställning")
3. Högerklicka på en användare på bladet risk.

    ![Återställning av lösenord för manuell](./media/overview/1004.png "manuell för lösenordsåterställning")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Stänga alla riskhändelser för en användare manuellt
I stället för att manuellt stänga riskhändelser för en användare individuellt, ger Azure Active Directory Identity Protection dig också med en metod för att Stäng alla händelser för en användare med ett enda klick.

![Åtgärder](./media/overview/2222.png "åtgärder")

När du klickar på **Ignorera alla händelser**, alla händelser är stängda och den berörda användaren inte längre är i fara.

### <a name="remediating-user-risk-events"></a>Åtgärda användarriskhändelser

En reparation är en åtgärd för att skydda en identitet eller en enhet som har tidigare eller misstänks äventyras. En Reparationsåtgärd återställer identitet eller enhet till säkert läge och löser tidigare riskhändelser som är associerade med den identitet eller enhet.

Om du vill åtgärda riskhändelser för användaren, kan du:

* Utföra ett säkert lösenord för att åtgärda användarriskhändelser manuellt
* Konfigurera en riskprincip för att minimera eller åtgärdar riskhändelser för användaren automatiskt
* Spegla angripen enhet  

#### <a name="manual-secure-password-reset"></a>Manuell säker lösenordsåterställning
En säker lösenordsåterställning är en effektiv justering av många riskhändelser och när utförs, automatiskt stängs dessa riskhändelser och beräknar om risknivån. Du kan använda Identity Protection-instrumentpanelen för att initiera en återställning av lösenord för en riskfylld användare.

Relaterade dialogrutan innehåller två olika metoder för att återställa ett lösenord:

**Återställ lösenord** – Välj **kräver att användaren återställer sitt lösenord** så att användaren kan själv återställa om användaren har registrerats för multifaktorautentisering. Vid användarens nästa inloggning, kommer användaren krävs för att lösa en multifaktorautentisering utmaning har och måste sedan ändra lösenordet. Det här alternativet är inte tillgängligt om användarkontot inte är redan registrerad multifaktorautentisering.

**Tillfälligt lösenord** – Välj **skapa ett tillfälligt lösenord** du omedelbart ogiltigförklara det befintliga lösenordet och skapar ett nytt tillfälligt lösenord för användaren. Skicka ett nytt tillfälligt lösenord till en alternativ e-postadressen för användaren eller användarens chef. Eftersom lösenordet är tillfällig, uppmanas användaren att ändra lösenordet vid inloggning.

![Principen](./media/overview/1005.png "princip")

**Att öppna dialogrutan tillhörande konfigurationer**:

1. På den **Azure AD Identity Protection** bladet klickar du på **användare som har flaggats för risk**.

    ![Återställning av lösenord för manuell](./media/overview/1006.png "manuell för lösenordsåterställning")
2. Välj en användare med minst en riskhändelser från listan över användare.

    ![Återställning av lösenord för manuell](./media/overview/1007.png "manuell för lösenordsåterställning")
3. Klicka på användarbladet **Återställ lösenord**.

    ![Återställning av lösenord för manuell](./media/overview/1008.png "manuell för lösenordsåterställning")

### <a name="user-risk-security-policy"></a>Riskprincip
En riskprincip är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare och vidtar åtgärder och minskning åtgärder baserat på fördefinierade villkor och regler.

![Princip för användarrisk](./media/overview/1009.png "riskprincip för användare")

Azure AD Identity Protection hjälper dig att hantera minskning och reparation av användare som har flaggats för risk genom att:

* Ange de användare och grupper som principen gäller för:

    ![Princip för användarrisk](./media/overview/1010.png "riskprincip för användare")
* Ange användaren risk på tröskelvärdet (låg, medel eller hög) som utlöser principen:

    ![Princip för användarrisk](./media/overview/1011.png "riskprincip för användare")
* Ange kontroller som ska tillämpas när principen utlöser:

    ![Princip för användarrisk](./media/overview/1012.png "riskprincip för användare")
* Växla tillståndet för din princip:

    ![Princip för användarrisk](./media/overview/403.png "MFA-registrering")
* Granska och utvärdera effekten av en ändring innan du aktiverar det:

    ![Princip för användarrisk](./media/overview/1013.png "riskprincip för användare")

Välja en **hög** tröskelvärdet minskar antalet gånger som en princip har utlösts och minimerar påverkan för användare.
Men det omfattar inte **låg** och **medel** användare som har flaggats för risk från principen som inte kanske att skydda identiteter eller enheter som har tidigare eller misstänks äventyras.

När du ställer in principen

* Exkludera användare som sannolikt inte kommer att skapa ett stort antal falskpositiva resultat (utvecklare, säkerhetsanalytiker)
* Exkludera användare i nationella inställningar där aktiverar principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)
* Använd en **hög** tröskelvärde under inledande skala ut, eller om du måste minimera utmaningar som setts av slutanvändare.
* Använd en **låg** tröskelvärdet om din organisation kräver ökad säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att få en balans mellan användbarhet och säkerhet.

En översikt över relaterade användarupplevelsen finns:

* [Komprometterat konto recovery flow](flows.md#compromised-account-recovery).  
* [Komprometterade kontot har spärrats flow](flows.md#compromised-account-blocked).  

**Att öppna dialogrutan tillhörande konfigurationer**:

- På den **Azure AD Identity Protection** bladet i den **konfigurera** klickar du på **användarprincip**.

    ![Princip för användarrisk](./media/overview/1009.png "riskprincip för användare")

### <a name="mitigating-user-risk-events"></a>Minimera riskhändelser för användaren
Administratörer kan ange en riskprincip för att blockera användare vid inloggning beroende på risknivån.

Blockera en inloggning:

* Förhindrar generering av nya riskhändelser för användaren för den berörda användaren
* Gör att administratörer kan manuellt åtgärdar riskhändelser som påverkar användarens identitet och återställer dem till ett säkert tillstånd



## <a name="multi-factor-authentication-registration-policy"></a>Registreringsprincip för multifaktorautentisering
Azure Multi-Factor authentication är en metod för att verifiera vem du är som kräver användning av mer än bara ett användarnamn och lösenord. Det ger ett andra säkerhetslager till användarinloggningar och transaktioner.  
Vi rekommenderar att du Kräv Azure Multi-Factor authentication för användarinloggningar eftersom den:

* Du får stark autentisering med en rad enkla verifieringsalternativ
* Spelar en viktig roll i att förbereda din organisation för att skydda och återställa från kontot kompromisser

![Princip för användarrisk](./media/overview/1019.png "riskprincip för användare")

Mer information finns i [vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

Azure AD Identity Protection hjälper dig att hantera lansering av multifaktorautentisering registrering genom att konfigurera en princip som hjälper dig att:

* Ange de användare och grupper som principen gäller för:

    ![MFA-principen](./media/overview/1020.png "MFA-principen")
* Ange kontroller som ska tillämpas när principen utlöser::  

    ![MFA-principen](./media/overview/1021.png "MFA-principen")
* Växla tillståndet för din princip:

    ![MFA-principen](./media/overview/403.png "MFA-principen")
* Visa den aktuella registreringsstatusen:

    ![MFA-principen](./media/overview/1022.png "MFA-principen")

En översikt över relaterade användarupplevelsen finns:

* [Flöde för registrering av multifaktorautentisering](flows.md#multi-factor-authentication-registration).  
* [Logga in som inträffar med Azure AD Identity Protection](flows.md).  

**Att öppna dialogrutan tillhörande konfigurationer**:

- På den **Azure AD Identity Protection** bladet i den **konfigurera** klickar du på **registrering av multifaktorautentisering**.

    ![MFA-principen](./media/overview/1019.png "MFA-principen")

## <a name="next-steps"></a>Nästa steg
* [Channel 9: Azure AD och Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Aktivera Azure Active Directory Identity Protection](enable.md)

* [Sårbarheter som identifieras av Azure Active Directory Identity Protection](vulnerabilities.md)

* [Azure Active Directory-riskhändelser](../active-directory-identity-protection-risk-events.md)

* [Azure Active Directory Identity Protection-aviseringar](notifications.md)

* [Azure Active Directory Identity Protection spelbok](playbook.md)

* [Azure Active Directory Identity Protection-ordlistan](glossary.md)

* [Logga in upplevelser med Azure AD Identity Protection](flows.md)

* [Azure Active Directory Identity Protection - så avblockera användare](howto-unblock-user.md)

* [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](graph-get-started.md)
