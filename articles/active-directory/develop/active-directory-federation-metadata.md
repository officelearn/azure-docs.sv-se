---
title: Azure AD-Federationsmetadata | Microsoft Docs
description: Den här artikeln beskriver federation Metadatadokumentet som Azure Active Directory publicerar för tjänster som accepterar token för Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: cfc79b451eafe7dcdd0b8f4285f92714138260bb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156134"
---
# <a name="federation-metadata"></a>Federationsmetadata
Azure Active Directory (AD Azure) publicerar en federation metadata dokument för tjänster som är konfigurerad för att acceptera de säkerhetstoken som Azure AD utfärdar. Federation metadata dokumentets format är beskrivs i den [Web Services Federation Language (WS-Federation) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), som utökar [Metadata för OASIS Security Assertion Markup Language (SAML) v2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Klient-specifika och klient oberoende Metadataslutpunkter
Azure AD publicerar klient-specifika och klient oberoende slutpunkter.

Klient-specifika slutpunkter är utformade för en viss klient. Klient-specifika federationsmetadata innehåller information om klienten, inklusive klient-specifik information för utfärdare och slutpunkt. Program som begränsar åtkomsten till en enskild klient använda klient-specifika slutpunkter.

Klient-oberoende slutpunkter ger information som är gemensamma för alla Azure AD-klienter. Den här informationen gäller för klienter som värd *login.microsoftonline.com* och delas av klienter. Klient-oberoende slutpunkter rekommenderas för program med flera klienter, eftersom de inte är associerade med viss innehavare.

## <a name="federation-metadata-endpoints"></a>Federation Metadataslutpunkter
Azure AD publicerar federationsmetadata på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

För **klient-specifika slutpunkter**, `TenantDomainName` kan vara något av följande typer:

* Ett registrerat domännamn för en Azure AD-klient, t.ex: `contoso.onmicrosoft.com`.
* Den oåterkalleliga klient-ID på domänen som `72f988bf-86f1-41af-91ab-2d7cd011db45`.

För **klient oberoende slutpunkter**, `TenantDomainName` är `common`. Det här dokumentet innehåller endast Federationsmetadata element som är gemensamma för alla Azure AD-klienter som finns på login.microsoftonline.com.

En klient-specifika slutpunkt kan till exempel vara `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Oberoende av klient-slutpunkten är [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Du kan visa dokumentet federation metadata genom att ange den här URL i en webbläsare.

## <a name="contents-of-federation-metadata"></a>Innehållet i federation Metadata
Följande avsnitt innehåller information som krävs av tjänster som använder de token som utfärdats av Azure AD.

### <a name="entity-id"></a>Enhets-id
Den `EntityDescriptor` elementet innehåller ett `EntityID` attribut. Värdet för den `EntityID` attributet representerar utfärdaren, det vill säga säkerhetstokentjänsten (STS) som utfärdade token. Det är viktigt att verifiera utfärdaren när du får en token.

Följande metadata visas ett exempel på klient-specifika `EntityDescriptor` element med ett `EntityID` element.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Du kan ersätta klient-ID i slutpunkten oberoende av klienten med klient-ID för att skapa en klient-specifika `EntityID` värde. Det resulterande värdet ska vara samma som token utfärdaren. Strategin kan ett program för flera innehavare att verifiera utgivaren för en viss klient.

Följande metadata visas ett exempel på klient-oberoende `EntityID` element. Observera, att den `{tenant}` är en literal, inte en platshållare.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certifikat för tokensignering
När en tjänst tar emot en token som utfärdas av en Azure AD-klient, verifieras signaturen för token med en signeringsnyckel som publiceras i federation Metadatadokumentet. Federationsmetadata innehåller den offentliga delen av de certifikat som klienterna använder för tokensignering. Certifikatet rå byte visas i den `KeyDescriptor` element. Certifikatet för tokensignering är giltig för signering endast när värdet för den `use` attributet `signing`.

Ett federation metadata dokument som publicerats av Azure AD kan ha flera nycklar för signering, till exempel när Azure AD förbereds att uppdatera signeringscertifikatet. När ett dokument för federation metadata innehåller fler än ett certifikat är en tjänst som validerar token som ska ha stöd för alla certifikat i dokumentet.

Följande metadata visas ett exempel på `KeyDescriptor` element med ett signeringsnyckeln.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Den `KeyDescriptor` elementet visas på två platser i dokumentet federation metadata; i WS-Federation-specifik avsnitten och SAML-specifika. De certifikat som publicerats i båda avsnitten ska vara samma.

I avsnittet WS-Federation-specifik en läsare för WS-Federation metadata läser certifikat från en `RoleDescriptor` element med den `SecurityTokenServiceType` typen.

Följande metadata visas ett exempel på `RoleDescriptor` element.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

I avsnittet SAML-specifika en läsare för WS-Federation metadata läser certifikat från en `IDPSSODescriptor` element.

Följande metadata visas ett exempel på `IDPSSODescriptor` element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Det finns inga skillnader i format för klient-specifika och klient oberoende certifikat.

### <a name="ws-federation-endpoint-url"></a>WS-Federation slutpunkts-URL
Federationsmetadata innehåller den URL som använder Azure AD för enkel inloggning och enskild utloggning i WS-Federation-protokollet. Den här slutpunkten som visas i den `PassiveRequestorEndpoint` element.

Följande metadata visas ett exempel på `PassiveRequestorEndpoint` element för en slutpunkt för innehavare.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
För oberoende av klient-slutpunkten WS-Federation-URL som visas i WS-Federation-slutpunkt som visas i följande exempel.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Slutpunkts-URL för SAML-protokoll
Federationsmetadata innehåller URL: en där Azure AD för enkel inloggning och enskild utloggning i SAML 2.0-protokollet. Dessa slutpunkter som visas i den `IDPSSODescriptor` element.

URL: er med inloggning och utloggning visas i den `SingleSignOnService` och `SingleLogoutService` element.

Följande metadata visas ett exempel på `PassiveResistorEndpoint` för en slutpunkt för innehavare.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Slutpunkterna för vanliga SAML 2.0-protokollet slutpunkter publiceras på samma sätt i klient-oberoende federationsmetadata, som visas i följande exempel.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
