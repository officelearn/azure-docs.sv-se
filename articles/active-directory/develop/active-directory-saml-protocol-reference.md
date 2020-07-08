---
title: Så här använder Azure AD SAML-protokoll
description: Den här artikeln innehåller en översikt över SAML-profilerna för enkel inloggning och enkel inloggning i Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 44d06030d8015d2df9499ce903eb9cb06e1ef27a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885658"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Så här använder Azure AD SAML-protokoll

Azure Active Directory (Azure AD) använder SAML 2,0-protokollet för att göra det möjligt för program att tillhandahålla enkel inloggning till sina användare. SAML-profilerna för [enkel inloggning](single-sign-on-saml-protocol.md) och [enkel utloggning](single-sign-out-saml-protocol.md) i Azure AD förklarar hur SAML-kontroller, protokoll och bindningar används i tjänsten för identitets leverantör.

SAML-protokollet kräver identitets leverantören (Azure AD) och tjänst leverantören (programmet) för att utbyta information om sig själva.

När ett program har registrerats med Azure AD registrerar appens utvecklare en federationsserverproxy med Azure AD. Den här informationen inkluderar **omdirigerings-URI** och **metadata-URI** för programmet.

Azure AD använder moln tjänstens **metadata-URI** för att hämta signerings nyckeln och utloggnings-URI: n. Kunden kan öppna appen i **Azure AD-> app Registration** och sedan i **Inställningar-> egenskaper**kan de uppdatera UTloggnings-URL: en. På så sätt kan Azure AD skicka svaret till rätt URL. 

Azure Active Directory exponerar klient-och common (klient oberoende) enkel inloggning och enkel inloggnings slut punkter. Dessa URL: er representerar adresser bara platser – de är inte bara identifierare – så att du kan gå till slut punkten för att läsa metadata.

* Den klient-/regionsspecifika slut punkten finns på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . *\<TenantDomainName>* Plats hållaren representerar ett registrerat domän namn eller ett TenantID-GUID för en Azure AD-klient. Contoso.com-klientens federationsmetadata är till exempel:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Klient oberoende slut punkt finns på `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . I den här slut punkts adressen visas **vanligt** i stället för ett klient domän namn eller-ID.

Information om de dokument för federationsmetadata som Azure AD publicerar finns i [federationsmetadata](../azuread-dev/azure-ad-federation-metadata.md).
