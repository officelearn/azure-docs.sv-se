---
title: Konfigurera resursägarens lösenord autentiseringsuppgifter flöde i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar resursägarens lösenord autentiseringsuppgifter flöde i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7dffa1480be73f1dbf5e99d11fd8d33eb2ab9038
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196420"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Konfigurera resursägarens lösenord autentiseringsuppgifter flöde i Azure Active Directory B2C med hjälp av en anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory (Azure AD) B2C är resursägarens lösenord autentiseringsuppgifter (ROPC) flöde en flöde för OAuth-standardautentisering. I det här flödet utbyter ett program, även kallat den förlitande parten, giltiga autentiseringsuppgifter för token. Autentiseringsuppgifterna som innehåller användar-ID och lösenord. De token som returneras är en ID-token, åtkomst-token och en uppdateringstoken. 

Följande alternativ kan användas i flödet ROPC:

- **Native Client** -interaktion från användaren under autentiseringen sker när koden körs på en enhet för användaren sida.
- **Offentliga klientflödet** -bara användaren autentiseringsuppgifter som har samlats in av ett program som skickas i API-anrop. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägga till nya anspråk** -ID-token innehållet kan ändras för att lägga till nya anspråk. 

Följande flöden stöds inte:

- **Server-till-server** -identity protection systemet behöver en tillförlitlig IP-adress som samlats in från anroparen (intern klient) som en del av interaktionen. I ett API-anrop för serversidan används bara serverns IP-adress. Om för många inloggningar misslyckas, kan identity protection-system titta på en upprepad IP-adress som en angripare.
- **Enkel sidprogram** -ett frontend-program som är huvudsakligen skrivna i JavaScript. Programmet skrivs ofta med hjälp av ett ramverk som AngularJS, Ember.js eller Durandal.
- **Konfidentiellt klientflödet** - programmets klient-ID har verifierats, men programhemlighet inte.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrera ett program

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**. 
4. Välj **program**, och välj sedan **Lägg till**.
5. Ange ett namn för programmet, till exempel *ROPC_Auth_app*.
6. Välj **nr** för **Web App/Web API**, och välj sedan **Ja** för **Native client**.
7. Lämna övriga värden som de är och välj sedan **skapa**.
8. Välj det nya programmet och registrera program-ID för senare användning.

##  <a name="create-a-resource-owner-policy"></a>Skapa en princip för resurs-ägare

1. Öppna den *TrustFrameworkExtensions.xml* fil.
2. Om den inte redan finns, lägger du till en **ClaimsSchema** elementet och dess underordnade element som det första elementet under den **BuildingBlocks** element:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Efter **ClaimsSchema**, lägga till en **ClaimsTransformations** elementet och dess underordnade element till den **BuildingBlocks** element:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Leta upp den **ClaimsProvider** element som har en **DisplayName** av `Local Account SignIn` och lägga till följande tekniska profil:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Ersätt den **DefaultValue** av **client_id** och **resursid** med program-ID för ProxyIdentityExperienceFramework programmet som du skapade i den kursen.

5. Lägg till följande **ClaimsProvider** element med sin tekniska profiler till den **ClaimsProviders** element:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>    
    ```

6. Lägg till en **UserJourneys** elementet och dess underordnade element till den **TrustFrameworkPolicy** element:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
        <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
8. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
9. Klicka på **Överför**.

## <a name="create-a-relying-party-file"></a>Skapa en förlitande part-fil

Därefter uppdatera filen för förlitande part som initierar användarresa som du skapade:

1. Skapa en kopia av *SignUpOrSignin.xml* filen i arbetskatalogen och Byt namn på den till *ROPC_Auth.xml*.
2. Öppna den nya filen och ändra värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** till ett unikt värde. Princip-ID är namnet på din princip. Till exempel **B2C_1A_ROPC_Auth**.
3. Ändra värdet för den **ReferenceId** attributet i **DefaultUserJourney** till `ResourceOwnerPasswordCredentials`.
4. Ändra den **OutputClaims** elementet så att den bara innehåller följande anspråk:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
6. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
7. Klicka på **Överför**.

## <a name="test-the-policy"></a>Testa principen

Använd din favorit-API-program för utveckling för att generera ett API-anrop och granska svaret för att felsöka din princip. Skapa ett anrop som i följande exempel med följande information som en del av POST-begäran:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient.
- Ersätt `B2C_1A_ROPC_Auth` med det fullständiga namnet på din resurs ägare autentiseringsuppgifter lösenordsprincip.

| Nyckel | Value |
| --- | ----- |
| användarnamn | `user-account` |
| lösenord | `password1` |
| _typ av beviljande | lösenord |
| omfång | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Ersätt `user-account` med namnet på ett användarkonto i din klient.
- Ersätt `password1` med lösenordet för användarkontot.
- Ersätt `application-id` med program-ID från den *ROPC_Auth_app* registrering. 
- *Offline_access* är valfritt om du vill få en uppdateringstoken.

Den faktiska POST-begäran som ser ut som i följande exempel:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Ett lyckat svar med offlineåtkomst ser ut som i följande exempel:

```JSON
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..." 
} 
```

## <a name="redeem-a-refresh-token"></a>Lösa in en uppdateringstoken

Skapa en POST-anrop som den som visas här. Använd informationen i följande tabell som en del av begäran:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient.
- Ersätt `B2C_1A_ROPC_Auth` med det fullständiga namnet på din resurs ägare autentiseringsuppgifter lösenordsprincip.

| Nyckel | Value |
| --- | ----- |
| _typ av beviljande | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resurs | `application-id` |
| refresh_token | `refresh-token` |

- Ersätt `application-id` med program-ID från den *ROPC_Auth_app* registrering.
- Ersätt `refresh-token` med den **refresh_token** som skickades i föregående svar. 

Ett lyckat svar ser ut som i följande exempel:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Använda en intern SDK eller App Auth

Azure AD B2C uppfyller OAuth 2.0-standarder för lösenord-autentiseringsuppgifterna för offentlig klient-ägare och bör vara kompatibla med de flesta klient-SDK: er. Den senaste informationen finns i [inbyggd App-SDK för OAuth 2.0 och OpenID Connect implementera moderna metodtips](https://appauth.io/).

## <a name="next-steps"></a>Nästa steg

- Se ett fullständigt exempel på det här scenariot i den [startpaket för Azure Active Directory B2C-anpassad princip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Mer information om de token som används av Azure Active Directory B2C i den [Token referens](active-directory-b2c-reference-tokens.md).


