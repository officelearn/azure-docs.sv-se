---
title: Hantera användarnas åtkomst i Azure AD B2C | Microsoft Docs
description: Lär dig att identifiera minderåriga, samla in datum för födelsedatum och land data och få godkännande av villkor för användning i ditt program med hjälp av Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.openlocfilehash: c44135a3069966b14d8760e4daa009ab8d39ccca
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Hantera användarnas åtkomst i Azure AD B2C

Den här artikeln innehåller information om hur du kan hantera åtkomst till dina program med Azure Active Directory (AD) B2C. Åtkomsthantering i ditt program innehåller:

- Identifiera minderåriga och hur du styr åtkomst till ditt program
- Kräver medgivandenivå för minderåriga att använda dina program
- Datainsamlingen av födelsedatum och land data från användaren
- Använder du sparandet villkoren i avtalet och gating åtkomst

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Den här artikeln innehåller information som kan användas för att stödja din skyldigheter enligt BNPR. Om du letar efter allmän information om BNPR finns i [BNPR avsnitt av tjänsten förtroende portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Lägre behörighet

Program och organisationer kan välja att blockera minderåriga från att använda program och tjänster som inte är riktade till målgruppen. Du kan också program och organisationer kan välja att acceptera minderåriga och sedan hantera medgivandenivå och leverera tillåtna upplevelser för minderåriga enligt affärsregler och tillåts av förordning. 

Om en användare identifieras som en mindre anges användaren flödet i Azure AD B2C till ett av tre alternativ:

- **Skicka en signerad JWT id_token tillbaka till programmet** – användaren har registrerats i katalogen och en token tillbaka till programmet. Sedan fortsätter med hjälp av affärsregler. Programmet kan gå vidare med en medgivandenivå process. Om du vill göra detta måste du välja att få den **ageGroup** och **consentProvidedForMinor** anspråk från programmet.
- **Skicka en osignerad JSON-token till programmet** -Azure AD B2C meddelar programmet att användaren är en mindre och ger status för användarens medgivandenivå. Sedan fortsätter med hjälp av affärsregler. En JSON-token slutförs inte autentiseringen med programmet. Programmet måste bearbeta oautentiserad användare enligt de anspråk som ingår i JSON-token, som kan innehålla **namn**, **e-post**, **ageGroup**, och **consentProvidedForMinor**.
- **Blockera användaren** - om användaren är en mindre och medgivandenivå har inte angetts.  Azure AD B2C kan sedan presentera en skärm för användaren som informerar av blockeras.  Inga token utfärdas, blockeras åtkomst och användarkontot har inte skapats under en registration resa. Om du vill använda den här kan du ange en lämplig HTML/CSS-innehållssida för att informera användaren och finns lämpliga alternativ. Ingen ytterligare åtgärd krävs av programmet för nya registreringar.

## <a name="get-parental-consent"></a>Hämta ditt medgivande som förälder

Beroende på programmet förordning, kan det krävas medgivandenivå för att beviljas åtkomst av en användare som har verifierats som vuxen.  Azure AD B2C tillhandahåller inte en upplevelse för att kontrollera en persons ålder och tillåta en verifierad vuxen att ge ditt medgivande som förälder till ett mindre.  Det här upplevelsen måste tillhandahållas av programmet eller en annan leverantör.

Följande är ett exempel på ett flöde för användaren för att samla in föräldrars tillstånd:

1. En [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) åtgärden identifierar användare som en mindre och returnerar informationen till programmet i form av en osignerad JSON-token.
2. Programmet bearbetar JSON-token och visar en skärm som är mindre meddela den medgivandenivå krävs och begär tillstånd för en överordnad online. 
3. Azure AD B2C visar en inloggning-resa där användaren får logga in normalt och utfärdar en token till det program som anges för att inkludera **legalAgeGroupClassification = ”minorWithParentalConsent”** programmet samlar in den e-postadressen till överordnat och verifierar överordnat är en vuxen med hjälp av en betrodd källa som en nationella ID office, Licensverifiering eller bevis kreditkort. Om det lyckas, uppmanar programmet mindre att logga in med Azure AD B2C användaren flödet. Om ditt medgivande nekades (t.ex. **legalAgeGroupClassification = ”minorWithoutParentalConsent”**, Azure AD B2C returnerar en JSON-token (inte en inloggning) till programmet för att starta om processen för medgivande. Det går eventuellt att anpassa flödet användare innebär en mindre eller en vuxen kan få åtkomst till en mindre konto genom att skicka en Registreringskod till den mindre e-postadress eller det vuxen e-postadress på posten.
4. Programmet ger möjlighet att mindre att upphäva ditt medgivande.
5. När mindre eller vuxen återkallar medgivande, Azure AD Graph API kan användas för att ändra **consetProvidedForMinor** till **nekad**. Programmet kan också välja att ta bort en mindre vars godkännande har återkallats. Det går eventuellt att anpassa flödet användaren där autentiserade mindre (eller överordnade med den mindre konto) kan upphäva ditt medgivande. Azure AD B2C poster **consentProvidedForMinor** som **nekad**.

Mer information om den **legalAgeGroupClassification**, **consentProvidedForMinor**, och **ageGroup**, se [användaren resurstypen](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user). Mer information om anpassade attribut finns [Använd anpassade attribut för att samla in information om dina användare](active-directory-b2c-reference-custom-attr.md). När du adresserar utökade attribut med hjälp av Azure AD Graph API lång version av attributet måste användas, till exempel ”extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth” ”: 2011-01-01T00:00:00Z”

## <a name="gather-date-of-birth-and-country-data"></a>Samla in datum för födelsedatum och land data

Program kan förlita sig på Azure AD B2C för att samla in födelsedatum (DOB) och land från alla användare under registreringen. Om DOB eller landsinformation inte redan finns, det kan du bli uppmanad för användaren under nästa transporten authentication (logga in). Användare kommer inte att kunna fortsätta utan att ange DOB och land. Beroende på land och DOB tillhandahålls, anger Azure AD B2C om enskilda betraktas som en mindre enligt standarderna för landet i fråga. 

En anpassad flöde kan samla in DOB och landsinformation och använda Azure AD B2C anspråk omvandling att fastställa den **ageGroup** och spara resultatet (eller bevara land och DOB information direkt) i katalogen.

Följande steg visar logiken som används för att beräkna **ageGroup** från Födelsedatum:

1. Försök att hitta landet av landskoden i listan. Om det gick inte att hitta landet, återgår till att **standard**.
2. Om den **MinorConsent** noden finns i det land-elementet:  <br>a. Beräkna det tidigaste datum som användaren fick du föddes på för att ses som en vuxen. Exempel: födelsedatum är 3-14/2015 och **MinorConsent** är 18, att det minsta födelsedatum 2000-3-14.
    <br>b. Jämför minsta födelsedatumet med det faktiska födelsedatumet. Om det minsta födelsedatumet är innan användarens födelsedatum beräkningen returnerar **mindre** som ålder gruppberäkningar.
3. Om den **MinorNoConsentRequired** noden finns i land element, Upprepa steg 2a och 2b med värdet från **MinorNoConsentRequired**. Returnerar resultatet av 2b **MinorNoConsentRequired** om det minsta födelsedatumet är före användarens födelsedatum. 
4. Om varken beräkningar returnerade värdet true, beräkningen returnerar **vuxna**.

Om ett program har på ett tillförlitligt sätt insamlade DOB eller landsspecifika data med andra metoder, kan programmet använda GRAPH API för att uppdatera användarposten med den här informationen. Exempel:

- Om en användare är känt att en vuxen, uppdatera attributet directory **ageGroup** med värdet **vuxna**.
- Om en användare är känt att en mindre uppdatera attributet directory **ageGroup** med värdet **mindre** och ange **consentProvidedForMinor** efter behov.

Läs mer om datainsamling DOB [med ålder gating i Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Samla in villkor för användning

När du utvecklar programmet vanligtvis avbilda användaren godkännande av villkor för användning i sina program utan, eller endast små medverkan från användarkatalogen för.  Det är möjligt, men om du vill använda en Azure AD B2C användaren flöda till samla in licensvillkoren för villkor för användning, begränsa åtkomst om godkännande har beviljats och tillämpa godkännande av framtida ändringar användningsvillkoren baserat på datumet för senaste godkännande och datumet för senaste bakåtkompileringen n av användningsvillkoren.

**Villkor för användning av** också innefatta ”samtycker till att dela data med tredje part”.  Positivt godkännande av dessa villkor från en användare kan tekniskt samlas in som kombineras eller användaren kan ta emot en och inte en beroende på lokala föreskrifter och affärsregler.

Följande steg beskriver funktioner för hantering av villkor för användning:

1. Registrera godkännande av villkor för användning och datum för godkännande av med Graph API och utökade attribut. Detta kan göras med hjälp av både inbyggda och anpassade användaren flöden. Vi rekommenderar att du skapar och använder den **extension_termsOfUseConsentDateTime** och **extension_termsOfUseConsentVersion** attribut.
2. Skapa en obligatorisk kryssruta med namnet ”acceptera användningsvillkoren” och registrera resultatet under registreringen. Detta kan göras med hjälp av både inbyggda och anpassade användaren flöden.
3. Azure AD B2C lagrar villkoren för användning och samtycke. Graph API kan användas för att fråga efter status för alla användare genom att läsa attributet för anknytning som används för att registrera svaret, till exempel läsa **termsOfUseTestUpdateDateTime**. Detta kan göras med hjälp av både inbyggda och anpassade användaren flöden.
4. Kräv godkännande av uppdaterade användningsvillkoren genom att jämföra dag då datumet för den senaste versionen av användningsvillkoren. Detta kan endast göras med hjälp av en anpassad användare flöde. Använd det utökade attributet **extension_termsOfUseConsentDateTime** och jämför värdet till anspråk av **termsOfUseTextUpdateDateTime**om godkännande är gammal och framtvinga sedan en ny godkännande automatisk vars skärmen, annars att blockera åtkomst med hjälp av principlogik.
5. Kräv godkännande av uppdaterade användningsvillkoren genom att jämföra versionsnumret för godkännande till senaste godkända versionsnummer. Detta kan endast göras med hjälp av en anpassad användare flöde. Använd det utökade attributet **extension_termsOfUseConsentDateTime** och jämför värdet till anspråk av **extension_termsOfUseConsentVersion**om godkännande är gammal och framtvinga sedan en ny godkännande automatisk vars skärmen, annars att blockera åtkomst med hjälp av principlogik.

Samla in villkor för användning samtycke kan visas för användaren under följande scenarier:

- En ny användare loggar. Användningsvillkoren visas resultatet medgivande lagras.
- En användare loggar in och redan tidigare har accepterat ditt medgivande för senaste eller aktiva villkoren i avtalet. Villkor för användning visas inte.
- En användare loggar in och har redan accepterades inte ditt medgivande för senaste eller aktiva användningsvillkoren. Användningsvillkoren visas resultatet medgivande lagras.
- En användare loggar in och har redan accepterats godkännande för en äldre villkor för användning som har uppdaterats till en senare version. Användningsvillkoren visas resultatet medgivande lagras.

Följande bild visar rekommenderade användaren flöde:

![flöde för godkännande av användare](./media/manage-user-access/user-flow.png) 

Följande är ett exempel på en DateTime baserat villkor för godkännande för användning i ett anspråk:

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

Följande är ett exempel på en Version baserat villkor för godkännande för användning i ett anspråk:

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

- Lär dig hur du tar bort och exportera användardata i [hanterar användardata](manage-user-data.md)
