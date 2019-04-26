---
title: Azure AD Federation Metadata | Microsoft Docs
description: Den här artikeln beskriver federationsmetadatadokumentet som Azure Active Directory publicerar för tjänster som har stöd för Azure Active Directory-token.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcabf51b3a368841f7f135a32c4824eb3db571ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299829"
---
# <a name="federation-metadata"></a>Federationsmetadata
Azure Active Directory (Azure AD) publicerar ett dokument med federationsmetadata för tjänster som är konfigurerad för att acceptera de säkerhetstoken som Azure AD utfärdar. Dokumentformat för federation metadata som beskrivs i den [Web Services Federation Language (WS-Federation) Version 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), som utökar [Metadata för OASIS Security Assertion Markup Language (SAML) v2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Klientspecifik och klient-oberoende Metadataslutpunkter
Azure AD publicerar klientspecifik och klient-oberoende slutpunkter.

Klientspecifik slutpunkter är utformade för en viss klient. Klientspecifik federationsmetadata innehåller information om klienten, inklusive klientspecifik utfärdare och slutpunkten information. Program som begränsar åtkomsten till en enda klient använda klientspecifik slutpunkter.

Klient-oberoende slutpunkter innehåller information som är gemensamma för alla Azure AD-klienter. Den här informationen gäller för klienter som finns på *login.microsoftonline.com* och delas mellan klienter. Klient-oberoende slutpunkter rekommenderas för program med flera klienter, eftersom de inte är associerade med viss innehavare.

## <a name="federation-metadata-endpoints"></a>Slutpunkter för Federation metadata
Azure AD publicerar federationsmetadata på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

För **klientspecifik slutpunkter**, `TenantDomainName` kan vara något av följande typer:

* Ett registrerat domännamn för en Azure AD-klient, till exempel: `contoso.onmicrosoft.com`.
* Den kan ändras klient-ID i domänen, till exempel `72f988bf-86f1-41af-91ab-2d7cd011db45`.

För **klient oberoende slutpunkter**, `TenantDomainName` är `common`. Det här dokumentet visas endast de Federationsmetadata-element som är gemensamma för alla Azure AD-klienter som finns på login.microsoftonline.com.

Till exempel en klientspecifik slutpunkt kanske `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Oberoende av klient-slutpunkten är [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Du kan visa federationsmetadatadokumentet genom att skriva följande URL i en webbläsare.

## <a name="contents-of-federation-metadata"></a>Innehållet i federation Metadata
Följande avsnitt innehåller information som krävs av tjänster som använder de token som utfärdats av Azure AD.

### <a name="entity-id"></a>Enhets-id
Den `EntityDescriptor` elementet innehåller ett `EntityID` attribut. Värdet för den `EntityID` attributet representerar utfärdaren, det vill säga den säkerhetstokentjänst (STS) som utfärdade token. Det är viktigt att verifiera utfärdaren när du tar emot en token.

Följande metadata visar ett urval klientspecifik `EntityDescriptor` element med ett `EntityID` element.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Du kan ersätta klient-ID i slutpunkten klient oberoende med ditt klient-ID för att skapa en klientspecifik `EntityID` värde. Resultatvärdet ska vara samma som tokenutfärdaren. Strategin kan ett program för flera användare att verifiera utfärdaren för en viss klient.

Följande metadata visar ett urval klient oberoende `EntityID` element. Observera att som den `{tenant}` är en literal, inte en platshållare.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certifikat för tokensignering
När en tjänst tar emot en token som utfärdas av en Azure AD-klient, måste signaturen för token verifieras med en signeringsnyckel som publiceras i federationsmetadatadokumentet. Federationsmetadata innehåller den offentliga delen av de certifikat som klienterna använder för tokensignering. Certifikatet rå byte visas i den `KeyDescriptor` element. Certifikatet för tokensignering är giltig för signering bara när värdet för den `use` attributet är `signing`.

En federationsmetadatadokumentet som publicerats av Azure AD kan ha flera nycklar för signering, till exempel när Azure AD förbereds för att uppdatera signeringscertifikatet. När ett dokument med federationsmetadata innehåller fler än ett certifikat, en tjänst som verifieras token ska ha stöd för alla certifikat i dokumentet.

Följande metadata visar ett urval `KeyDescriptor` element med en signeringsnyckel.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Den `KeyDescriptor` elementet förekommer på två platser i federationsmetadatadokumentet; i avsnittet WS-Federation-specifika och avsnittet SAML-specifika. De certifikat som publicerats i båda avsnitten är samma.

I avsnittet WS-Federation-specifika en läsare för WS-Federation metadata läser certifikat från en `RoleDescriptor` element med den `SecurityTokenServiceType` typen.

Följande metadata visar ett urval `RoleDescriptor` element.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

I avsnittet SAML-specifika en läsare för WS-Federation metadata läser certifikat från en `IDPSSODescriptor` element.

Följande metadata visar ett urval `IDPSSODescriptor` element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Det finns inga skillnader i formatet klientspecifik och klient-oberoende certifikat.

### <a name="ws-federation-endpoint-url"></a>Slutpunkts-URL för WS-Federation
Federationsmetadata innehåller den URL som använder Azure AD för enkel inloggning och enkel utloggning i WS-Federation-protokollet. Den här slutpunkten visas i den `PassiveRequestorEndpoint` element.

Följande metadata visar ett urval `PassiveRequestorEndpoint` element för en klientspecifik slutpunkt.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
För oberoende av klient-slutpunkt för WS-Federation Webbadressen som visas i WS-Federation-slutpunkt som visas i följande exempel.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Slutpunkts-URL för SAML-protokoll
Federationsmetadata innehåller den URL som Azure AD använder för enkel inloggning och enkel utloggning i SAML 2.0-protokollet. De här slutpunkterna visas i den `IDPSSODescriptor` element.

URL: er med inloggning och utloggning visas i den `SingleSignOnService` och `SingleLogoutService` element.

Följande metadata visar ett urval `PassiveResistorEndpoint` för en klientspecifik slutpunkt.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

På samma sätt publiceras slutpunkterna för vanliga SAML 2.0-protokollslutpunkterna i klient oberoende federationsmetadata som visas i följande exempel.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
