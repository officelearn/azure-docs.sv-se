---
title: Azure för enkel inloggning SAML-protokoll | Microsoft Docs
description: Den här artikeln beskriver enkel inloggning på SAML-protokoll i Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: edb8ae501548775932a259621c19acece474018d
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581739"
---
# <a name="single-sign-on-saml-protocol"></a>Enkel inloggning SAML-protokoll

Den här artikeln beskriver SAML 2.0-autentiseringsbegäranden och svar som stöder Azure Active Directory (Azure AD) för enkel inloggning.

Protokollet diagrammet nedan beskriver ordningsföljden för enkel inloggning. Molntjänst (service provider) använder en omdirigering för HTTP-bindning för att skicka en `AuthnRequest` element (autentiseringsbegäran) till Azure AD (identitetsprovidern). Sedan Azure AD använder en HTTP post bindning för att publicera en `Response` element till Molntjänsten.

![Arbetsflöde för enkel inloggning](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Om du vill begära en användarautentisering, cloud services-skicka ett `AuthnRequest` element till Azure AD. Ett exempel SAML 2.0 `AuthnRequest` bör se ut som i följande exempel:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| ID | Krävs | Azure AD använder det här attributet för att fylla i `InResponseTo` attribut för returnerade svaret. ID: T kan inte börja med ett tal, så en gemensam strategi är att åtkomstgruppen liknande ”id” till strängrepresentation av ett GUID. Till exempel `id6c1c178c166d486687be4aaf5e482730` är ett giltigt-ID. |
| Version | Krävs | Den här parametern anges till **2.0**. |
| IssueInstant | Krävs | Det här är ett DateTime-sträng med ett UTC-värde och [fram och åter format (”o”)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD måste ha ett DateTime-värde av samma typ, men inte utvärdera eller använda värdet. |
| AssertionConsumerServiceUrl | Valfri | Om den här parametern måste matcha den `RedirectUri` av Molntjänsten i Azure AD. |
| ForceAuthn | Valfri | Det här är ett booleskt värde. Om värdet är SANT innebär det att användaren tvingas att autentisera igen, även om de har en giltig session med Azure AD. |
| IsPassive | Valfri | Det här är ett booleskt värde som anger om Azure AD ska autentisera användaren tyst, utan användarinteraktion, med hjälp av sessions-cookie om en sådan finns. Om det stämmer, försöker Azure AD autentiserar användaren med sessions-cookie. |

Alla andra `AuthnRequest` attribut, t.ex. godkännande, mål, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex och ProviderName är **ignoreras**.

Azure AD även ignorerar den `Conditions` element i `AuthnRequest`.

### <a name="issuer"></a>Utfärdare

Den `Issuer` element i en `AuthnRequest` måste exakt matcha en av de **ServicePrincipalNames** i Molntjänsten i Azure AD. Normalt detta är inställt på den **Appidentitets-URI** som har angetts under registreringen av program.

En SAML utdrag som den `Issuer` ser ut som i följande exempel:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Det här elementet begär ett visst namn-ID-format i svaret och är valfritt i `AuthnRequest` element som skickas till Azure AD.

En `NameIdPolicy` ser ut som i följande exempel:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Om `NameIDPolicy` anges så du kan inkludera dess valfritt `Format` attribut. Den `Format` attributet kan bara ha en av följande värden; alla andra resulterar i ett fel.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory utfärdar NameID-anspråket som en pairwise identifierare.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory utfärdar NameID-anspråket i e-postadress.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Det här värdet tillåter Azure Active Directory för att välja anspråket-format. Azure Active Directory utfärdar NameID som en pairwise identifierare.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory utfärdar NameID-anspråket som ett slumpmässigt genererat värde som är unik för den aktuella SSO-åtgärden. Det innebär att värdet är tillfällig och kan inte användas för att identifiera den autentiserande användaren.

Azure AD ignorerar den `AllowCreate` attribut.

### <a name="requestauthncontext"></a>RequestAuthnContext
Den `RequestedAuthnContext` elementet anger önskad autentiseringsmetoder. Det är valfritt i `AuthnRequest` element som skickas till Azure AD. Azure AD stöder bara en `AuthnContextClassRef` värde: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Ange omfång
Den `Scoping` element som innehåller en lista över identitetsleverantörer, är valfritt i `AuthnRequest` element som skickas till Azure AD.

Om omfattar inte den `ProxyCount` attribut, `IDPListOption` eller `RequesterID` element, eftersom de inte stöds.

### <a name="signature"></a>Signatur
Omfattar inte en `Signature` element i `AuthnRequest` element, som Azure AD inte stöder signerats autentiseringsbegäranden.

### <a name="subject"></a>Ämne
Azure AD ignorerar den `Subject` element i `AuthnRequest` element.

## <a name="response"></a>Svar
När en begärda inloggning är klar skickar ett svar till Molntjänsten Azure AD. Ett svar till lyckade försök inloggning ser ut som i följande exempel:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Svar

Den `Response` elementet innehåller resultatet av begäran om godkännande. Azure AD-uppsättningar på `ID`, `Version` och `IssueInstant` värdena i den `Response` element. Koden ställer också följande attribut:

* `Destination`: När inloggningen har slutförts, detta är inställt på den `RedirectUri` för tjänstleverantören (Molntjänsten).
* `InResponseTo`: Detta är inställt på den `ID` attributet för den `AuthnRequest` element som initierade svaret.

### <a name="issuer"></a>Utfärdare

Azure AD-uppsättningar på `Issuer` elementet mot `https://login.microsoftonline.com/<TenantIDGUID>/` där <TenantIDGUID> är klient-ID för Azure AD-klient.

Ett svar med utfärdaren element kan till exempel se ut som i följande exempel:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Den `Status` förmedlas elementet har lyckats eller misslyckats för inloggning. Den innehåller den `StatusCode` element som innehåller en kod eller en uppsättning kapslade koder som representerar status för begäran. Den innehåller också de `StatusMessage` element som innehåller anpassade felmeddelanden som genereras under inloggning.

<!-- TODO: Add a authentication protocol error reference -->

I följande exempel är ett SAML-svar till ett misslyckat försök inloggning.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>försäkran

Förutom den `ID`, `IssueInstant` och `Version`, Azure AD anger följande element i den `Assertion` element i svaret.

#### <a name="issuer"></a>Utfärdare

Detta är inställt på `https://sts.windows.net/<TenantIDGUID>/`där <TenantIDGUID> är klient-ID för Azure AD-klient.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Signatur

Azure AD signerar försäkran som svar på en lyckad inloggning. Den `Signature` elementet innehåller en digital signatur som Molntjänsten kan använda för att autentisera källan för att kontrollera integriteten för kontrollen.

För att generera den digitala signaturen, Azure AD använder signeringsnyckeln i den `IDPSSODescriptor` element i Metadatadokumentet dess.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Ämne

Detta anger det säkerhetsobjekt som omfattas av instruktionerna i kontrollen. Den innehåller en `NameID` element som representerar den autentiserade användaren. Den `NameID` värdet är en riktad identifierare som riktas endast till tjänstleverantören som är målgruppen för token. Det är beständiga – den kan återkallas, men aldrig omtilldelas. Det är också täckande, eftersom det inte avslöjar något om användaren och kan inte användas som en identifierare för attributet frågor.

Den `Method` attributet för den `SubjectConfirmation` element har alltid värdet `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Villkor

Det här elementet anger villkoren som definierar godkänd användning av SAML intyg.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Den `NotBefore` och `NotOnOrAfter` attribut ange med vilket intervall då kontrollen är giltig.

* Värdet för den `NotBefore` attributet uppgår till eller något (mindre än en sekund) senare än värdet för `IssueInstant` attributet för den `Assertion` element. Azure AD tar inte hänsyn till eventuella tidsskillnaden mellan själva och Molntjänsten (service provider) och lägger inte till någon buffert nu.
* Värdet för den `NotOnOrAfter` attributet är 70 minuter senare än värdet för den `NotBefore` attribut.

#### <a name="audience"></a>Målgrupp

Innehåller en URI som identifierar en målgrupp. Azure AD anger värdet för det här elementet till värdet för `Issuer` elementet i den `AuthnRequest` som initierade inloggning. Att utvärdera den `Audience` använder värdet för den `App ID URI` som angavs under registreringen av program.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Som den `Issuer` värde, den `Audience` värde måste exakt matcha en av tjänstens huvudnamn som representerar Molntjänsten i Azure AD. Men om värdet för den `Issuer` elementet är inte en URI-värdet i `Audience` värdet i svaret är den `Issuer` värdet prefixet `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Innehåller anspråk om ämnet eller användare. Följande utdrag innehåller ett exempel på `AttributeStatement` element. De tre punkterna anger att elementet kan innehålla flera attribut och attributvärden.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Namnge anspråk** -värdet för den `Name` attribut (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) är den autentiserade användaren, användarens huvudnamn som `testuser@managedtenant.com`.
* **ObjectIdentifier anspråk** -värdet för den `ObjectIdentifier` attribut (`http://schemas.microsoft.com/identity/claims/objectidentifier`) är den `ObjectId` för objekt som representerar den autentiserade användaren i Azure AD. `ObjectId` är en oföränderlig globalt unikt och återanvända säker identifierare för den autentiserade användaren.

#### <a name="authnstatement"></a>AuthnStatement

Det här elementet kontrollerar att ämnet assertion autentiserades på ett visst sätt vid en given tidpunkt.

* Den `AuthnInstant` attributet anger den tidpunkt då användaren autentiseras med Azure AD.
* Den `AuthnContext` elementet anger autentiseringskontext som används för att autentisera användaren.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
