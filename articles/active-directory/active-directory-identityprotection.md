---
title: Azure Active Directory identitetsskydd | Microsoft Docs
description: "Lär dig hur Azure AD Identity Protection gör att du kan begränsa möjligheten för en angripare som utnyttjar en komprometterad identitet eller en enhet och att skydda en identitet eller en enhet som har tidigare eller misstänks vara hotad."
services: active-directory
keywords: "Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: d9a83c1f1adb0a3dedaf7f8f0665de8bb809c4e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection"></a>Identitetsskydd för Azure Active Directory

Azure Active Directory Identity Protection är en funktion i Azure AD Premium P2-version som gör det möjligt att:

- Identifiera potentiella problem som påverkar din organisations identiteter

- Konfigurera automatiska svar på identifierade misstänkta åtgärder som är relaterade till din organisations identiteter  

- Undersöka misstänkt incidenter och vidta lämpliga åtgärder för att lösa dem.   


## <a name="getting-started"></a>Komma igång

Microsoft innehåller skyddade molnbaserade identiteter för mer än en tio åren. Med Azure Active Directory Identity Protection kan i din miljö, du använda samma skyddssystem som Microsoft använder för att skydda identiteter.

Det stora flertalet av säkerhetsintrång sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Angripare har blivit allt mer effektivt utnyttja från tredje part överträdelser och använda avancerade nätfiskeattacker åren har. När en angripare får åtkomst till även låg Privilegierade konton, är det relativt enkelt att få åtkomst till viktiga företagets resurser via lateral förflyttning.

Till följd av detta måste du:

- Skydda alla identiteter oavsett deras behörighetsnivå

- Proaktivt förhindra avslöjade identiteter som missbrukat

Identifiering av avslöjade identiteter finns ingen enkel aktivitet. Azure Active Directory använder anpassningsbar maskininlärningsalgoritmer och heuristik för att identifiera avvikelser och misstänkta incidenter som indikerar potentiellt komprometteras identiteter. Med dessa data kan genererar identitetsskydd rapporter och aviseringar som hjälper dig att utvärdera de identifierade problem och vidta lämplig lösning eller reparationsåtgärder.

Azure Active Directory Identity Protection är mer än en övervakning och rapportering av verktyget. Du kan konfigurera risk-baserade principer som svarar på identifierade problem automatiskt när en angiven risknivå har uppnåtts för att skydda din organisations identiteter. Dessa principer, utöver andra kontroller för villkorlig åtkomst som tillhandahålls av Azure Active Directory och EMS, kan automatiskt blockera eller initiera anpassningsbar reparationsåtgärder inklusive lösenordsåterställning och multifaktorautentisering tvingande.


#### <a name="identity-protection-capabilities"></a>Identitetsfunktionerna

**Identifiera säkerhetsrisker och riskfyllda konton:**  

* Att tillhandahålla anpassade rekommendationer för att förbättra övergripande säkerhetstillståndet genom att markera säkerhetsrisker
* Beräkning av risknivåer för inloggning
* Beräkning av risknivåer för användaren


**Undersöka riskhändelser:**

* Skicka meddelanden för riskhändelser
* Undersöka riskhändelser med hjälp av relevanta och sammanhangsberoende information
* Tillhandahåller grundläggande arbetsflöden för att spåra undersökningar
* Enkel åtkomst till åtgärder, till exempel återställning av lösenord

**Principer för risk-baserad villkorlig åtkomst:**

* Princip för att minska riskfyllda inloggningar genom att blockera inloggningar eller att kräva multifaktorautentisering utmaningar
* Princip för att blockera eller säker riskfyllda användarkonton
* Princip för att kräva att användarna registrera sig för multifaktorautentisering



## <a name="identity-protection-roles"></a>Identity Protection roller

För att belastningsutjämna hanteringsaktiviteter runt implementeringen Identity Protection kan du tilldela flera roller. Azure AD Identity Protection stöder 3 directory roller:

