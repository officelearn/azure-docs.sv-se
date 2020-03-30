---
title: Azure Single Sign On SAML Protocol | Microsoft-dokument
description: I den här artikeln beskrivs SAML-protokollet för enkel inloggning i Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262430"
---
# <a name="single-sign-on-saml-protocol"></a>Saml-protokoll för enkel inloggning

Den här artikeln innehåller autentiseringsbegäranden och svar från SAML 2.0 som Azure Active Directory (Azure AD) stöder för enkel inloggning.

Protokolldiagrammet nedan beskriver den enda signeringssekvensen. Molntjänsten (tjänsteleverantören) använder en HTTP-omdirigeringsbindning för att skicka ett `AuthnRequest` (autentiseringsbegäran) element till Azure AD (identitetsprovidern). Azure AD använder sedan en HTTP-inläggsbindning för att bokföra ett `Response` element till molntjänsten.

![Arbetsflöde för enkel inloggning](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest (en)

För att begära en användarautentisering `AuthnRequest` skickar molntjänster ett element till Azure AD. Ett exempel SAML 2.0 `AuthnRequest` kan se ut som följande exempel:

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
| ID | Krävs | Azure AD använder det här `InResponseTo` attributet för att fylla i attributet för det returnerade svaret. ID får inte börja med ett tal, så en gemensam strategi är att prepend en sträng som "id" till strängrepresentation av ett GUID. Till exempel `id6c1c178c166d486687be4aaf5e482730` är ett giltigt ID. |
| Version | Krävs | Den här parametern bör anges till **2.0**. |
| IssueInstant | Krävs | Det här är en DateTime-sträng med ETT UTC-värde och [tur-och-retur-format ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD förväntar sig ett DateTime-värde av den här typen, men utvärderar eller använder inte värdet. |
| PåståendeConsumerServiceUrl | Valfri | Om den här parametern `RedirectUri` tillhandahålls måste den matcha molntjänsten i Azure AD. |
| ForceAuthn (500) | Valfri | Detta är ett booleskt värde. Om det är sant innebär det att användaren kommer att tvingas autentisera igen, även om de har en giltig session med Azure AD. |
| ÄrPassiv | Valfri | Detta är ett booleskt värde som anger om Azure AD ska autentisera användaren tyst, utan användarinteraktion, med hjälp av sessionscookien om det finns en sådan. Om detta är sant kommer Azure AD att försöka autentisera användaren med hjälp av sessionscookien. |

Alla `AuthnRequest` andra attribut, till exempel Medgivande, Mål, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex och ProviderName **ignoreras**.

Azure AD ignorerar `Conditions` också `AuthnRequest`elementet i .

### <a name="issuer"></a>Utfärdare

Elementet `Issuer` i `AuthnRequest` ett måste exakt matcha ett av **ServicePrincipalNames** i molntjänsten i Azure AD. Detta är vanligtvis inställt på **app-ID URI** som anges under programregistreringen.

Ett SAML-utdrag `Issuer` som innehåller elementet ser ut som följande exempel:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Det här elementet begär ett visst namn-ID-format i svaret och är valfritt i `AuthnRequest` element som skickas till Azure AD.

Ett `NameIdPolicy` element ser ut som följande exempel:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Om `NameIDPolicy` det anges kan du `Format` inkludera dess valfria attribut. Attributet `Format` kan bara ha ett av följande värden. något annat värde resulterar i ett fel.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory utfärdar NameID-anspråket som en pairwise-identifierare.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory utfärdar NameID-anspråket i e-postadressformat.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Det här värdet gör att Azure Active Directory kan välja anspråksformat. Azure Active Directory utfärdar NameID som en pairwise-identifierare.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory utfärdar NameID-anspråk som ett slumpmässigt genererat värde som är unikt för den aktuella SSO-åtgärden. Det innebär att värdet är tillfälligt och inte kan användas för att identifiera den autentiserade användaren.

Azure AD ignorerar `AllowCreate` attributet.

### <a name="requestauthncontext"></a>RequestAuthnContext
Elementet `RequestedAuthnContext` anger önskade autentiseringsmetoder. Det är `AuthnRequest` valfritt i element som skickas till Azure AD. Azure AD `AuthnContextClassRef` stöder `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`värden som .

### <a name="scoping"></a>Miljöprövningens
Elementet, `Scoping` som innehåller en lista över `AuthnRequest` identitetsleverantörer, är valfritt i element som skickas till Azure AD.

Om det anges ska `ProxyCount` du `IDPListOption` inte `RequesterID` inkludera attributet eller elementet, eftersom de inte stöds.

### <a name="signature"></a>Signatur
Ta inte med `Signature` ett `AuthnRequest` element i element, eftersom Azure AD inte stöder signerade autentiseringsbegäranden.

### <a name="subject"></a>Subjekt
Azure AD ignorerar `Subject` elementet element av `AuthnRequest` element.

## <a name="response"></a>Svar
När en begärd inloggning har slutförts publicerar Azure AD ett svar på molntjänsten. Ett svar på ett lyckat inloggningsförsök ser ut som följande exempel:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
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

Elementet `Response` innehåller resultatet av auktoriseringsbegäran. Azure AD `ID`anger `Version` `IssueInstant` och värden `Response` i elementet. Följande attribut anges också:

* `Destination`: När inloggningen har slutförts ställs detta `RedirectUri` in på tjänsteleverantörens (molntjänstens).
* `InResponseTo`: Detta är `ID` inställt på `AuthnRequest` attributet för det element som initierade svaret.

### <a name="issuer"></a>Utfärdare

Azure AD `Issuer` anger `https://login.microsoftonline.com/<TenantIDGUID>/` elementet till där \<TenantIDGUID> är klient-ID för Azure AD-klienten.

Ett svar med Emittentelementet kan till exempel se ut så här:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Elementet `Status` förmedlar framgång eller misslyckande sign-on. Den innehåller `StatusCode` elementet, som innehåller en kod eller en uppsättning kapslade koder som representerar status för begäran. Den innehåller `StatusMessage` också elementet, som innehåller anpassade felmeddelanden som genereras under inloggningsprocessen.

<!-- TODO: Add an authentication protocol error reference -->

Följande exempel är ett SAML-svar på ett misslyckat inloggningsförsök.

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

### <a name="assertion"></a>Påstående

Förutom `ID`, `IssueInstant` och `Version`Azure AD anger följande element `Assertion` i elementet i svaret.

#### <a name="issuer"></a>Utfärdare

Detta är `https://sts.windows.net/<TenantIDGUID>/`inställt \<på var TenantIDGUID> är klient-ID för Azure AD-klienten.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Signatur

Azure AD signerar påståendet som svar på en lyckad inloggning. Elementet `Signature` innehåller en digital signatur som molntjänsten kan använda för att autentisera källan för att verifiera integriteten för påståendet.

För att generera den här digitala signaturen `IDPSSODescriptor` använder Azure AD signeringsnyckeln i elementet i dess metadatadokument.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subjekt

Detta anger det huvudnamn som är föremål för uttalandena i påståendet. Den innehåller `NameID` ett element som representerar den autentiserade användaren. Värdet `NameID` är en riktad identifierare som endast dirigeras till den tjänstprovider som är målgruppen för token. Det är beständigt - det kan återkallas, men tilldelas aldrig om. Det är också ogenomskinligt, eftersom det inte avslöjar något om användaren och kan inte användas som identifierare för attributfrågor.

Elementets `Method` `SubjectConfirmation` attribut är alltid `urn:oasis:names:tc:SAML:2.0:cm:bearer`inställt på .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Villkor

Det här elementet anger villkor som definierar godtagbar användning av SAML-påståenden.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` Attributen `NotOnOrAfter` och anger det intervall under vilket påståendet är giltigt.

* Värdet för `NotBefore` attributet är lika med eller något (mindre än `IssueInstant` en `Assertion` sekund) senare än värdet för attributet för elementet. Azure AD tar inte hänsyn till någon tidsskillnad mellan sig själv och molntjänsten (tjänsteleverantör) och lägger inte till någon buffert till den här gången.
* Värdet för `NotOnOrAfter` attributet är 70 minuter senare `NotBefore` än värdet för attributet.

#### <a name="audience"></a>Målgrupp

Detta innehåller en URI som identifierar en avsedd målgrupp. Azure AD anger värdet för det `Issuer` här elementet till värdet för elementet i `AuthnRequest` det som initierade inloggningen. Om du `Audience` vill utvärdera värdet `App ID URI` använder du värdet för det som angavs under programregistreringen.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Precis `Issuer` som värdet `Audience` måste värdet exakt matcha ett av tjänstens huvudnamn som representerar molntjänsten i Azure AD. Men om värdet för `Issuer` elementet inte är ett `Audience` URI-värde, `Issuer` är värdet `spn:`i svaret det värde som föregås av .

#### <a name="attributestatement"></a>AttributeStatement

Detta innehåller anspråk om ämnet eller användaren. Följande utdrag innehåller ett `AttributeStatement` exempelelement. Ellipsen anger att elementet kan innehålla flera attribut och attributvärden.

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

* **Namnanspråk** - Värdet `Name` för`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`attributet ( ) är användarens huvudnamn för den autentiserade användaren, till exempel `testuser@managedtenant.com`.
* **ObjectIdentifier Claim** - Värdet `ObjectIdentifier` för`http://schemas.microsoft.com/identity/claims/objectidentifier`attributet `ObjectId` ( ) är katalogobjektet som representerar den autentiserade användaren i Azure AD. `ObjectId`är en oföränderlig, globalt unik och återanvändning av säker identifierare för den autentiserade användaren.

#### <a name="authnstatement"></a>AuthnStatement

Detta element hävdar att påståendet ämnet autentiserades på ett visst sätt vid en viss tidpunkt.

* Attributet `AuthnInstant` anger vid vilken tidpunkt användaren autentiserades med Azure AD.
* Elementet `AuthnContext` anger den autentiseringskontext som används för att autentisera användaren.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
