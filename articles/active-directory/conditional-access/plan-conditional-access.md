---
title: Planera principer för villkorlig åtkomst i Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 3c8c5e3c2552101437bfed17906f94861e676568
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468445"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Gör så här: planera din distribution av villkorlig åtkomst i Azure Active Directory

Det är viktigt att planera distributionen av den villkorliga åtkomsten för att se till att du uppnår den nödvändiga åtkomst strategin för appar och resurser i din organisation. Spendera det mesta av din tid under planerings fasen för distributionen för att utforma de olika principerna som du behöver för att bevilja eller blockera åtkomst till användarna under de villkor som du väljer. Det här dokumentet beskriver de steg du bör vidta för att implementera säkra och effektiva principer för villkorlig åtkomst. Innan du börjar ska du kontrol lera att du förstår hur [villkorlig åtkomst](overview.md) fungerar och när du ska använda det.

## <a name="what-you-should-know"></a>Det här bör du känna till

Tänk på villkorlig åtkomst som ett ramverk som gör att du kan styra åtkomsten till din organisations appar och resurser, i stället för en fristående funktion. Vissa inställningar för villkorlig åtkomst kräver därför att ytterligare funktioner konfigureras. Du kan till exempel konfigurera en princip som svarar på en särskilt [inloggnings risk nivå](../identity-protection/howto-identity-protection-configure-risk-policies.md). En princip som baseras på en inloggnings risk nivå kräver dock att [Azure Active Directory identitets skydd](../identity-protection/overview-identity-protection.md) är aktiverat.

Om ytterligare funktioner krävs kan du också behöva hämta relaterade licenser. Till exempel, medan villkorlig åtkomst är Azure AD Premium P1-funktionen, kräver identitets skydd en Azure AD Premium P2-licens.

Det finns två typer av principer för villkorlig åtkomst: bas linje och standard. En [bas linje princip](baseline-protection.md) är en fördefinierad princip för villkorlig åtkomst. Målet med dessa principer är att se till att du har minst säkerhets nivån som är aktive rad. Bas linje principer. Bas linje principer är tillgängliga i alla versioner av Azure AD och de tillhandahåller endast begränsade anpassnings alternativ. Om ett scenario kräver större flexibilitet kan du inaktivera bas linje principen och implementera dina krav i en anpassad standard princip.

I en standard princip för villkorlig åtkomst kan du anpassa alla inställningar för att justera principen efter dina affärs behov. Standard principer kräver en Azure AD Premium P1-licens.

>[!NOTE]
> Vi rekommenderar att du använder Azure AD Device-baserad princip för villkorlig åtkomst för att få bästa möjliga tillämpning efter den första autentiseringen av enheten. Detta inkluderar sessioner som stänger om enheten faller utanför efterlevnad och enhets kod flödet.

## <a name="draft-policies"></a>Utkast principer

Med Azure Active Directory villkorlig åtkomst kan du skydda dina molnappar med en ny nivå. På den nya nivån, hur du kan komma åt en molnbaserad app baseras på en dynamisk princip utvärdering i stället för en statisk åtkomst konfiguration. Med en princip för villkorlig åtkomst definierar du ett svar (**gör detta**) till ett åtkomst villkor (**när det händer**).

![Orsak och svar](./media/plan-conditional-access/10.png)

Definiera varje princip för villkorlig åtkomst som du vill implementera med hjälp av den här planerings modellen. Planerings övningen:

- Hjälper dig att disponera svar och villkor för varje princip.
- Resulterar i en väl dokumenterad princip katalog för villkorlig åtkomst för din organisation. 

Du kan använda din katalog för att bedöma om princip implementeringen återspeglar organisationens affärs behov. 

Använd följande exempel mall för att skapa principer för villkorlig åtkomst för din organisation:

