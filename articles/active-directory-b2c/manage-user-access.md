---
title: Hantera användaråtkomst i Azure Active Directory B2C | Microsoft-dokument
description: Lär dig hur du identifierar minderåriga, samlar in födelsedatum och land-/regiondata och godkänner användarvillkor i ditt program med hjälp av Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f04a3fea3801f917a3ae4aced04ef3824d1cfa82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184527"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Hantera användaråtkomst i Azure Active Directory B2C

I den här artikeln beskrivs hur du hanterar användaråtkomst till dina program med hjälp av Azure Active Directory B2C (Azure AD B2C). Åtkomsthantering i ditt program omfattar:

- Identifiera minderåriga och kontrollera användarnas åtkomst till ditt program.
- Kräver föräldrarnas samtycke för minderåriga att använda dina program.
- Samla in uppgifter om födelse och land/region från användarna.
- Fånga ett villkor för användningsavtalet och ge åtkomst.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kontrollera mindre åtkomst

Program och organisationer kan besluta att blockera minderåriga från att använda program och tjänster som inte är riktade till den här målgruppen. Alternativt kan ansökningar och organisationer besluta att acceptera minderåriga och därefter hantera föräldrarnas samtycke, och leverera tillåtna erfarenheter för minderåriga som dikteras av affärsregler och tillåts av förordning.

Om en användare identifieras som minderårig kan du ange användarflödet i Azure AD B2C till ett av tre alternativ:

- **Skicka en signerad JWT id_token tillbaka till programmet**: Användaren är registrerad i katalogen och en token returneras till programmet. Ansökan fortsätter sedan genom att tillämpa affärsregler. Programmet kan till exempel fortsätta med en föräldramedgivandeprocess. Om du vill använda den här metoden väljer du att ta emot **ageGroup-** och **consentProvidedForMinor-anspråk** från programmet.

- **Skicka en osignerad JSON-token till programmet**: Azure AD B2C meddelar programmet att användaren är minderårig och ger status för användarens föräldramedgivande. Ansökan fortsätter sedan genom att tillämpa affärsregler. En JSON-token slutför inte en lyckad autentisering med programmet. Programmet måste bearbeta den oautentiserade användaren enligt de anspråk som ingår i JSON-token, som kan innehålla **namn,** **e-post,** **ageGroup**och **consentProvidedForMinor**.

- **Blockera användaren**: Om en användare är minderårig och föräldrarnas medgivande inte har tillhandahållits kan Azure AD B2C meddela användaren att de är blockerade. Ingen token har utfärdats, åtkomsten blockeras och användarkontot skapas inte under en registreringsresa. Om du vill implementera det här meddelandet tillhandahåller du en lämplig HTML/CSS-innehållssida för att informera användaren och presentera lämpliga alternativ. Ansökan om nya registreringar behöver inte vidta några ytterligare åtgärder.

## <a name="get-parental-consent"></a>Få föräldrarnas samtycke

Beroende på tillämpningsförordningen kan föräldrarnas samtycke behöva beviljas av en användare som verifieras som vuxen. Azure AD B2C ger ingen upplevelse för att verifiera en persons ålder och tillåter sedan en verifierad vuxen att ge föräldrarnas medgivande till en minderårig. Den här upplevelsen måste tillhandahållas av programmet eller en annan tjänsteleverantör.

Följande är ett exempel på ett användarflöde för att samla in föräldrarnas samtycke:

