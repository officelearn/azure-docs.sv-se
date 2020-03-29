---
title: Anpassade e-postverifieringar
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar verifieringsmeddelandet som skickas till dina kunder när de registrerar sig för att använda dina Azure AD B2C-aktiverade program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671637"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Anpassad e-postverifiering i Azure Active Directory B2C

Använd anpassad e-post i Azure Active Directory B2C (Azure AD B2C) för att skicka anpassad e-post till användare som registrerar sig för att använda dina program. Genom att använda [DisplayControls](display-controls.md) (för närvarande i förhandsversion) och en tredjepartsleverantör av e-post kan du använda din egen e-postmall och *Från:* adress och ämne, samt stödja lokalisering och anpassade engångslösenord (OTP) inställningar.

Anpassad e-postverifiering kräver användning av en tredjeparts e-postleverantör som [SendGrid](https://sendgrid.com) eller [SparkPost](https://sparkpost.com), ett anpassat REST API eller någon HTTP-baserad e-postleverantör (inklusive din egen). I den här artikeln beskrivs hur du konfigurerar en lösning som använder SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

Om du inte redan har ett, börja med att skapa ett SendGrid-konto (Azure-kunder kan låsa upp 25 000 kostnadsfria e-postmeddelanden varje månad). Instruktioner för inställningar finns i avsnittet [Skapa ett SendGrid-konto](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) i [Hur du skickar e-post med SendGrid med Azure](../sendgrid-dotnet-how-to-send-email.md).

Var noga med att fylla i avsnittet där du [skapar en SendGrid API-nyckel](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Registrera API-nyckeln för användning i ett senare steg.

## <a name="create-azure-ad-b2c-policy-key"></a>Skapa principnyckel för Azure AD B2C

Spara sedan SendGrid API-nyckeln i en Azure AD B2C-principnyckel för dina principer att referera till.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj din Azure AD B2C-katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Manual`väljer du .
1. Ange ett **namn** för principnyckeln. Till exempel `SendGridSecret`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
1. I **Secret**anger du din klienthemlighet som du tidigare spelat in.
1. För **nyckelanvändning** `Signature`väljer du .
1. Välj **Skapa**.

## <a name="create-sendgrid-template"></a>Skapa SendGrid-mall

När ett SendGrid-konto har skapats och SendGrid API-nyckel har lagrats i en Azure AD B2C-principnyckel skapar du en [dynamisk transaktionsmall](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)för SendGrid .

1. Öppna sidan [transaktionsmallar](https://sendgrid.com/dynamic_templates) på webbplatsen SendGrid och välj **Skapa mall**.
1. Ange ett unikt `Verification email` mallnamn som och välj sedan **Spara**.
1. Om du vill börja redigera den nya mallen väljer du **Lägg till version**.
1. Välj **Kodredigerare** och **fortsätt**sedan .
1. Klistra in följande HTML-mall i HTML-redigeraren eller använd din egen. `{{otp}}` Parametrarna `{{email}}` och ersätts dynamiskt med engångslösenordsvärdet och användarens e-postadress.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Expandera **Inställningar** till vänster och för `{{subject}}` **E-postämne**anger du .
1. Välj **Spara mall**.
1. Gå tillbaka till sidan **Transaktionsmallar** genom att välja bakåtpilen.
1. Registrera **ID:t** för mallen som du skapade för användning i ett senare steg. Till exempel `d-989077fbba9746e89f3f6411f596fb96`. Du anger det här ID:t när du [lägger till anspråksomvandlingen](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Lägga till Azure AD B2C-anspråkstyper

Lägg till följande anspråkstyper i `<ClaimsSchema>` elementet i `<BuildingBlocks>`i principen .

Dessa anspråkstyper är nödvändiga för att generera och verifiera e-postadressen med hjälp av en engångslösenordskod.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Lägg till anspråksomvandlingen

Därefter behöver du en anspråksomvandling för att mata ut ett JSON-stränganspråk som kommer att vara brödtexten i begäran som skickas till SendGrid.

JSON-objektets struktur definieras av ID:erna i punktatering av InputParameters och TransformationClaimTypes för InputClaims. Tal i punkt notationen antyder matriser. Värdena kommer från InputClaims värden och InputParameters "Value"-egenskaper. Mer information om JSON-anspråksomvandlingar finns i [JSON-anspråksomvandlingar](json-transformations.md).

Lägg till följande anspråksomvandling i elementet `<ClaimsTransformations>` i `<BuildingBlocks>`. Gör följande uppdateringar av XML för anspråksomvandling:

* Uppdatera `template_id` värdet InputParameter med ID:n för den SendGrid-transaktionsmall som du skapade tidigare i [mallen Skapa SendGrid](#create-sendgrid-template).
* Uppdatera `from.email` adressvärdet. Använd en giltig e-postadress för att förhindra att verifieringsmeddelandet markeras som skräppost.
* Uppdatera värdet för `personalizations.0.dynamic_template_data.subject` indataparametern ämnesrad med en ämnesrad som är lämplig för din organisation.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Lägg till DataUri-innehållsdefinition

Lägg till följande `<BuildingBlocks>` [ContentDefinition](contentdefinitions.md) under anspråksomvandlingarna i och refererar till data-URI för version 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Skapa en DisplayControl

En kontroll för verifieringsvisning används för att verifiera e-postadressen med en verifieringskod som skickas till användaren.

Den här exempelvisningskontrollen är konfigurerad för:

1. Samla `email` in adressanspråkstypen från användaren.
1. Vänta tills användaren `verificationCode` har lämnat anspråkstypen med koden som skickas till användaren.
1. Returnera `email` tillbaka till den självsäkra tekniska profilen som har en referens till den här visningskontrollen.
1. Med `SendCode` hjälp av åtgärden, generera en OTP-kod och skicka ett e-postmeddelande med OTP-koden till användaren.

![Skicka e-poståtgärd för verifieringskod](media/custom-email/display-control-verification-email-action-01.png)

Lägg till följande `<BuildingBlocks>` [DisplayControl](display-controls.md) av typen [VerificationControl](display-control-verification.md) i principen under innehållsdefinitioner.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Lägga till tekniska profiler för up-program

Den `GenerateOtp` tekniska profilen genererar en kod för e-postadressen. Den `VerifyOtp` tekniska profilen verifierar koden som är associerad med e-postadressen. Du kan ändra konfigurationen av formatet och utgången av engångslösenordet. Mer information om tekniska profiler för otp finns i [Definiera en teknisk profil för engångslösenord](one-time-password-technical-profile.md).

Lägg till följande tekniska `<ClaimsProviders>` profiler i elementet.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Lägga till en teknisk REST API-profil

Den här tekniska profilen för REST API genererar e-postinnehållet (med SendGrid-formatet). Mer information om RESTful-tekniska profiler finns i [Definiera en RESTful teknisk profil](restful-technical-profile.md).

Precis som med de tekniska profilerna för `<ClaimsProviders>` engångslösenta personer lägger du till följande tekniska profiler i elementet.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Gör en hänvisning till DisplayControl

I det sista steget lägger du till en referens till DisplayControl som du skapade. Ersätt din `LocalAccountSignUpWithLogonEmail` befintliga självpåförda tekniska profil med följande om du använde en tidigare version av Azure AD B2C-principen. Den här `DisplayClaims` tekniska profilen används med en referens till DisplayControl.

Mer information finns i [Självsäkrad teknisk profil](restful-technical-profile.md) och [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Valfritt] Lokalisera din e-post

Om du vill lokalisera e-postmeddelandet måste du skicka lokaliserade strängar till SendGrid eller din e-postleverantör. Till exempel för att lokalisera e-postämne, brödtext, ditt kodmeddelande eller signatur av e-postmeddelandet. För att göra det kan du använda [getlocalizedStringsTransformation](string-transformations.md) claims transformation för att kopiera lokaliserade strängar till anspråkstyper. I `GenerateSendGridRequestBody` anspråksomvandlingen, som genererar JSON-nyttolasten, används indataanspråk som innehåller de lokaliserade strängarna.

1. I din princip definiera följande stränganspråk: ämne, meddelande, codeIntro och signatur.
1. Definiera en [GetLocalizedStringsTransformation](string-transformations.md) anspråk omvandling att ersätta lokaliserade strängvärden i anspråk från steg 1.
1. Ändra `GenerateSendGridRequestBody` anspråksomvandlingen om du vill använda indataanspråk med följande XML-kodavsnitt.
1. Uppdatera din SendGrind-mall för att använda dynamiska parametrar i stället för alla strängar som kommer att lokaliseras av Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Nästa steg

Du kan hitta ett exempel på en anpassad e-postverifieringsprincip på GitHub:

[Anpassad e-postverifiering - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Information om hur du använder ett anpassat REST API eller en HTTP-baserad SMTP-e-postleverantör finns [i Definiera en RESTful teknisk profil i en azure AD B2C-anpassad princip](restful-technical-profile.md).