|När *detta* händer:|*Gör sedan följande:*|
|-|-|
|Ett åtkomst försök har gjorts:<br>– Till en molnbaserad app *<br>– av användare och grupper*<br>Där<br>– Villkor 1 (till exempel utanför Corp-nätverk)<br>– Villkor 2 (till exempel enhets plattformar)|Blockera åtkomst till programmet|
|Ett åtkomst försök har gjorts:<br>– Till en molnbaserad app *<br>– av användare och grupper*<br>Där<br>– Villkor 1 (till exempel utanför Corp-nätverk)<br>– Villkor 2 (till exempel enhets plattformar)|Bevilja åtkomst med (och):<br>-Krav 1 (till exempel MFA)<br>-Krav 2 (till exempel enhetens efterlevnad)|
|Ett åtkomst försök har gjorts:<br>– Till en molnbaserad app *<br>– av användare och grupper*<br>Där<br>– Villkor 1 (till exempel utanför Corp-nätverk)<br>– Villkor 2 (till exempel enhets plattformar)|Bevilja åtkomst med (eller):<br>-Krav 1 (till exempel MFA)<br>-Krav 2 (till exempel enhetens efterlevnad)|

**När detta inträffar** definieras den huvudprincip (**som**) som försöker få åtkomst till en molnbaserad app (**vad**). Om det behövs kan du även ta med **hur** ett åtkomst försök utförs. I villkorlig åtkomst är de element som definierar vem, vad och hur kallas för villkor. Mer information finns i [Vad är villkor i Azure Active Directory villkorlig åtkomst?](concept-conditional-access-conditions.md) 

**Därefter**definierar du svars principen till ett åtkomst villkor. I ditt svar kan du antingen blockera eller bevilja åtkomst med ytterligare krav, till exempel Multi-Factor Authentication (MFA). En fullständig översikt finns i [Vad är åtkomst kontroller i Azure Active Directory villkorlig åtkomst?](controls.md)  

Kombinationen av villkor med dina åtkomst kontroller representerar en princip för villkorlig åtkomst.

![Orsak och svar](./media/plan-conditional-access/51.png)

