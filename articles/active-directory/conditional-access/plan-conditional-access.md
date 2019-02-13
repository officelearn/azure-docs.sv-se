---
title: Planera principer för villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: I den här artikeln får du lära dig hur du planerar principer för villkorlig åtkomst för Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d43c7943eedcfdf839303a6a55d7727a8483441
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175820"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Instruktioner: Planera distributionen av villkorlig åtkomst i Azure Active Directory

Planera distributionen av villkorlig åtkomst är viktigt att se till att du få strategin kräver åtkomst för appar och resurser i din organisation. Du bör spenderar det mesta av din tid under planeringsfasen i distributionen för att utforma olika principer som du behöver för att bevilja eller neka åtkomst till dina användare på de villkor som du väljer. Det här dokumentet beskrivs de steg som du bör vidta för att implementera säkra och effektiva villkorliga åtkomstprinciper. Innan du börjar måste du kontrollera att du förstår hur [villkorlig åtkomst](overview.md) fungerar och när du ska använda.


## <a name="what-you-should-know"></a>Det här bör du känna till

Tänk på villkorlig åtkomst som ett ramverk som gör att du kan styra åtkomsten till din organisations appar och resurser, i stället för en fristående funktion. Vissa inställningar för villkorlig åtkomst kräver därför, extrafunktioner som ska konfigureras. Du kan till exempel konfigurera en princip som svarar på en specifik [risknivå för inloggning](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy). En princip som baseras på en nivå för inloggningsrisk kräver dock [Azure Active Directory identity protection](../identity-protection/overview.md) aktiveras.

Om ytterligare funktioner krävs, kan du också behöva hämta relaterade licenser. Villkorlig åtkomst är Azure AD Premium P1-funktionen, kräver identitetsskydd en Azure AD Premium P2-licens.

Det finns två typer av principer för villkorlig åtkomst: grundläggande och standard. En [baslinjeprincip](baseline-protection.md) är en fördefinierad villkorlig åtkomstprincip. Målet med dessa principer är att se till att du har minst baslinje-säkerhetsnivå aktiverat. Grundläggande principer. Grundläggande principer är tillgängliga i alla utgåvor av Azure AD och ger endast begränsade anpassningsalternativ. Om ett scenario kräver mer flexibilitet, inaktivera baslinje-principen och implementera dina krav i en anpassad princip för standard.

Du kan anpassa alla inställningar om du vill ändra principen för ditt företags behov i en princip för villkorlig åtkomst som standard. Standard principer kräver en Azure AD Premium P1-licens.




## <a name="draft-policies"></a>Draft principer

Villkorlig åtkomst i Azure Active Directory kan du överföra skydd av dina appar i molnet till en ny nivå. I den här nya nivån baseras hur du kan komma åt en molnapp på en dynamisk principutvärdering i stället för en statisk konfiguration. Med en princip för villkorlig åtkomst definierar du ett svar (**då**) till ett villkor för åtkomst (**om det här händer**).

![Orsak och svar](./media/plan-conditional-access/10.png)

Definiera varje princip för villkorlig åtkomst som du vill implementera med hjälp av den här planeringen modellen. Planering Övning:

- Hjälper dig att visas svar och villkor för varje princip.
- Resultat i en katalog med väl dokumenterade villkorlig åtkomst princip för din organisation. 

Du kan använda din katalog för att utvärdera om din implementering av principer återspeglar organisationens affärsbehov. 

Använd följande exempelmall för att skapa principer för villkorlig åtkomst för din organisation:

|När *detta* händer:|Gör sedan *detta*:|
|-|-|
|Det görs ett åtkomstförsök:<br>– Om du vill en molnapp*<br>– användare och grupper*<br>Om du använder:<br>-Villkor 1 (till exempel utanför företagsnätverk)<br>-Villkor 2 (till exempel enhetsplattformar)|Blockera åtkomst till programmet|
|Det görs ett åtkomstförsök:<br>– Om du vill en molnapp*<br>– användare och grupper*<br>Om du använder:<br>-Villkor 1 (till exempel utanför företagsnätverk)<br>-Villkor 2 (till exempel enhetsplattformar)|Bevilja åtkomst med (AND):<br>-Krav 1 (till exempel MFA)<br>-Krav 2 (till exempel enhetsefterlevnad)|
|Det görs ett åtkomstförsök:<br>– Om du vill en molnapp*<br>– användare och grupper*<br>Om du använder:<br>-Villkor 1 (till exempel utanför företagsnätverk)<br>-Villkor 2 (till exempel enhetsplattformar)|Bevilja åtkomst med (eller):<br>-Krav 1 (till exempel MFA)<br>-Krav 2 (till exempel enhetsefterlevnad)|