| Roll                         | Kan göra                          | Det går inte att göra
| :--                          | ---                                |  ---   |
| Global administratör         | Fullständig åtkomst till identitetsskydd, publicera identitetsskydd| |
| Säkerhetsadministratör       | Fullständig åtkomst till Identity Protection | Publicera identitetsskydd, återställa lösenord för en användare |
| Säkerhetsläsare              | Skrivskyddad åtkomst till Identity Protection | Publicera identitetsskydd, remidiate användare konfigurera principer, återställa lösenord |




Mer information finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Detection (Identifiering)

### <a name="vulnerabilities"></a>Sårbarheter

Azure Active Directory-identitetsskydd analyser konfigurationen och identifierar säkerhetsrisker som kan påverka din användaridentitet. Mer information finns i [sårbarheter som identifieras av Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Riskhändelser

Azure Active Directory använder anpassningsbar maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till din användaridentitet. Systemet skapar en post för varje upptäckt misstänkt åtgärd. Dessa poster kallas även riskhändelser.  
Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Undersökning
Din resa via Identity Protection startas normalt med identitetsskydd instrumentpanelen.

![Reparation](./media/active-directory-identityprotection/1000.png "reparation")

Instrumentpanelen ger dig tillgång till:

* Rapporter som **användare som har flaggats för risk**, **riskerar händelser** och **säkerhetsrisker**
* Inställningar, till exempel konfigurationen av din **säkerhetsprinciper**, **meddelanden** och **multifaktorautentisering registrering**

Det är vanligtvis utgångspunkten för undersökningar, som du kan granska aktiviteter, loggar och annan relevant information som rör en händelse för risk att bestämma om reparation eller minimering steg är nödvändiga, och hur identiteten var komprometteras och förstå hur avslöjade identiteten användes.

Du kan koppla dina undersökningen aktiviteter till den [meddelanden](active-directory-identityprotection-notifications.md) Azure Active Directory Protection skickar per e-post.

Följande avsnitt kan du med mer information och de steg som är relaterade till en undersökning.  


## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

Azure Active Directory identifierar [riskerar händelsetyper](active-directory-reporting-risk-events.md#risk-event-types) i realtid och offline. Varje händelse för risker som har identifierats för en inloggning för en användare bidrar till ett logiskt koncept som kallas riskfyllda inloggning. Är en indikator för en inloggning försök som inte kanske har utförts av legitima ägaren till ett användarkonto riskfyllda loggar in.


### <a name="sign-in-risk-level"></a>Risknivå för inloggning

Logga in risknivå är en indikation (hög, medel eller låg) på sannolikheten att ett försök att logga in inte utfördes av legitima ägaren till ett användarkonto.

### <a name="mitigating-sign-in-risk-events"></a>Minimera riskhändelser för inloggning

En lösning är en åtgärd för att begränsa möjligheten för en angripare som utnyttjar en komprometterad identitet eller en enhet utan att återställa identitet eller enhet till ett säkert tillstånd. En lösning kan inte matchas tidigare inloggning riskhändelser som associeras med identiteten eller enhet.

Du kan konfigurera inloggning risk säkerhet policicies för att minimera riskfyllda inloggningar automatiskt. Med dessa principer kan du risknivån för användaren eller logga in att blockera riskfyllda inloggningar eller kräver att användaren utför Multi-Factor authentication. Dessa åtgärder kan hindra en angripare från att en stulen identitet för att orsaka skada och ge lite tid att skydda identitet.

### <a name="sign-in-risk-security-policy"></a>Logga in risk säkerhetsprincip
Logga in riskprincipen är en princip för villkorlig åtkomst som utvärderar risken för en specifik inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

![Logga in riskprincipen](./media/active-directory-identityprotection/1014.png "inloggning riskprincipen")

Azure AD Identity Protection hjälper dig att hantera minskning av riskfyllda inloggningar genom att du kan:

* Ange de användare och grupper som principen gäller för:

    ![Logga in riskprincipen](./media/active-directory-identityprotection/1015.png "inloggning riskprincipen")
* Ange inloggning risk nivån tröskelvärdet (låg, medel eller hög) som utlöser principen:

    ![Logga in riskprincipen](./media/active-directory-identityprotection/1016.png "inloggning riskprincipen")
* Ange kontrollerna som tillämpas när principen utlöser:  

    ![Logga in riskprincipen](./media/active-directory-identityprotection/1017.png "inloggning riskprincipen")
* Växla tillståndet för principen:

    ![MFA-registrering](./media/active-directory-identityprotection/403.png "MFA-registrering")
* Granska och utvärdera effekten av en ändring innan du aktiverar det:

    ![Logga in riskprincipen](./media/active-directory-identityprotection/1018.png "inloggning riskprincipen")

#### <a name="what-you-need-to-know"></a>Vad du behöver veta
Du kan konfigurera en säkerhetsprincip för inloggning risk för att kräva multifaktorautentisering:

![Logga in riskprincipen](./media/active-directory-identityprotection/1017.png "inloggning riskprincipen")

Men av säkerhetsskäl har fungerar den här inställningen bara för användare som redan har registrerats för multifaktorautentisering. Om du uppfyller villkoret för att kräva multifaktorautentisering för en användare som ännu inte har registrerats för multifaktorautentisering, är användaren blockerad.

Som bästa praxis, om du vill kräva Multi-Factor authentication för riskfyllda inloggningar, bör du:

1. Aktivera den [multifaktorautentisering registrering princip](#multi-factor-authentication-registration-policy) för användare som påverkas.
2. Kräv att berörda användare att logga in i en icke-riskfyllda session att utföra en MFA-registrering

Gör så här ser du till att multifaktorautentisering krävs för riskfyllda loggar in.

#### <a name="best-practices"></a>Bästa praxis
Om du väljer en **hög** tröskelvärdet minskar antalet gånger som en princip utlöses och minimera de negativa effekterna för användarna.  

Men det omfattar inte **låg** och **medel** inloggningar som flaggats för risk från principen som inte kan medföra att en angripare från att en komprometterad identitet.

När du ställer in principen.

* Undanta användare som inte / kan inte ha multifaktorautentisering
* Undanta användare i språk om att aktivera principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)
* Undanta användare som kommer att generera en mängd FALSKT positiva (utvecklare, säkerhetsanalytiker)
* Använd en **hög** tröskelvärde under inledande skala, eller om du måste minska utmaningar som ses av användare.
* Använd en **låg** tröskelvärde om din organisation kräver högre säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att göra en avvägning mellan användbarhet och säkerhet.

Logga in riskprincipen är:

* Tillämpas på alla webbläsartrafik och inloggningar som använder modern autentisering.
* Tillämpas inte på program som använder äldre säkerhetsprotokoll genom att inaktivera WS-Trust-slutpunkt på den federerade IDP som AD FS.

Den **riskhändelser** i Identity Protection-konsolen visar alla händelser:

* Den här principen har tillämpats på
* Du kan granska aktivitetens och avgöra om åtgärden har lämplig

En översikt över den relaterade användarupplevelsen finns:

* [Återställning för riskfyllda inloggning](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Riskfyllda inloggning blockeras](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Logga in som inträffar med Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Att öppna dialogrutan relaterade configuration**:

- På den **Azure AD Identity Protection** blad i den **konfigurera** klickar du på **inloggning riskprincipen**.

    ![Användarprincip ridk](./media/active-directory-identityprotection/1014.png "ridk användarprincip")



## <a name="users-flagged-for-risk"></a>Användare som har flaggats för risk

Alla aktiva [riskerar händelser](active-directory-identity-protection-risk-events.md) som identifierades av Azure Active Directory för en användare som bidrar till ett logiskt koncept som kallas användaren risk. En användare som har flaggats för risk är en indikator för ett konto som kan ha drabbats.

![Användare som har flaggats för risk](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Risknivå för användaren

Risknivå för en användare är en indikation (hög, medel eller låg) på sannolikheten att användarens identitet har komprometterats. Den beräknas baserat på riskhändelser för användare som är kopplade till en användarens identitet.

Status för en risk händelse är antingen **Active** eller **stängd**. Endast riskerar händelser som är **Active** bidra till användaren risk nivån beräkningen.

Risknivå användaren beräknas med hjälp av följande indata:

* Aktiva riskhändelser påverka användaren
* Risknivå för dessa händelser
* Om eventuella åtgärder har utförts

![Användaren risker](./media/active-directory-identityprotection/1031.png "användaren risker")

Du kan använda risknivåer för användare för att skapa principer för villkorlig åtkomst som blockerar riskfyllda användare från att logga in eller tvinga dem att ändra sina lösenord på ett säkert sätt.

### <a name="closing-risk-events-manually"></a>Stänger riskhändelser manuellt

I de flesta fall ska vi ta åtgärder, till exempel ett säkert lösenord för att automatiskt stänga riskhändelser. Men kanske det inte alltid är möjligt.  
Detta gäller, till exempel när:

* En användare med aktiva riskhändelser har tagits bort
* En undersökningen visar att en händelse rapporteras risk utför av behöriga användare

Eftersom riskhändelser som är **Active** bidra till den användaren vid beräkningen måste du kanske att sänka en risknivå manuellt genom att stänga riskhändelser manuellt.  
Under undersökningen, kan du vidta någon av dessa åtgärder för att ändra status för en risk händelse:

![Åtgärder](./media/active-directory-identityprotection/34.png "åtgärder")

* **Lös** - om när du undersöker en risk händelse du använde en lämplig Reparationsåtgärd utanför identitetsskydd och du tror att risk händelsen ska betraktas som stängd Markera händelsen som löst. Löst händelser ska ange händelsen risk status är stängd och händelsen risk kommer inte längre att bidra till användaren risk.
* **Markera som falska positiva** – i vissa fall kan du kan undersöka en händelse för risk och identifiera att det var felaktigt flaggas som en riskfyllda. Du kan minska antalet sådana händelser genom att markera händelsen risk som falska positiva. Detta hjälper de maskininlärningsalgoritmer för att förbättra klassificeringen av liknande händelser i framtiden. Status för falska positiva händelser är att **stängd** och kommer inte längre att bidra till användaren risk.
* **Ignorera** - om du inte har vidtagit några Reparationsåtgärd, men vill händelsen risken att tas bort från listan över aktiva, kan du markera en händelse för risk Ignorera och händelsestatus kommer att stängas. Ignorerade händelser bidrar inte till användaren risk. Det här alternativet bör endast användas under ovanliga omständigheter.
* **Återaktivera** -riskerar händelser som har stängts manuellt (genom att välja **lösa**, **falsklarm**, eller **Ignorera**) kan återaktiveras, ställa in händelsen tillbaka till **Active**. Återaktiverade riskhändelser bidra till användaren risk nivån beräkningen. Riskhändelser stängd genom reparation (t.ex. en säker lösenordsåterställning) kan inte aktiveras igen.

**Att öppna dialogrutan relaterade configuration**:

1. På den **Azure AD Identity Protection** bladet under **Undersök**, klickar du på **riskerar händelser**.

    ![Manuell lösenordsåterställning](./media/active-directory-identityprotection/1002.png "manuell lösenordsåterställning")
2. I den **riskerar händelser** klickar du på en risk.

    ![Manuell lösenordsåterställning](./media/active-directory-identityprotection/1003.png "manuell lösenordsåterställning")
3. Högerklicka på en användare på bladet risk.

    ![Manuell lösenordsåterställning](./media/active-directory-identityprotection/1004.png "manuell lösenordsåterställning")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Stänger alla riskhändelser för en användare manuellt
I stället för att stänga manuellt riskhändelser för en användare individuellt, ger Azure Active Directory-identitetsskydd dig en metod för att stänga alla händelser för en användare med ett enda klick.

![Åtgärder](./media/active-directory-identityprotection/2222.png "åtgärder")

När du klickar på **stänga alla händelser**, alla händelser har stängts och den berörda användaren inte längre är i fara.

### <a name="remediating-user-risk-events"></a>Hälsostatus riskhändelser för användaren

En är en åtgärd för att skydda en identitet eller en enhet som har tidigare eller misstänks vara hotad. En Reparationsåtgärd återställer identitet eller enhet till ett säkert tillstånd och löser tidigare riskhändelser som associeras med identiteten eller enhet.

Om du vill reparera användaren riskhändelser kan du:

* Utför ett säkert lösenord för att åtgärda användaren riskhändelser manuellt
* Konfigurera en säkerhetsprincip för användaren risk för att minimera eller reparera riskhändelser användare automatiskt
* Ny avbildning infekterade enheter  

#### <a name="manual-secure-password-reset"></a>Manuell säker lösenordsåterställning
En säker lösenordsåterställning är en effektiv åtgärder för många riskhändelser och när utföra automatiskt stängs händelserna risk och beräknar risknivå för användaren. Du kan använda identitetsskydd instrumentpanelen för att starta en återställning av lösenord för en riskfyllda användare.

Dialogrutan relaterade innehåller två olika metoder för att återställa ett lösenord:

**Återställ lösenord** – Välj **användaren att återställa sina lösenord** att tillåta användaren att återställa själva om användaren har registrerats för multifaktorautentisering. Vid användarens nästa inloggning, kommer användaren krävs för att lösa en multifaktorautentisering utmaning har och måste sedan ändra lösenordet. Det här alternativet är inte tillgängligt om användarkontot inte är redan registrerad multifaktorautentisering.

**Tillfälligt lösenord** – Välj **generera ett tillfälligt lösenord** omedelbart ogiltigförklara det befintliga lösenordet och skapa ett nytt tillfälligt lösenord för användaren. Skicka ett nytt tillfälligt lösenord till en alternativ e-postadress för användaren eller användarens chef. Eftersom lösenordet är tillfälligt, uppmanas användaren att ändra lösenord vid inloggning.

![Principen](./media/active-directory-identityprotection/1005.png "princip")

**Att öppna dialogrutan relaterade configuration**:

1. På den **Azure AD Identity Protection** bladet, klickar du på **användare som har flaggats för risk**.

    ![Manuell lösenordsåterställning](./media/active-directory-identityprotection/1006.png "manuell lösenordsåterställning")
2. Välj en användare med minst en riskhändelser från listan över användare.

    ![Manuell lösenordsåterställning](./media/active-directory-identityprotection/1007.png "manuell lösenordsåterställning")
3. Klicka på bladet användare **Återställ lösenord**.

    ![Manuell lösenordsåterställning](./media/active-directory-identityprotection/1008.png "manuell lösenordsåterställning")

### <a name="user-risk-security-policy"></a>Användaren risk säkerhetsprincip
En säkerhetsprincip för användaren risk är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare och tillämpar reparation och minskning åtgärder baserat på fördefinierade villkor och regler.

![Användarprincip ridk](./media/active-directory-identityprotection/1009.png "ridk användarprincip")

Azure AD Identity Protection hjälper dig att hantera minskning och reparationen av användare som har flaggats för risken genom att du kan:

* Ange de användare och grupper som principen gäller för:

    ![Användarprincip ridk](./media/active-directory-identityprotection/1010.png "ridk användarprincip")
* Ange användaren risk nivån tröskelvärdet (låg, medel eller hög) som utlöser principen:

    ![Användarprincip ridk](./media/active-directory-identityprotection/1011.png "ridk användarprincip")
* Ange kontrollerna som tillämpas när principen utlöser:

    ![Användarprincip ridk](./media/active-directory-identityprotection/1012.png "ridk användarprincip")
* Växla tillståndet för principen:

    ![Användarprincip ridk](./media/active-directory-identityprotection/403.png "MFA-registrering")
* Granska och utvärdera effekten av en ändring innan du aktiverar det:

    ![Användarprincip ridk](./media/active-directory-identityprotection/1013.png "ridk användarprincip")

Om du väljer en **hög** tröskelvärdet minskar antalet gånger som en princip utlöses och minimera de negativa effekterna för användarna.
Men det omfattar inte **låg** och **medel** användare som har flaggats för risk från principen som inte kan skydda identiteter eller enheter som har tidigare eller misstänks vara hotad.

När du ställer in principen.

* Undanta användare som kommer att generera en mängd FALSKT positiva (utvecklare, säkerhetsanalytiker)
* Undanta användare i språk om att aktivera principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)
* Använd en **hög** tröskelvärde under inledande skala, eller om du måste minska utmaningar som ses av användare.
* Använd en **låg** tröskelvärde om din organisation kräver högre säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att göra en avvägning mellan användbarhet och säkerhet.

En översikt över den relaterade användarupplevelsen finns:

* [Komprometterat konto recovery flödet](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Komprometteras kontot blockerades flödet](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Att öppna dialogrutan relaterade configuration**:

- På den **Azure AD Identity Protection** blad i den **konfigurera** klickar du på **risk användarprincip**.

    ![Användarprincip ridk](./media/active-directory-identityprotection/1009.png "ridk användarprincip")

### <a name="mitigating-user-risk-events"></a>Minimera riskhändelser för användaren
Administratörer kan ange en säkerhetsprincip för användaren risken att blockera användare vid inloggning beroende på vilken risknivå av.

Blockera en inloggning:

* Förhindrar generering av riskhändelser för nya användare för den berörda användaren
* Gör att administratörer kan reparera riskhändelser som påverkar användarens identitet och återställa den till ett säkert tillstånd manuellt



## <a name="multi-factor-authentication-registration-policy"></a>Registreringsprincip för multifaktorautentisering
Azure Multi-Factor authentication är en metod för att verifiera vem du är som kräver mer än bara ett användarnamn och lösenord. Det ger ett andra säkerhetslager till användarinloggningar och transaktioner.  
Vi rekommenderar att du behöver Azure Multi-Factor authentication för användarinloggningar eftersom den:

* Ger stark autentisering med ett antal alternativ för enkel verifiering
* Spelar en viktig roll i att förbereda din organisation vill skydda och återställa från kontot kompromisser

![Användarprincip ridk](./media/active-directory-identityprotection/1019.png "ridk användarprincip")

Mer information finns i [vad är Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)

Azure AD Identity Protection hjälper dig att hantera lansering av multifaktorautentisering registrering genom att konfigurera en princip som gör att du kan:

* Ange de användare och grupper som principen gäller för:

    ![Principen för MFA](./media/active-directory-identityprotection/1020.png "MFA-principen")
* Ange kontrollerna som tillämpas när principen utlöser::  

    ![Principen för MFA](./media/active-directory-identityprotection/1021.png "MFA-principen")
* Växla tillståndet för principen:

    ![Principen för MFA](./media/active-directory-identityprotection/403.png "MFA-principen")
* Visa den aktuella registreringsstatusen:

    ![Principen för MFA](./media/active-directory-identityprotection/1022.png "MFA-principen")

En översikt över den relaterade användarupplevelsen finns:

* [Multifaktorautentisering registrering flödet](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Logga in som inträffar med Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Att öppna dialogrutan relaterade configuration**:

- På den **Azure AD Identity Protection** blad i den **konfigurera** klickar du på **multifaktorautentisering registrering**.

    ![Principen för MFA](./media/active-directory-identityprotection/1019.png "MFA-principen")

## <a name="next-steps"></a>Nästa steg
* [Channel 9: Azure AD och Identity visa: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Aktivera Identitetsskydd för Azure Active Directory](active-directory-identityprotection-enable.md)

* [Sårbarheter som identifieras av Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)

* [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md)

* [Azure Active Directory Identity Protection-aviseringar](active-directory-identityprotection-notifications.md)

* [Azure Active Directory-identitetsskydd playbook](active-directory-identityprotection-playbook.md)

* [Azure Active Directory Identity Protection – ordlista](active-directory-identityprotection-glossary.md)

* [Logga in som inträffar med Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Azure Active Directory Identity Protection - så att avblockera användare](active-directory-identityprotection-unblock-howto.md)

* [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