1. En [Microsoft Graph API-åtgärd](https://docs.microsoft.com/graph/use-the-api) identifierar användaren som en minderårig och returnerar användardata till programmet i form av en osignerad JSON-token.

2. Programmet bearbetar JSON-token och visar en skärm för den minderårige, meddelar dem att föräldrarnas samtycke krävs och begär samtycke från en förälder online.

3. Azure AD B2C visar en inloggningsresa som användaren kan logga in på normalt och utfärdar en token till programmet som är inställt på att inkludera **legalAgeGroupClassification = "minorWithParentalConsent"**. Programmet samlar in den överordnade förälderns e-postadress och verifierar att den överordnade är en vuxen. För att göra det används en betrodd källa, till exempel ett nationellt ID-kontor, licensverifiering eller kreditkortsbevis. Om verifieringen lyckas uppmanar programmet den minderårige att logga in med hjälp av Azure AD B2C-användarflödet. Om samtycke nekas (till exempel om **legalAgeGroupClassification = "minorWithoutParentalConsent"**), returnerar Azure AD B2C en JSON-token (inte en inloggning) till programmet för att starta om medgivandeprocessen. Det är eventuellt möjligt att anpassa användarflödet så att en minderårig eller en vuxen kan återfå åtkomsten till en minderårigs konto genom att skicka en registreringskod till den minderåriges e-postadress eller den vuxnas e-postadress som registrerats.

4. Ansökan erbjuder en möjlighet för den minderårige att återkalla samtycket.

5. När antingen den minderårige eller den vuxne återkallar samtycke kan Microsoft Graph API användas för att ändra **samtyckeFörminor** att **nekas**. Alternativt kan programmet välja att ta bort en minderårig vars samtycke har återkallats. Det är eventuellt möjligt att anpassa användarflödet så att den autentiserade minderårign (eller föräldern som använder den minderåriges konto) kan återkalla samtycket. Azure AD B2C-poster **medgivandeTillförMinor** **nekas**.

Mer information om **legalAgeGroupClassification**, **consentProvidedForMinor**och **ageGroup**finns i [Resurstyp för användare](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Mer information om anpassade attribut finns i [Använda anpassade attribut för att samla in information om dina konsumenter](user-flow-custom-attributes.md). När du adresserar utökade attribut med hjälp av Microsoft Graph API måste du använda den långa versionen av attributet, till exempel *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth:* *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Samla in födelsedatum och lands-/regiondata

Program kan förlita sig på Azure AD B2C för att samla in födelsedatum (DOB) och land/region information från alla användare under registreringen. Om den här informationen inte redan finns kan programmet begära den från användaren under nästa autentiseringsresa (inloggning). Användare kan inte fortsätta utan att ange sin DOB och land / region information. Azure AD B2C använder informationen för att avgöra om personen betraktas som minderårig enligt de lagstadgade standarderna i det landet/regionen.

Ett anpassat användarflöde kan samla in DOB- och lands-/regioninformation och använda Azure AD B2C-anspråkstransformation för att fastställa **ageGroup** och bevara resultatet (eller bevara DOB- och lands-/regioninformationen direkt) i katalogen.

Följande steg visar den logik som används för att beräkna **ageGroup** från användarens födelsedatum:

1. Försök att hitta landet efter landskoden i listan. Om landet inte hittas går du tillbaka till **Standard**.

2. Om **noden MinorConsent** finns i landselementet:

    a. Beräkna det datum då användaren måste ha fötts för att betraktas som vuxen. Om det aktuella datumet till exempel är den 14 mars 2015 och **MinorConsent** är 18, måste födelsedatumet vara senast den 14 mars 2000.

    b. Jämför det minsta födelsedatumet med det faktiska födelsedatumet. Om det minsta födelsedatumet inbetals före användarens födelsedatum returneras **Mindre** som åldersgruppsberäkning.

3. Om **noden MinorNoConsentRequired** finns i landselementet upprepar du steg 2a och 2b med värdet från **MinorNoConsentRequired**. Utdata från 2b returnerar **MinorNoConsentRequired** om det minsta födelsedatumet är före användarens födelsedatum.

4. Om ingen av beräkningarna returnerar sant returnerar beräkningen **Vuxen**.

Om ett program på ett tillförlitligt sätt har samlat in DOB- eller lands-/regiondata med andra metoder kan programmet använda Graph API för att uppdatera användarposten med den här informationen. Ett exempel:

- Om en användare är känd för att vara vuxen uppdaterar du katalogattributet **ageGroup** med värdet **Vuxen**.
- Om en användare är känd för att vara minderårig uppdaterar du katalogattributet **ageGroup** med värdet **Minor** och ange **medgivandeFörminor**, beroende på vad som är lämpligt.

Mer information om hur du samlar in DOB-data finns [i Använda åldersbering i Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Capture villkor för användning avtal

När du utvecklar ditt program, du vanligtvis fånga användarnas godkännande av användarvillkor inom sina program utan, eller endast mindre, deltagande från användarkatalogen. Det är dock möjligt att använda ett Azure AD B2C-användarflöde för att samla in en användares godkännande av användarvillkor, begränsa åtkomsten om godkännande inte beviljas och genomdriva godkännande av framtida ändringar av användarvillkoren, baserat på datumet för det senaste godkännandet och datumet för den senaste versionen av användarvillkoren.

**Användarvillkoren** kan också innehålla "Samtycke till att dela data med tredje part". Beroende på lokala bestämmelser och affärsregler kan du samla en användares godkännande av båda villkoren tillsammans, eller så kan du tillåta användaren att acceptera ett villkor och inte det andra.

I följande steg beskrivs hur du kan hantera användningsvillkor:

1. Registrera godkännandet av användarvillkoren och datum för godkännande med hjälp av Graph API och utökade attribut. Du kan göra det genom att använda både inbyggda och anpassade användarflöden. Vi rekommenderar att du skapar och använder **extension_termsOfUseConsentDateTime** och **extension_termsOfUseConsentVersion** attribut.

2. Skapa en obligatorisk kryssruta med etiketten "Acceptera användarvillkor" och registrera resultatet under registreringen. Du kan göra det genom att använda både inbyggda och anpassade användarflöden.

3. Azure AD B2C lagrar användarvillkoren och användarens godkännande. Du kan använda Graph API för att fråga efter status för alla användare genom att läsa tillägget attribut som används för att registrera svaret (till exempel läsa **termsOfUseTestUpdateDateTime**). Du kan göra det genom att använda både inbyggda och anpassade användarflöden.

4. Kräv godkännande av uppdaterade användarvillkor genom att jämföra datum för godkännande till datumet för den senaste versionen av användarvillkoren. Du kan bara jämföra datumen med hjälp av ett anpassat användarflöde. Använd det utökade attributet **extension_termsOfUseConsentDateTime**och jämför värdet med anspråk på **termsOfUseTextUpdateDateDateTime**. Om godkännandet är gammalt tvingar du fram ett nytt godkännande genom att visa en självbekände skärm. Annars blockerar du åtkomst med hjälp av principlogik.

5. Kräv godkännande av uppdaterade användningsvillkor genom att jämföra versionsnumret för godkännandet med det senaste accepterade versionsnumret. Du kan bara jämföra versionsnummer med hjälp av ett anpassat användarflöde. Använd det utökade attributet **extension_termsOfUseConsentDateTime**och jämför värdet med anspråk på **extension_termsOfUseConsentVersion**. Om godkännandet är gammalt tvingar du fram ett nytt godkännande genom att visa en självbekände skärm. Annars blockerar du åtkomst med hjälp av principlogik.

Du kan samla in användarvillkor godkännande under följande scenarier:

- En ny användare registrerar sig. Användningsvillkoren visas och acceptansresultatet lagras.
- En användare loggar in som tidigare har accepterat de senaste eller aktiva användarvillkoren. Användningsvillkoren visas inte.
- En användare loggar in som inte redan har accepterat de senaste eller aktiva användarvillkoren. Användningsvillkoren visas och acceptansresultatet lagras.
- En användare loggar in som redan har accepterat en äldre version av användarvillkoren, som nu uppdateras till den senaste versionen. Användningsvillkoren visas och acceptansresultatet lagras.

Följande bild visar det rekommenderade användarflödet:

![Flödesdiagramdiagram som visar det rekommenderade användarflödet för godkännande](./media/manage-user-access/user-flow.png)

Följande är ett exempel på ett DateTime-baserat villkor för användningsgodkännande i ett anspråk:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Följande är ett exempel på ett versionsbaserat användningsvillkor i ett anspråk:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du tar bort och exporterar användardata finns i [Hantera användardata](manage-user-data.md).
- En anpassad princip som implementerar ett exempel på en fråga om användning finns [i en anpassad B2C IEF-policy - Registrera dig och logga in med uppmaningen "Användarvillkor".](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou)
