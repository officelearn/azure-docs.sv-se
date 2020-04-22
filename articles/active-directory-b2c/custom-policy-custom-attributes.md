---
title: Lägga till egna attribut i anpassade principer
titleSuffix: Azure AD B2C
description: En genomgång av hur du använder tilläggsegenskaper och anpassade attribut och inkluderar dem i användargränssnittet.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b5990f79891a9cbc0d18c3499691a3d7ef309a73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678259"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: Aktivera anpassade attribut i en anpassad profilprincip

I artikeln [Lägg till anspråk och anpassa användarindata med hjälp av anpassade principer](custom-policy-configure-user-input.md) får du lära dig hur du använder inbyggda attribut för [användarprofiler](user-profile-attributes.md). I den här artikeln aktiverar du ett anpassat attribut i din Azure Active Directory B2C -katalog (Azure AD B2C). Senare kan du använda det nya attributet som ett anpassat anspråk i [användarflöden](user-flow-overview.md) eller [anpassade principer](custom-policy-get-started.md) samtidigt.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Krav

Följ stegen i artikeln [Azure Active Directory B2C: Kom igång med anpassade principer](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Använda anpassade attribut för att samla in information om dina kunder 

Din Azure AD B2C-katalog levereras med en [inbyggd uppsättning attribut](user-profile-attributes.md). Du måste dock ofta skapa egna attribut för att hantera ditt specifika scenario, till exempel när:

* Ett kundinriktadt program måste bevara ett **LoyaltyId-attribut.**
* En identitetsprovider har en unik användaridentifierare, **uniqueUserGUID**, som måste bevaras.
* En anpassad användarfärd måste bevara användarens tillstånd, **migrationStatus,** för annan logik att fungera på.

Med Azure AD B2C kan du utöka uppsättningen attribut som lagras på varje användarkonto. Du kan också läsa och skriva dessa attribut med hjälp av [Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C-tillägg app

Tilläggsattribut kan bara registreras på ett programobjekt, även om de kan innehålla data för en användare. Tilläggets attribut är kopplat till programmet som kallas b2c-extensions-app. Ändra inte det här programmet eftersom det används av Azure AD B2C för lagring av användardata. Du hittar det här programmet under Azure AD B2C, appregistreringar.

Termernas *tilläggsegenskap,* *anpassade attribut*och anpassade *anspråk* refererar till samma sak i den här artikeln. Namnet varierar beroende på kontexten, till exempel program, objekt eller princip.

## <a name="get-the-application-properties"></a>Hämta programegenskaperna

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsversion)** och välj sedan **Alla program**.
1. Välj `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` programmet.
1. Kopiera följande identifierare till Urklipp och spara dem:
    * **Program-ID**. Exempel: `11111111-1111-1111-1111-111111111111`.
    * **Objekt-ID**. Exempel: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Ändra din anpassade princip

Om du vill aktivera anpassade attribut i principen anger du **program-ID** och **programobjekt-ID** i AAD-Commons tekniska profilmetadata. Den tekniska profilen *AAD-Common* finns i den grundläggande tekniska profilen för [Azure Active Directory](active-directory-technical-profile.md) och ger support för Azure AD-användarhantering. Andra tekniska Azure AD-profiler inkluderar AAD-Common för att utnyttja dess konfiguration. Åsidosätt den tekniska profilen AAD-Common i tilläggsfilen.

1. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Leta reda på elementet ClaimsProviders. Lägg till en ny ClaimsProvider till elementet ClaimsProviders.
1. Ersätt `ApplicationObjectId` med det objekt-ID som du tidigare spelade in. Ersätt `ClientId` sedan med det program-ID som du tidigare har spelat in i kodavsnittet nedan.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Ladda upp din anpassade princip

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD B2C-klient.
3. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
4. Välj **Identity Experience Framework**.
5. Välj **Ladda upp anpassad princip**och ladda sedan upp principfilerna TrustFrameworkExtensions.xml som du har ändrat.

> [!NOTE]
> Första gången den tekniska profilen för Azure AD kvarstår anspråket på katalogen kontrollerar den om det anpassade attributet finns. Om inte, skapas det anpassade attributet.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Skapa ett anpassat attribut via Azure Portal

Samma tilläggsattribut delas mellan inbyggda och anpassade principer. När du lägger till anpassade attribut via portalupplevelsen registreras dessa attribut med hjälp av **b2c-tillägg-appen** som finns i varje B2C-klientorganisation.

Du kan skapa dessa attribut med hjälp av portalgränssnittet före eller efter att du använder dem i dina anpassade principer. Följ vägledningen för hur [du definierar anpassade attribut i Azure Active Directory B2C](user-flow-custom-attributes.md). När du skapar en attribute **loyaltyId** i portalen måste du referera till det på följande sätt:

|Namn     |Används i |
|---------|---------|
|`extension_loyaltyId`  | Anpassad princip|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph-API](manage-user-accounts-graph-api.md)|

I följande exempel visas användningen av anpassade attribut i en azure AD B2C-anpassad principanspråksdefinition.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

I följande exempel visas användningen av ett anpassat attribut i Azure AD B2C-anpassad princip i en teknisk profil, indata, utdata och beständiga anspråk.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Använda ett anpassat attribut i en princip

Följ vägledningen för hur du lägger till [anspråk och anpassar användarindata med hjälp av anpassade principer](custom-policy-configure-user-input.md). I det här exemplet används en inbyggd anspråksstad. Om du vill använda ett anpassat attribut ersätter du "city" med dina egna anpassade attribut.


## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure AD B2C-användarprofilattribut](user-profile-attributes.md)
- [Definition av tilläggsattribut](user-profile-attributes.md#extension-attributes)
- [Hantera Azure AD B2C-användarkonton med Microsoft Graph](manage-user-accounts-graph-api.md)
