---
title: Hantera användarnas åtkomst i Azure Active Directory B2C | Microsoft Docs
description: Lär dig att identifiera minderåriga, samla in datum födelsedatum och land data och få godkännande av villkor för användning i ditt program med hjälp av Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6709fb8ae328f749b367c58f95b8a9ef8da9bc65
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055629"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Hantera användarnas åtkomst i Azure Active Directory B2C

Den här artikeln beskrivs hur du hanterar användarnas åtkomst till dina program med hjälp av Azure Active Directory (Azure AD) B2C. Hantering av ditt program innehåller:

- Identifiera minderåriga och styra användarnas åtkomst till ditt program.
- Kräver att medgivandenivå för minderåriga använda dina program.
- Samla in födelsedatum och land data från användare.
- Samla in ett avtal för villkor för användning och hantera åtkomsten.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Lägre behörighet

Program och organisationer kan välja att blockera minderåriga från att använda program och tjänster som inte är riktade till målgruppen. Du kan också program och organisationer kan välja att acceptera minderåriga och därefter hantera föräldrars tillstånd och leverera tillåtna upplevelser för minderåriga enligt affärsregler och tillåts av förordning. 

Om en användare identifieras som en mindre, kan du ange användarflödet i Azure AD B2C till något av tre alternativ:

- **Skicka en signerad JWT-id_token tillbaka till programmet**: användaren har registrerats i katalogen och en token returneras till programmet. Sedan fortsätter genom att använda affärsregler. Exempelvis kan programmet fortsätta med en medgivandenivå process. Om du vill använda den här metoden måste du välja att ta emot den **ageGroup** och **consentProvidedForMinor** anspråk från programmet.

- **Skicka en osignerad JSON-token till programmet**: Azure AD B2C meddelar programmet att användaren är en mindre och som visar status för användarens föräldrars tillstånd. Sedan fortsätter genom att använda affärsregler. En JSON-token inte att slutföra en lyckad autentisering med programmet. Programmet måste bearbeta oautentiserad användare enligt de anspråk som ingår i JSON-token som kan innehålla **namn**, **e-post**, **ageGroup**, och **consentProvidedForMinor**.

- **Blockera användaren**: om en användare är en mindre och föräldrars tillstånd har inte angetts, Azure AD B2C kan meddela användaren att han eller hon är blockerad. Ingen token utfärdas, blockeras åtkomsten och användarkontot har inte skapats under en registrerings-resa. För att implementera det här meddelandet, kan du ange en lämplig HTML/CSS-innehållssida för att informera användaren och finns lämpliga alternativ. Ingen ytterligare åtgärd krävs av programmet för nya registreringar.

## <a name="get-parental-consent"></a>Hämta föräldrars tillstånd

Beroende på program förordning behöva medgivandenivå beviljas av en användare som har verifierats som vuxen. Azure AD B2C tillhandahåller inte en upplevelse för att verifiera en persons ålder och låt en verifierad vuxen du beviljar minderårig föräldrars tillstånd. Den här upplevelsen måste tillhandahållas med programmet eller en annan tjänstleverantör.

Följande är ett exempel på ett användarflöde för att samla in föräldrars tillstånd:

1. En [Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog) åtgärden identifierar användaren som minderårig och returnerar användarens data till programmet i form av en osignerad JSON-token.

2. Programmet bearbetar JSON-token och visar en skärm till mindre, meddela honom eller henne att föräldrars tillstånd krävs och begära medgivande från en överordnad online. 

3. Azure AD B2C visar en inloggning resa att användaren kan logga in på normalt och utfärdar en token till det program som har angetts att inkludera **legalAgeGroupClassification = ”minorWithParentalConsent”**. Programmet samlar in e-postadressen till överordnat och kontrollerar att överordnat är en vuxen. Om du vill göra det använder en betrodd källa, till exempel en nationell ID office, Licensverifiering eller kreditkort bevis. Om verifieringen lyckas uppmanar programmet mindre att logga in med hjälp av Azure AD B2C-användarflödet. Om medgivande nekas (till exempel om **legalAgeGroupClassification = ”minorWithoutParentalConsent”**), Azure AD B2C returnerar en JSON-token (inte en inloggning) i program för att starta om processen för medgivande. Det är också möjligt att anpassa användarflödet så att en mindre eller en vuxen kan få åtkomst till en mindre konto genom att skicka en Registreringskod till det mindre e-postadress eller det vuxen e-postadress på posten.

4. Programmet ger möjlighet till mindre att återkalla medgivande.

5. När mindre eller vuxen återkallar ditt medgivande, Azure AD Graph API kan användas för att ändra **consentProvidedForMinor** till **nekas**. Programmet kan också välja att ta bort minderårig vars godkännande har återkallats. Det är också möjligt att anpassa användarflödet så att den autentiserade mindre (eller överordnade som använder den mindre konto) kan återkalla medgivande. Azure AD B2C-poster **consentProvidedForMinor** som **nekas**.

