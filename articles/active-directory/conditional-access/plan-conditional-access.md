---
title: Planera principer för villkorlig åtkomst i Azure Active Directory | Microsoft-dokument
description: I den här artikeln får du lära dig hur du planerar principer för villkorlig åtkomst för Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671817"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Planera distributionen för villkorlig åtkomst i Azure Active Directory

Det är viktigt att planera distributionen för villkorlig åtkomst för att du ska uppnå den åtkomststrategi som krävs för appar och resurser i organisationen. Tillbringa större delen av din tid under planeringsfasen av distributionen för att utforma de olika principer som du behöver för att bevilja eller blockera åtkomst till användarna under de villkor du väljer. I det här dokumentet beskrivs de åtgärder du bör vidta för att implementera säkra och effektiva principer för villkorlig åtkomst. Innan du börjar måste du förstå hur [villkorlig åtkomst](overview.md) fungerar och när du ska använda den.

## <a name="what-you-should-know"></a>Det här bör du känna till

Tänk på villkorlig åtkomst som ett ramverk som gör att du kan styra åtkomsten till organisationens appar och resurser i stället för en fristående funktion. Därför kräver vissa inställningar för villkorlig åtkomst att ytterligare funktioner konfigureras. Du kan till exempel konfigurera en princip som svarar på en viss [inloggningsrisknivå](../identity-protection/howto-identity-protection-configure-risk-policies.md). En princip som baseras på en inloggningsrisknivå kräver dock att [Azure Active Directory-identitetsskydd](../identity-protection/overview-identity-protection.md) aktiveras.

Om ytterligare funktioner krävs kan du också behöva skaffa relaterade licenser. Till exempel, medan villkorlig åtkomst är Azure AD Premium P1-funktionen, kräver identitetsskydd en Azure AD Premium P2-licens.

Det finns två typer av principer för villkorlig åtkomst: baslinje och standard. En [baslinjeprincip](baseline-protection.md) är en fördefinierad princip för villkorlig åtkomst. Målet med dessa principer är att se till att du har minst baslinjenivån för säkerhet aktiverad. Baslinjeprinciper. Baslinjeprinciper är tillgängliga i alla versioner av Azure AD och de innehåller endast begränsade anpassningsalternativ. Om ett scenario kräver större flexibilitet inaktiverar du originalprincipen och implementerar dina krav i en anpassad standardprincip.

I en standardprincip för villkorlig åtkomst kan du anpassa alla inställningar för att anpassa principen efter dina affärsbehov. Standardprinciper kräver en Azure AD Premium P1-licens.

>[!NOTE]
> Vi rekommenderar att du använder Azure AD-enhetsbaserad policy för villkorlig åtkomst för att få bästa möjliga efterlevnad efter inledande enhetsautentisering. Detta inkluderar att stänga sessioner om enheten faller utanför kompatibilitet och enhetskodflöde.

## <a name="draft-policies"></a>Utkast till policyer

Med Azure Active Directory Villkorlig åtkomst kan du anpassa skyddet av dina molnappar till en ny nivå. På den här nya nivån baseras hur du kan komma åt en molnapp på en dynamisk principutvärdering i stället för en statisk åtkomstkonfiguration. Med en princip för villkorlig åtkomst definierar du ett svar (**gör detta)** på ett åtkomstvillkor (**när detta inträffar).**

![Orsak och svar](./media/plan-conditional-access/10.png)

Definiera alla principer för villkorlig åtkomst som du vill implementera med den här planeringsmodellen. Planeringsövningen:

- Hjälper dig att beskriva svaren och villkoren för varje princip.
- Resulterar i en väldokumenterad principkatalog för villkorlig åtkomst för din organisation. 

Du kan använda katalogen för att bedöma om principimplementeringen återspeglar organisationens affärsbehov. 

Använd följande exempelmall för att skapa principer för villkorlig åtkomst för din organisation:

|När *detta* händer:|Gör sedan *så här:*|
|-|-|
|Ett åtkomstförsök görs:<br>- Till en molnapp*<br>- Av användare och grupper*<br>Använda:<br>- Villkor 1 (till exempel utanför Corp nätverk)<br>- Villkor 2 (t.ex. enhetsplattformar)|Blockera åtkomst till programmet|
|Ett åtkomstförsök görs:<br>- Till en molnapp*<br>- Av användare och grupper*<br>Använda:<br>- Villkor 1 (till exempel utanför Corp nätverk)<br>- Villkor 2 (t.ex. enhetsplattformar)|Bevilja åtkomst med (OCH):<br>- Krav 1 (t.ex. MFA)<br>- Krav 2 (t.ex. enhetsefterlevnad)|
|Ett åtkomstförsök görs:<br>- Till en molnapp*<br>- Av användare och grupper*<br>Använda:<br>- Villkor 1 (till exempel utanför Corp nätverk)<br>- Villkor 2 (t.ex. enhetsplattformar)|Bevilja åtkomst med (ELLER):<br>- Krav 1 (t.ex. MFA)<br>- Krav 2 (t.ex. enhetsefterlevnad)|

