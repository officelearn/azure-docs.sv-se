---
title: Metadata för Azure AD-federation | Microsoft-dokument
description: I den här artikeln beskrivs federationsmetadatadokumentet som Azure Active Directory publicerar för tjänster som accepterar Azure Active Directory-token.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154907"
---
# <a name="federation-metadata"></a>Federationsmetadata

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) publicerar ett federationsmetadatadokument för tjänster som är konfigurerade för att acceptera de säkerhetstoken som Azure AD utfärdar. Dokumentformatet för federationsmetadata beskrivs i [WS-Federations språk (Web Services Federation Language) Version 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), som utökar [metadata för OAS-säkerhetspåläggsspråket (SAML) v2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Slutpunkter för klientspecifika och klientoberoende metadata
Azure AD publicerar klientspecifika och klientoberoende slutpunkter.

Klientspecifika slutpunkter är utformade för en viss klient. Klientspecifika federationsmetadata innehåller information om klienten, inklusive klientspecifik utfärdare och slutpunktsinformation. Program som begränsar åtkomsten till en enda klient använder klientspecifika slutpunkter.

Klientoberoende slutpunkter ger information som är gemensam för alla Azure AD-klienter. Den här informationen gäller klienter som finns på *login.microsoftonline.com* och delas mellan klienter. Klientoberoende slutpunkter rekommenderas för program med flera innehavare, eftersom de inte är associerade med någon särskild klientorganisation.

## <a name="federation-metadata-endpoints"></a>Slutpunkter för federationsmetadata
Azure AD publicerar federationsmetadata på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

För **klientspecifika slutpunkter**kan `TenantDomainName` den vara en av följande typer:

* Ett registrerat domännamn för en Azure AD-klient, till exempel: `contoso.onmicrosoft.com`.
* Domänens oföränderliga klient-ID, till exempel `72f988bf-86f1-41af-91ab-2d7cd011db45`.

För **klientoberoende slutpunkter** `TenantDomainName` är `common`det . Det här dokumentet listar endast federationsmetadataelement som är gemensamma för alla Azure AD-klienter som finns på login.microsoftonline.com.

En klientspecifik slutpunkt kan till `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`exempel vara . Slutpunkten för klientoberoende är [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Du kan visa federationsmetadatadokumentet genom att skriva den här URL:en i en webbläsare.

## <a name="contents-of-federation-metadata"></a>Innehållet i federationens metadata
I följande avsnitt finns information som behövs av tjänster som använder token som utfärdats av Azure AD.

### <a name="entity-id"></a>Enhets-ID
Elementet `EntityDescriptor` innehåller `EntityID` ett attribut. Värdet för `EntityID` attributet representerar utfärdaren, det villa om säkerhetstokentjänsten (STS) som utfärdade token. Det är viktigt att validera utfärdaren när du får en token.

Följande metadata visar ett exempel `EntityDescriptor` på `EntityID` klientspecifikt element med ett element.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Du kan ersätta klient-ID i klientoberoende slutpunkten med ditt klient-ID för att skapa ett klientspecifikt `EntityID` värde. Det resulterande värdet kommer att vara detsamma som tokenutfärdaren. Strategin gör det möjligt för ett program med flera innehavare att validera utfärdaren för en viss klient.

Följande metadata visar ett exempel `EntityID` på klientoberoende element. Observera att det `{tenant}` är en bokstavlig, inte en platshållare.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Tokensigneringscertifikat
När en tjänst tar emot en token som utfärdas av en Azure AD-klient måste signaturen för token valideras med en signeringsnyckel som publiceras i federationens metadatadokument. Federationens metadata innehåller den offentliga delen av certifikaten som klienterna använder för tokensignering. Certifikatets råa byte `KeyDescriptor` visas i elementet. Tokensigneringscertifikatet är endast giltigt `use` för `signing`signering när värdet för attributet är .

Ett federationsmetadatadokument som publiceras av Azure AD kan ha flera signeringsnycklar, till exempel när Azure AD förbereder att uppdatera signeringscertifikatet. När ett federationsmetadatadokument innehåller mer än ett certifikat bör en tjänst som validerar token stödja alla certifikat i dokumentet.

Följande metadata visar `KeyDescriptor` ett exempelelement med en signeringsnyckel.

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

Elementet `KeyDescriptor` visas på två ställen i federationens metadatadokument. i avsnittet WS-Federation-specifika avsnitt och det SAML-specifika avsnittet. De certifikat som publiceras i båda avsnitten kommer att vara desamma.

I avsnittet WS-Federation-specifika skulle en WS-Federation-metadataläsare läsa `RoleDescriptor` certifikaten `SecurityTokenServiceType` från ett element med typen.

Följande metadata visar `RoleDescriptor` ett exempelelement.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

I det SAML-specifika avsnittet läste en WS-Federation-metadataläsare `IDPSSODescriptor` certifikaten från ett element.

Följande metadata visar `IDPSSODescriptor` ett exempelelement.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Det finns inga skillnader i formatet för klientspecifika och klientoberoende certifikat.

### <a name="ws-federation-endpoint-url"></a>Url till WS-Federation-slutpunkt
Federationsmetadata innehåller url:en som azure AD använder för enkel inloggning och enkel ut logga ut i WS-Federation-protokollet. Den här slutpunkten `PassiveRequestorEndpoint` visas i elementet.

Följande metadata visar `PassiveRequestorEndpoint` ett exempelelement för en klientspecifik slutpunkt.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
För den klientoberoende slutpunkten visas WS-Federation-URL:en i WS-Federation-slutpunkten, vilket visas i följande exempel.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL för SLUTPUNKT FÖR SAML-protokoll
Federationsmetadata innehåller url:en som Azure AD använder för enkel inloggning och enkel ut logga ut i SAML 2.0-protokollet. Dessa slutpunkter visas `IDPSSODescriptor` i elementet.

Url:erna för inloggning och ut `SingleSignOnService` logga `SingleLogoutService` ut visas i elementen och.

Följande metadata visar `PassiveResistorEndpoint` ett exempel för en klientspecifik slutpunkt.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

På samma sätt publiceras slutpunkterna för de vanliga SAML 2.0-protokollslutpunkterna i klientoberoende federationsmetadata, vilket visas i följande exempel.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