Mer information om **legalAgeGroupClassification**, **consentProvidedForMinor**, och **ageGroup**, se [användarresurstyp](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Läs mer om anpassade attribut, [Använd anpassade attribut för att samla in information om dina användare](active-directory-b2c-reference-custom-attr.md). När du har gått utökade attribut med hjälp av Azure AD Graph-API måste du använda den långa versionen av attributet, som *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-country-data"></a>Samla in datumet för födelsedatum och land

Program kan förlita sig på Azure AD B2C för att samla in födelsedatum (DOB) och landspecifik information från alla användare under registreringen. Om den här informationen inte redan finns, kan programmet begära den från användaren under nästa authentication (logga in) resan. Användare kan inte fortsätta utan att ange sina DOB och land. Azure AD B2C använder informationen för att avgöra om enskilda betraktas som minderårig enligt regelverk i det landet. 

En anpassad användarflödet kan samla in DOB och landspecifik information och använda Azure AD B2C anspråk omvandlingen att fastställa den **ageGroup** och spara resultatet (eller kvar DOB och landspecifik information direkt) i katalogen.

Följande steg visar logiken som används för att beräkna **ageGroup** från användarens Födelsedatum:

1. Försök att hitta landet genom landskoden i listan. Om landet inte hittas, återgår till att **standard**.

2. Om den **MinorConsent** noden finns i det land-elementet:

    a. Beräkna det datum då användaren måste vara född på för att anses vara en vuxen. Exempel: om dagens datum är 14 mars 2015 och **MinorConsent** är 18, födelsedatumet måste vara senare än 14 mars 2000.

    b. Jämför det minsta födelsedatumet med det faktiska födelsedatumet. Om är det minsta födelsedatumet innan användarens födelsedatum, beräkningen returnerar **mindre** som åldersgrupp beräkningen.

3. Om den **MinorNoConsentRequired** noden finns i land element, upprepar du steg 2a och 2b med värdet från **MinorNoConsentRequired**. Returnerar resultatet av 2b **MinorNoConsentRequired** om det minsta födelsedatumet är före användarens födelsedatum. 

4. Om varken beräkningen returneras true, beräkningen returnerar **vuxet**.

Om ett program har på ett tillförlitligt sätt insamlade DOB eller landdata med andra metoder, kan programmet använda Graph API för att uppdatera användarposten med den här informationen. Exempel:

- Om en användare är känd måste vara vuxen, uppdatera attributet directory **ageGroup** med värdet **vuxet**.
- Om en användare har visat sig vara minderårig, uppdatera attributet directory **ageGroup** med värdet **mindre** och ange **consentProvidedForMinor**efter behov.

Läs mer om datainsamling DOB [använda åldershantering i Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Samla in villkor för användning

När du utvecklar ditt program kan du avbilda normalt användarnas godkännande av villkor för användning i sina program utan eller endast små deltagare från katalogen för användaren. Det är dock möjligt att använda en Azure AD B2C-användarflödet att samla in en användares godkännande av användningsvillkor, begränsa åtkomst om godkännande inte beviljas och genomdriva godkännande av användningsvillkor, baserat på datumet för senaste godkännande och datumet för framtida ändringar i  senaste versionen av användningsvillkoren.

**Användningsvillkor** också innefatta ”samtycker till att dela data med tredje part”. Du kan samla in en användares godkännande av båda villkoren kombineras beroende på lokala föreskrifter och affärsregler, eller du kan tillåta att användaren godkänner ett villkor och inte på den andra.

Följande steg beskriver hur du kan hantera användningsvillkor:

1. Registrera godkännande av villkor för användning och dag då med hjälp av Graph API och utökade attribut. Du kan göra det med hjälp av både inbyggda och anpassade användarflöden. Vi rekommenderar att du skapar och använder den **extension_termsOfUseConsentDateTime** och **extension_termsOfUseConsentVersion** attribut.

2. Skapa en obligatorisk kryssruta med namnet ”godkänna användningsvillkoren” och registrera resultatet under registreringen. Du kan göra det med hjälp av både inbyggda och anpassade användarflöden.

3. Azure AD B2C lagrar villkoren i avtalet för användning och användarens godkännande. Du kan använda Graph-API för att fråga för status för alla användare genom att läsa attributet anknytning som används för att registrera svaret (till exempel läsa **termsOfUseTestUpdateDateTime**). Du kan göra det med hjälp av både inbyggda och anpassade användarflöden.

4. Kräv godkännande av uppdaterade användningsvillkoren genom att jämföra dag då att datumet för den senaste versionen av användningsvillkoren. Du kan jämföra datum med ett anpassat användarflöde. Använda EA **extension_termsOfUseConsentDateTime**, och jämför värdet till anspråk av **termsOfUseTextUpdateDateTime**. Framtvinga ett nytt godkännande genom att visa en självkontrollerad skärm om godkännande är gammal. Annars kan blockera åtkomst med hjälp av principlogik.

5. Kräv godkännande av uppdaterade användningsvillkoren genom att jämföra det lägre versionsnumret för godkännande till den senaste godkända versionsnumret. Du kan jämföra versionsnummer med ett anpassat användarflöde. Använda EA **extension_termsOfUseConsentDateTime**, och jämför värdet till anspråk av **extension_termsOfUseConsentVersion**. Framtvinga ett nytt godkännande genom att visa en självkontrollerad skärm om godkännande är gammal. Annars kan blockera åtkomst med hjälp av principlogik.

Du kan avbilda användningsvillkor Använd godkännande under följande scenarier:

- En ny användare loggar. Användningsvillkoren visas och godkännande resultatet lagras.
- En användare loggar in som tidigare har accepterat användningsvillkoren för senaste eller aktiv. Användningsvillkoren visas inte.
- En användare loggar in som inte har redan accepterat användningsvillkoren för senaste eller aktiv. Användningsvillkoren visas och godkännande resultatet lagras.
- En användare loggar in som redan har godkänt en äldre version av villkoren, som nu har uppdaterats till den senaste versionen. Användningsvillkoren visas och godkännande resultatet lagras.

Följande bild visar rekommenderade användarflödet:

![Policy för godkännande](./media/manage-user-access/user-flow.png) 

Följande är ett exempel på en DateTime-baserade villkor medgivande för användningsvillkor i ett anspråk:

```
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

Följande är ett exempel på en Version baserad villkoren medgivande för användningsvillkor i ett anspråk:

```
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

- Läs hur du tar bort och exportera användardata i [hanterar användardata](manage-user-data.md).