Mer information finns i [vad som krävs för att en princip ska fungera](best-practices.md#whats-required-to-make-a-policy-work).

I det här skedet är det dags att välja en namngivnings standard för dina principer. Namngivnings standarden hjälper dig att hitta principer och förstå deras syfte utan att öppna dem i Azure admin-portalen. Ge principen ett namn som du vill visa:

- Ett sekvensnummer
- Cloud-appen som den gäller för
- Svaret
- Vem det gäller för
- När det gäller (om tillämpligt)
 
![Namngivnings standard](./media/plan-conditional-access/11.png)

Ett beskrivande namn hjälper dig att hålla en översikt över implementeringen av villkorlig åtkomst, sekvensnummer är användbart om du behöver referera till en princip i en konversation. Om du t. ex. pratar om en administratör på telefonen kan du be dem att öppna princip EM063 för att lösa ett problem.

Följande namn anger till exempel att principen kräver MFA för marknadsförings användare på externa nätverk med hjälp av Dynamics CRP-appen:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Förutom dina aktiva principer är det tillrådligt att även implementera inaktiverade principer som fungerar som sekundära [elastiska åtkomst kontroller i avbrott/nöd situationer](../authentication/concept-resilient-controls.md). Din namngivnings standard för katastrof principerna ska innehålla några fler objekt: 

- `ENABLE IN EMERGENCY` i början för att namnet ska se ut bland de andra principerna.
- Namnet på det avbrott som det ska gälla för.
- Ett ordnings ordnings nummer som hjälper administratören att veta i vilka ordnings principer ska aktive ras. 

Till exempel visar följande namn att den här principen är den första principen av fyra som du bör aktivera om det uppstår ett MFA-avbrott:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Planera principer

När du planerar din lösning för princip för villkorlig åtkomst ska du utvärdera om du behöver skapa principer för att uppnå följande resultat. 

### <a name="block-access"></a>Blockera åtkomst

Alternativet att blockera åtkomst är kraftfullt eftersom det:

- Trumfar alla andra tilldelningar för en användare
- Har kraften att blockera hela organisationen från att logga in på din klient organisation
 
Om du vill blockera åtkomst för alla användare bör du minst undanta en användare (vanligt vis nödfalls åtkomst konton) från principen. Mer information finns i [Välj användare och grupper](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Kräv MFA

För att förenkla inloggnings upplevelsen för dina användare kanske du vill att de ska kunna logga in på dina molnappar med ett användar namn och ett lösen ord. Vanligt vis finns det dock minst några scenarier för vilka det är lämpligt att kräva en starkare form av konto verifiering. Med en princip för villkorlig åtkomst kan du begränsa kravet för MFA till vissa scenarier. 

Vanliga användnings fall för att kräva MFA är åtkomst:

- [Av administratörer](howto-baseline-protect-administrators.md)
- [Till vissa appar](app-based-mfa.md) 
- [Du kan inte lita på nätverks platser](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Svara på potentiellt komprometterade konton

Med principer för villkorlig åtkomst kan du implementera automatiserade svar på inloggningar från potentiellt komprometterade identiteter. Sannolikheten för att ett konto har komprometterats uttrycks i form av risk nivåer. Det finns två risk nivåer som beräknas med identitets skydd: inloggnings risker och användar risker. Om du vill implementera ett svar på en inloggnings risk har du två alternativ:

- [Villkoret för inloggnings risk](concept-conditional-access-conditions.md#sign-in-risk) i principen för villkorlig åtkomst
- [Principen för inloggnings risker](../identity-protection/howto-sign-in-risk-policy.md) i identitets skydd 

Den bästa metoden är att hantera inloggnings risken som villkor eftersom det ger dig fler anpassnings alternativ.

Användar risk nivån är bara tillgänglig som [användar risk princip](../identity-protection/howto-user-risk-policy.md) i identitets skydd. 

Mer information finns i [Vad är Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Kräv hanterade enheter

Spridningen av enheter som stöds för att komma åt moln resurser hjälper till att förbättra användarnas produktivitet. På sidan vänd vill du förmodligen inte att vissa resurser i din miljö ska kunna nås av enheter med en okänd skydds nivå. För de berörda resurserna bör du kräva att användarna bara kan komma åt dem med hjälp av en hanterad enhet. Mer information finns i [så här kräver du hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Kräva godkända klientappar

Ett av de första beslut som du måste fatta för att ta med dina egna enheter (BYOD) är om du behöver hantera hela enheten eller bara data på den. Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. När du ser till att dina anställda kan vara produktiva vill du även förhindra data förlust. Med Azure Active Directory (Azure AD) villkorlig åtkomst kan du begränsa åtkomsten till dina molnappar till godkända klient program som kan skydda företagets data. Mer information finns i [så här kräver du godkända klient program för Cloud app-åtkomst med villkorlig åtkomst](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Azure AD stöder flera av de vanligaste autentiseringsprotokollen för autentisering och auktorisering, inklusive äldre autentisering. Hur kan du förhindra att appar använder äldre autentisering för att komma åt din klients resurser? Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs kan endast vissa användare och specifika nätverks platser använda appar som baseras på äldre autentisering. Mer information finns i [så här blockerar du äldre autentisering till Azure AD med villkorlig åtkomst](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testa principen

Innan du utvärderar en princip i produktion bör du kontrol lera att den fungerar som förväntat.

1. Skapa test användare
1. Skapa en test plan
1. Konfigurera principen
1. Utvärdera en simulerad inloggning
1. Testa principen
1. Rensa

### <a name="create-test-users"></a>Skapa test användare

Om du vill testa en princip skapar du en uppsättning användare som liknar användarna i din miljö. Genom att skapa test användare kan du kontrol lera att principerna fungerar som förväntat innan du påverkar riktiga användare och kan störa deras åtkomst till appar och resurser. 

Vissa organisationer har test klienter för detta ändamål. Det kan dock vara svårt att återskapa alla villkor och appar i en test klient för att fullständigt testa resultatet av en princip. 

### <a name="create-a-test-plan"></a>Skapa en test plan

Test planen är viktig för att få en jämförelse mellan de förväntade resultaten och de faktiska resultaten. Du bör alltid ha en förväntad händelse innan du testar något. I följande tabell visas exempel test fall. Justera scenarier och förväntade resultat baserat på hur dina CA-principer är konfigurerade.

|Princip |Scenario |Förväntat resultat | Resultat |
|---|---|---|---|
|[Kräv MFA när det inte är på arbetet](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Auktoriserad användare loggar in på *appen* på en betrodd plats/ett arbete|Användaren uppmanas inte att använda MFA| |
|[Kräv MFA när det inte är på arbetet](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Auktoriserad användare loggar in på *appen* men inte på en betrodd plats/arbete|Användaren uppmanas att MFA och kan logga in| |
|[Kräv MFA (för admin)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Global administratör loggar in på *appen*|Administratör uppmanas att MFA| |
|[Riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Användaren loggar in i *appen* med en [Tor-webbläsare](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Administratör uppmanas att MFA| |
|[Enhetshantering](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Behörig användare försöker logga in från en auktoriserad enhet|Åtkomst beviljad| |
|[Enhetshantering](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Behörig användare försöker logga in från en oauktoriserad enhet|Åtkomst blockerad| |
|[Lösen ords ändring för riskfyllda användare](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Behörig användare försöker logga in med komprometterade autentiseringsuppgifter (hög risk inloggning)|Användaren uppmanas att ändra lösen ordet eller åtkomsten blockeras baserat på principen| |

### <a name="configure-the-policy"></a>Konfigurera principen

Att hantera principer för villkorlig åtkomst är en manuell uppgift. I Azure Portal kan du hantera principer för villkorlig åtkomst på en central plats – sidan för villkorlig åtkomst. En start punkt på sidan för villkorlig åtkomst är **säkerhets** avsnittet i navigerings fönstret **Active Directory** . 

![Villkorlig åtkomst](media/plan-conditional-access/03.png)

Om du vill veta mer om hur du skapar principer för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md). Den här snabb starten hjälper dig att:

- Bekanta dig med användar gränssnittet.
- Få en första överblick över hur villkorlig åtkomst fungerar. 

### <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorlig åtkomst vill du förmodligen veta om den fungerar som förväntat. Som ett första steg använder du den villkorliga åtkomsten [vad gör om-princip verktyget](what-if-tool.md) för att simulera en inloggning av test användaren. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.

>[!NOTE]
> Medan en simulerad körning ger dig en överblick över effekten en princip för villkorlig åtkomst har, ersätter den inte en faktisk test körning.

### <a name="test-your-policy"></a>Testa principen

Kör test ärenden enligt test planen. I det här steget ska du köra genom ett slut punkt till slut punkt-test av varje princip för dina test användare för att kontrol lera att varje princip fungerar korrekt. Använd scenarierna som skapats ovan för att köra varje test.

Det är viktigt att kontrol lera att du testar undantags villkoren för en princip. Du kan till exempel utesluta en användare eller grupp från en princip som kräver MFA. Testa om de exkluderade användarna uppmanas att använda MFA, eftersom kombinationen av andra principer kan kräva MFA för dessa användare.

### <a name="cleanup"></a>Rensa

Rensnings proceduren består av följande steg:

1. Inaktivera principen.
1. Ta bort de tilldelade användarna och grupperna.
1. Ta bort test användare.  

## <a name="move-to-production"></a>Flytta till produktion

När nya principer är klara för din miljö kan du distribuera dem i faser:

- Tillhandahålla intern ändrings kommunikation till slutanvändare.
- Börja med en liten uppsättning användare och kontrol lera att principen fungerar som förväntat.
- När du expanderar en princip för att inkludera fler användare fortsätter du att undanta alla administratörer. Om du undantar administratörer ser det till att någon fortfarande har åtkomst till en princip om en ändring krävs.
- Tillämpa bara en princip för alla användare om det krävs.

Vi rekommenderar att du skapar minst ett användar konto som är:

- Dedikerad till princip administration
- Exkluderas från alla dina principer

## <a name="rollback-steps"></a>Återställnings steg

Om du behöver återställa dina nyligen implementerade principer kan du använda ett eller flera av följande alternativ för att återställa:

1. **Inaktivera principen** – om du inaktiverar en princip ser du till att den inte gäller när en användare försöker logga in. Du kan alltid komma tillbaka och aktivera principen när du vill använda den.

   ![Inaktivera princip](media/plan-conditional-access/07.png)

1. **Undanta en användare/grupp från en princip** – om en användare inte kan komma åt appen kan du välja att undanta användaren från principen

   ![Exluce-användare](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Det här alternativet bör användas sparsamt, endast i situationer där användaren är betrodd. Användaren ska läggas till i principen eller gruppen igen så snart som möjligt.

1. **Ta bort principen** – om principen inte längre krävs tar du bort den.

## <a name="next-steps"></a>Nästa steg

Se [dokumentationen för villkorlig åtkomst för Azure AD](index.yml) för att få en översikt över tillgänglig information.