När detta **inträffar** definierar det huvudnamn (**vem**) som försöker komma åt en molnapp (**vad**). Om det behövs kan du också inkludera **hur** ett åtkomstförsök utförs. I Villkorlig åtkomst, de element som definierar vem, vad och hur som kallas villkor. Mer information finns [i Vad är villkoren i Azure Active Directory Villkorlig åtkomst?](concept-conditional-access-conditions.md) 

Med **sedan gör detta**definierar du svaret på din princip till ett åtkomstvillkor. I ditt svar blockerar eller beviljar du åtkomst med ytterligare krav, till exempel MFA (Multifaktorautentisering). En fullständig översikt finns [i Vad är åtkomstkontroller i Azure Active Directory Villkorlig åtkomst?](controls.md)  

Kombinationen av villkor med dina åtkomstkontroller representerar en princip för villkorlig åtkomst.

![Orsak och svar](./media/plan-conditional-access/51.png)

Mer information finns i [vad som krävs för att få en princip att fungera](best-practices.md#whats-required-to-make-a-policy-work).

Nu är det ett bra tillfälle att bestämma en namngivningsstandard för dina principer. Namngivningsstandarden hjälper dig att hitta principer och förstå deras syfte utan att öppna dem i Azure-administratörsportalen. Ge din policy ett namn:

- Ett sekvensnummer
- Molnappen som den gäller för
- Svaret
- Vem gäller för
- När det gäller (om tillämpligt)
 
![Namngivningsstandard](./media/plan-conditional-access/11.png)

Ett beskrivande namn hjälper dig att behålla en översikt över implementeringen av villkorlig åtkomst, men sekvensnumret är användbart om du behöver referera till en princip i en konversation. Om du till exempel pratar med en annan administratör på telefonen kan du be dem att öppna policy EM063 för att lösa ett problem.

Följande namn anger till exempel att principen kräver MFA för marknadsföring av användare i externa nätverk med hjälp av Dynamics CRP-appen:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Förutom dina aktiva principer är det lämpligt att även implementera principer för inaktiverad åtkomst som fungerar som sekundära [elastiska åtkomstkontroller i avbrotts-/nödscenarier.](../authentication/concept-resilient-controls.md) Din namngivningsstandard för principer för oförutsedda händelser bör innehålla några fler objekt: 

- `ENABLE IN EMERGENCY`i början för att få namnet att sticka ut bland de andra politikområdena.
- Namnet på störningar det bör gälla för.
- Ett beställningssekvensnummer som hjälper administratören att veta i vilken ordning principer ska aktiveras. 

Följande namn anger till exempel att den här principen är den första principen av fyra som du bör aktivera om det finns en MFA-störning:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Planera principer

När du planerar principlösningen för villkorsåtkomst bör du bedöma om du behöver skapa principer för att uppnå följande resultat. 

### <a name="block-access"></a>Blockera åtkomst

Alternativet att blockera åtkomst är kraftfullt eftersom det:

- Övertrumfar alla andra uppdrag för en användare
- Har befogenhet att blockera hela organisationen från att logga in på din klient
 
Om du vill blockera åtkomst för alla användare bör du åtminstone utesluta en användare (vanligtvis konton för nödåtkomst) från principen. Mer information finns i [välj användare och grupper](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Kräv MFA

Om du vill förenkla inloggningsupplevelsen för användarna kanske du vill tillåta dem att logga in på dina molnappar med ett användarnamn och ett lösenord. Det finns dock vanligtvis åtminstone några scenarier som det är lämpligt att kräva en starkare form av kontoverifiering. Med en princip för villkorlig åtkomst kan du begränsa kravet på MFA till vissa scenarier. 

Vanliga användningsfall som kräver makroekonomiskt stöd är åtkomst:

- [Av administratörer](howto-baseline-protect-administrators.md)
- [Till specifika appar](app-based-mfa.md) 
- [Från nätverksplatser litar du inte på](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Svara på potentiellt komprometterade konton

Med principer för villkorlig åtkomst kan du implementera automatiska svar på inloggningar från potentiellt komprometterade identiteter. Sannolikheten för att ett konto har komprometterats uttrycks i form av risknivåer. Det finns två risknivåer som beräknas av identitetsskydd: inloggningsrisk och användarrisk. Om du vill implementera ett svar på en inloggningsrisk har du två alternativ:

- [Inloggningsriskvillkoret i](concept-conditional-access-conditions.md#sign-in-risk) principen villkorlig åtkomst
- [Principen för inloggningsrisk](../identity-protection/howto-sign-in-risk-policy.md) i identitetsskydd 

Att ta itu med inloggningsrisken som villkor är den bästa metoden eftersom det ger dig fler anpassningsalternativ.

Användarrisknivån är endast tillgänglig som [användarriskprincip](../identity-protection/howto-user-risk-policy.md) i identitetsskydd. 

Mer information finns i [Vad är Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Kräv hanterade enheter

Spridningen av enheter som stöds för att komma åt dina molnresurser bidrar till att förbättra användarnas produktivitet. På baksidan vill du förmodligen inte att vissa resurser i din miljö ska nås av enheter med en okänd skyddsnivå. För de berörda resurserna bör du kräva att användarna bara kan komma åt dem med hjälp av en hanterad enhet. Mer information finns i [Så här kräver du hanterade enheter för molnappåtkomst med villkorlig åtkomst](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Kräva godkända klientappar

Ett av de första besluten du behöver fatta för att ta med egna enheter (BYOD) scenarier, är om du behöver hantera hela enheten eller bara data på den. Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. Samtidigt som du ser till att dina anställda kan vara produktiva, vill du också förhindra dataförlust. Med Azure Active Directory (Azure AD) Villkorlig åtkomst kan du begränsa åtkomsten till dina molnappar till godkända klientappar som kan skydda dina företagsdata. Mer information finns i [Så här kräver du godkända klientappar för molnappåtkomst med villkorlig åtkomst](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Azure AD stöder flera av de mest använda autentiserings- och auktoriseringsprotokollen, inklusive äldre autentisering. Hur kan du förhindra att appar som använder äldre autentisering kommer åt klientens resurser? Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs tillåter du bara vissa användare och specifika nätverksplatser att använda appar som baseras på äldre autentisering. Mer information finns i [Så här blockerar du äldre autentisering till Azure AD med villkorlig åtkomst](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testa din policy

Innan du distribuerar en princip till produktion bör du testa att den fungerar som förväntat.

1. Skapa testanvändare
1. Skapa en testplan
1. Konfigurera principen
1. Utvärdera en simulerad inloggning
1. Testa din policy
1. Rensa

### <a name="create-test-users"></a>Skapa testanvändare

Om du vill testa en princip skapar du en uppsättning användare som liknar användarna i din miljö. Genom att skapa testanvändare kan du kontrollera att dina principer fungerar som förväntat innan du påverkar verkliga användare och kan störa deras åtkomst till appar och resurser. 

Vissa organisationer har testklienter för detta ändamål. Det kan dock vara svårt att återskapa alla villkor och appar i en testklient för att fullständigt testa resultatet av en princip. 

### <a name="create-a-test-plan"></a>Skapa en testplan

Testplanen är viktig för att ha en jämförelse mellan de förväntade resultaten och de faktiska resultaten. Du bör alltid ha en förväntan innan du testar något. I följande tabell beskrivs exempeltestfall. Justera scenarierna och förväntade resultat baserat på hur certifikatutfärdarprinciperna är konfigurerade.

|Princip |Scenario |Förväntat resultat | Resultat |
|---|---|---|---|
|[Kräv MFA när den inte är på jobbet](/azure/active-directory/conditional-access/untrusted-networks)|Behöriga användare loggar in på *App* på en betrodd plats/arbete|Användaren uppmanas inte till MFA| |
|[Kräv MFA när den inte är på jobbet](/azure/active-directory/conditional-access/untrusted-networks)|Behöriga användare loggar in på *App* medan de inte finns på en betrodd plats/ett tillförlitligt arbete|Användaren uppmanas till MFA och kan logga in| |
|[Kräv MFA (för admin)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Global administratör loggar in på *App*|Admin uppmanas till MFA| |
|[Riskfyllda inloggningar](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Användaren loggar in på *Appen* med en [Tor-webbläsare](/azure/active-directory/active-directory-identityprotection-playbook)|Admin uppmanas till MFA| |
|[Enhetshantering](/azure/active-directory/conditional-access/require-managed-devices)|Behöriga användare försöker logga in från en auktoriserad enhet|Beviljad åtkomst| |
|[Enhetshantering](/azure/active-directory/conditional-access/require-managed-devices)|Behöriga användare försöker logga in från en obehörig enhet|Åtkomst blockerad| |
|[Lösenordsändring för riskfyllda användare](/azure/active-directory/identity-protection/howto-user-risk-policy)|Behöriga användare försöker logga in med komprometterade autentiseringsuppgifter (högriskloggning)|Användaren uppmanas att ändra lösenord eller åtkomst blockeras baserat på din princip| |

### <a name="configure-the-policy"></a>Konfigurera principen

Hantering av principer för villkorlig åtkomst är en manuell aktivitet. I Azure-portalen kan du hantera dina principer för villkorlig åtkomst på en central plats - sidan Villkorlig åtkomst. En startpunkt till sidan Villkorlig åtkomst är avsnittet **Säkerhet** i navigeringsfönstret **i Active Directory.** 

![Villkorlig åtkomst](media/plan-conditional-access/03.png)

Om du vill veta mer om hur du skapar principer för villkorlig åtkomst läser du [Kräv MFA för specifika appar med Azure Active Directory Villkorlig åtkomst](app-based-mfa.md). Den här snabbstarten hjälper dig att:

- Bekanta dig med användargränssnittet.
- Få ett första intryck av hur villkorlig åtkomst fungerar. 

### <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat principen villkorlig åtkomst vill du förmodligen veta om den fungerar som förväntat. Som ett första steg använder du verktyget Villkorlig åtkomst [vad händer om-principverktyget](what-if-tool.md) för att simulera en inloggning av testanvändaren. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.

>[!NOTE]
> En simulerad körning ger dig intryck av vilken påverkan en princip för villkorlig åtkomst har, men den ersätter inte en faktisk testkörning.

### <a name="test-your-policy"></a>Testa din policy

Kör testfall enligt din testplan. I det här steget kör du igenom ett end-to-end-test av varje princip för dina testanvändare för att se till att varje princip fungerar korrekt. Använd de scenarier som skapats ovan för att köra varje test.

Det är viktigt att du testar uteslutningskriterierna för en princip. Du kan till exempel utesluta en användare eller grupp från en princip som kräver MFA. Testa om de uteslutna användarna uppmanas för MFA, eftersom kombinationen av andra principer kan kräva MFA för dessa användare.

### <a name="cleanup"></a>Rensa

Rensningsproceduren består av följande steg:

1. Inaktivera principen.
1. Ta bort de tilldelade användarna och grupperna.
1. Ta bort testanvändarna.  

## <a name="move-to-production"></a>Flytta till produktion

När nya principer är klara för din miljö kan du distribuera dem i faser:

- Ge intern ändringskommunikation till slutanvändare.
- Börja med en liten uppsättning användare och kontrollera att principen fungerar som förväntat.
- När du expanderar en princip så att den omfattar fler användare fortsätter du att utesluta alla administratörer. Exklusive administratörer säkerställer att någon fortfarande har åtkomst till en princip om en ändring krävs.
- Tillämpa en princip endast för alla användare om det behövs.

På bästa sätt bör du skapa minst ett användarkonto som är:

- Tillägnad politisk administration
- Utesluten från alla dina policyer

## <a name="rollback-steps"></a>Steg för återställning

Om du behöver återställa dina nyligen implementerade principer kan du använda ett eller flera av följande alternativ för att återställa:

1. **Inaktivera principen** – Inaktivera en princip ser till att den inte gäller när en användare försöker logga in. Du kan alltid komma tillbaka och aktivera principen när du vill använda den.

   ![Inaktivera princip](media/plan-conditional-access/07.png)

1. **Utesluta en användare/grupp från en princip** - Om en användare inte kan komma åt appen kan du välja att utesluta användaren från principen

   ![Exluce-användare](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Det här alternativet bör användas sparsamt, endast i situationer där användaren är betrodd. Användaren ska läggas tillbaka till principen eller gruppen så snart som möjligt.

1. **Ta bort principen** - Om principen inte längre behövs tar du bort den.

## <a name="next-steps"></a>Nästa steg

Kolla in [Azure AD-dokumentation för villkorlig åtkomst](index.yml) för att få en översikt över tillgänglig information.