Minst **om det här händer** definierar huvudkontot (**som**) som försöker få åtkomst till en molnapp (**vad**). Om nödvändigt, kan du även inkludera **hur** ett åtkomstförsök utförs. Villkorlig åtkomst elementen som definierar vem, vad och hur kallas villkor. Mer information finns i [vad är villkor i Azure Active Directory villkorlig åtkomst?](conditions.md) 

Med **gör detta**, du definiera svaret på din princip till ett villkor för åtkomst. I ditt svar du antingen blockera eller bevilja åtkomst med ytterligare krav, till exempel multifaktorautentisering (MFA). En fullständig översikt finns i [vad är access styr i Azure Active Directory villkorlig åtkomst?](controls.md)  
 

Kombinationen av villkor med dina åtkomstkontroller representerar en princip för villkorsstyrd åtkomst.

![Orsak och svar](./media/plan-conditional-access/51.png)


Mer information finns i [vad krävs för att göra en princip som fungerar](best-practices.md#whats-required-to-make-a-policy-work).

Nu är det dags att besluta om en namngivningsstandard för dina principer. Namnstandarden hjälper dig att hitta principer och förstå syftet utan att öppna dem i Azure-administrationsportalen. Du bör namnge din princip att visa:

- Ett sekvensnummer
- Molnappen som gäller för
- Svaret
- Vem som gäller för
- När det gäller (om tillämpligt)
 
![Namngivningsstandarden](./media/plan-conditional-access/11.png)

Ett beskrivande namn hjälper dig att få överblick över implementeringen av villkorlig åtkomst, är sekvensnumret användbart om du vill referera till en princip i en konversation. Exempelvis kan du be honom att öppna princip EM063 att lösa ett problem om du prata med en andra administratör på telefonen.



Till exempel anger följande namn att principen kräver MFA för Marknadsföringsanvändare på externa nätverk som använder appen Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`


Förutom din aktiva principer, är det lämpligt att även implementera inaktiverad principer som fungerar som sekundär [elastiska åtkomstkontroller i avbrott/nödfall situationer](../authentication/concept-resilient-controls.md). Din namngivningsstandarden för oförutsedda händelser principer bör innehålla några fler objekt: 

- `ENABLE IN EMERGENCY` i början göra namnet skilja sig från de andra principerna.

- Namnet på avbrott som den ska tillämpas på.

- Ett skrivordning sekvensnummer och hjälper administratören att veta i vilken ordning principer ska aktiveras. 


Till exempel anger följande namn att den här principen är den första principen från fyra bör du aktivera när det gäller MFA avbrott:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`







## <a name="plan-policies"></a>Planera principer

När du planerar din lösning för princip för villkorlig åtkomst, bedöma om du behöver skapa principer för att uppnå följande resultat. 


### <a name="block-access"></a>Blockera åtkomst

Alternativet för att blockera åtkomst är kraftfulla eftersom den:

- Trumps alla tilldelningar för en användare

- Har rätt att blockera hela din organisation loggar in till din klient
 
Om du vill blockera åtkomst för alla användare, bör du minst undanta en användare (vanligtvis Nödfallsåtkomst konton) från principen. Mer information finns i [Välj användare och grupper](block-legacy-authentication.md#select-users-and-cloud-apps).  
    

### <a name="require-mfa"></a>Kräv MFA

För att förenkla inloggning för dina användare kan vilja du att de kan logga in på dina appar i molnet med ett användarnamn och ett lösenord. Men vanligtvis finns det minst några scenarier som det är lämpligt att kräva en starkare form av verifiering för kontot. Du kan begränsa för MFA kravet på att vissa scenarier med en princip för villkorlig åtkomst. 

Vanliga användningsområden för att kräva MFA är åtkomst:

- [Av administratörer](baseline-protection.md#require-mfa-for-admins)
- [Till specifika appar](app-based-mfa.md) 
- [Från nätverket, som du inte litar](untrusted-networks.md).


### <a name="respond-to-potentially-compromised-accounts"></a>Svara på potentiellt komprometterade konton

Du kan implementera automatiska svar till inloggningar från potentiellt komprometterade identiteter med principer för villkorlig åtkomst. Sannolikheten att ett konto har komprometterats uttrycks i form av risknivåer. Det finns två risknivåer beräknas genom att identitetsskydd: inloggningsrisk och användarrisk. För att implementera ett svar till en inloggningsrisk, har du två alternativ:

- [Villkoret inloggningsrisk](conditions.md#sign-in-risk) i principen för villkorlig åtkomst
- [Princip för inloggningsrisk](../identity-protection/howto-sign-in-risk-policy.md) i identity protection 

Adressering inloggningsrisk som villkor är den bästa metoden eftersom det ger dig fler anpassningsalternativ.

Risknivån är endast tillgänglig som [användarprincip](../identity-protection/howto-user-risk-policy.md) i identity protection. 

Mer information finns i [vad är Azure Active Directory Identity Protection?](../identity-protection/overview.md) 


### <a name="require-managed-devices"></a>Kräv hanterade enheter

Det hjälper dig för att förbättra användarnas produktivitet ökningen av enheter som stöds till dina molnresurser. Å andra sidan vill du förmodligen inte vissa resurser i din miljö för att användas av enheter med en okänd skyddsnivån. För de berörda resurserna bör du kräva att användare kan bara komma åt dem med hjälp av en hanterad enhet. Mer information finns i [kräva hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Kräva godkända klientappar

En av de första besluten som du behöver göra bring your own Device (BYOD)-scenarier, är om du behöver hantera hela enheten eller bara data på den. Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. Att se till att dina anställda kan vara produktiva, vill du också förhindra förlust av data. Med villkorlig åtkomst för Azure Active Directory (AD Azure), kan du begränsa åtkomsten till dina appar i molnet på godkända klientappar som kan skydda företagets data. Mer information finns i [kräva godkända klientappar för åtkomst till molnet appen med villkorlig åtkomst](app-based-conditional-access.md).


### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Azure AD stöder flera av de mest använda protokoll för autentisering och auktorisering, inklusive äldre autentisering. Hur kan du förhindra att appar som använder äldre autentisering från att komma åt resurser i din klient Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs kan tillåta du enbart vissa användare och specifika nätverksplatser du använder appar som baseras på äldre autentisering. Mer information finns i [blockera äldre autentisering till Azure AD med villkorlig åtkomst](block-legacy-authentication.md).


## <a name="test-your-policy"></a>Testa din princip

Innan du distribuerar en princip till produktion, bör du testa att kontrollera att det fungerar som förväntat.

1. Skapa testanvändare

2. Skapa en testplan

3. Konfigurera principen

4. Utvärdera en simulerad inloggning

5. Testa din princip

6. Rensa



### <a name="create-test-users"></a>Skapa testanvändare

Om du vill testa en princip för att skapa en uppsättning användare som liknar användare i din miljö. Skapa testanvändare kan du kontrollera att dina principer fungerar som förväntat innan du påverkar riktiga användare och störa åtkomsten till appar och resurser. 


Vissa organisationer har test-klienter för detta ändamål. Det kan dock vara svårt att återskapa alla villkor och appar i en test-klient för att testa fullständigt resultatet av en princip. 

### <a name="create-a-test-plan"></a>Skapa en testplan

Det är viktigt att ha en jämförelse mellan de förväntade resultaten och de faktiska resultaten i testplanen. Du bör alltid ha en förväntan innan du testar något. I följande tabell visas exempel testfall. Justera scenarier och förväntade resultat baserat på hur dina CA-principer har konfigurerats.

|Princip |Scenario |Förväntat resultat | Resultat |
|---|---|---|---|
|[Kräva MFA när du inte arbetar](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Auktoriserade användare loggar in *App* befinner dig på en betrodd plats / arbete|Användaren inte ska uppmanas att MFA| |
|[Kräva MFA när du inte arbetar](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Auktoriserade användare loggar in *App* medan inte på en betrodd plats / arbete|Användaren uppmanas att MFA och kan logga in| |
|[Kräva MFA (för administratör)](https://docs.microsoft.com/azure/active-directory/conditional-access/baseline-protection#require-mfa-for-admins)|Global administratör loggar in på *App*|Administratören uppmanas att MFA| |
|[Riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Användaren loggar in på *App* med hjälp av en [Tor webbläsare](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Administratören uppmanas att MFA| |
|[Enhetshantering](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Auktoriserade användare försöker logga in från en auktoriserad enhet|Åtkomst beviljad| |
|[Enhetshantering](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Auktoriserade användare försöker logga in från en obehörig enhet|Åtkomst som är blockerad| |
|[Ändring av lösenord för riskabla användare](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Auktoriserade användare försöker logga in med avslöjade autentiseringsuppgifter (hög risk logga in)|Användaren uppmanas att ändra lösenord eller blockeras åtkomsten baserat på din princip| |


### <a name="configure-the-policy"></a>Konfigurera principen

Hantera principer för villkorlig åtkomst är en manuell aktivitet. Du kan hantera principer för villkorlig åtkomst på en central plats - sidan för villkorlig åtkomst i Azure-portalen. En startpunkt för sidan villkorlig åtkomst är den **Security** i avsnittet den **Active Directory** navigeringsfönstret. 

![Villkorlig åtkomst](media/plan-conditional-access/03.png)


Om du vill veta mer om hur du skapar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md). Den här snabbstarten hjälper dig att:

- Bekanta dig med användargränssnittet.
- Få ett första intryck av hur villkorlig åtkomst fungerar. 


### <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorsstyrd åtkomst vill du förmodligen veta om den fungerar som förväntat. Använda villkorlig åtkomst som ett första steg [vad händer om principen verktyget](what-if-tool.md) att simulera en inloggning av din testanvändare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.

>[!NOTE]
> Körningen av en simulerad ger intryck inverkan principer för villkorlig åtkomst har, ersätter inte en faktisk testkörningen.


### <a name="test-your-policy"></a>Testa din princip

Kör testfall enligt testplanen. I det här steget ska gå du igenom ett test för slutpunkt till slutpunkt för varje princip för test-användarna ska se till att varje princip fungerar som avsett. Använd de scenarier som skapades ovan för att köra varje test.

Det är viktigt att se till att du testar exkluderingsvillkor av en princip. Du kan till exempel undanta en användare eller grupp från en princip som kräver MFA. Du bör därför testa om exkluderade användare ombeds MFA, eftersom kombinationen av andra principer kan kräva MFA för dessa användare.


### <a name="cleanup"></a>Rensa

Rensningsproceduren består av följande steg:

1. Inaktivera principen.

2. Ta bort de tilldelade användare och grupper.

3. Ta bort testanvändarna.  




## <a name="move-to-production"></a>Flytta till produktion

När nya principer är redo för din miljö kan du distribuera dem i olika faser:

- Ange intern ändring kommunikation till slutanvändare.

- Börja med en liten uppsättning användare och kontrollera att principen fungerar som förväntat.

- När du har expanderat en princip för att inkludera fler användare kan fortsätta att undanta alla administratörer. Exkludera administratörer säkerställer som någon har fortfarande åtkomst till en princip om en ändring krävs.

- Tillämpa en princip för alla användare bara om det är obligatoriskt.

Skapa minst ett användarkonto som är ett bra tips är:

- Dedikerad för administration av principer

- Undantas från alla dina principer

 



## <a name="rollback-steps"></a>Steg för återställning

Om du vill återställa din nyligen implementerade principer kan du använda en eller flera av följande alternativ för att återställa:

1. **Inaktivera principen** – inaktiverar en princip gör att det inte gäller när en användare försöker logga in. Du kan alltid gå tillbaka och aktivera principen när du vill använda den.

    ![Inaktivera principen](media/plan-conditional-access/07.png)

2. **Undanta en användare / grupp från en princip** – om en användare kan inte få åtkomst till appen, du kan välja att exkludera användaren från principen

    ![Exluce användare](media/plan-conditional-access/08.png)

    >[!NOTE]
    > Det här alternativet bör användas sparsamt, endast i situationer där användaren är betrodd. Användaren bör läggas tillbaka i principen eller grupp så snart som möjligt.

3. **Ta bort principen** - om principen inte längre behövs, ta bort den.

## <a name="next-steps"></a>Nästa steg

Kolla in [dokumentation för Azure AD villkorlig åtkomst](index.yml) att få en översikt över tillgänglig information.
