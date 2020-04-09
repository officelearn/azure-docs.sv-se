---
title: Så här använder Azure AD SAML-protokoll
description: Den här artikeln innehåller en översikt över SAML-profilerna För enkel inloggning och enkel inloggning i Azure Active Directory.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885658"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Så här använder Azure AD SAML-protokoll

Azure Active Directory (Azure AD) använder SAML 2.0-protokollet för att göra det möjligt för program att tillhandahålla en enda inloggningsupplevelse för sina användare. [Saml-påståenden,](single-sign-on-saml-protocol.md) protokoll och bindningar används i identitetsprovidertjänsten med enkel inloggning och [enkel inloggning.](single-sign-out-saml-protocol.md)

SAML Protocol kräver att identitetsprovidern (Azure AD) och tjänsteleverantören (programmet) utbyter information om sig själva.

När ett program registreras med Azure AD registrerar apputvecklaren federationsrelaterad information med Azure AD. Den här informationen omfattar **programmets redirect URI** och **metadatauri.**

Azure AD använder molntjänstens Metadata URI för att hämta signeringsnyckeln och utloggnings-URI.Azure AD uses the cloud service's **Metadata URI** to retrieve the signing key and the logout URI. Kunden kan öppna appen i **Azure AD -> Appregistrering** och sedan i **Inställningar -> Egenskaper**kan de uppdatera url:en för utloggning. På så sätt kan Azure AD skicka svaret till rätt URL. 

Azure Active Directory exponerar klientspecifika och vanliga (klientoberoende) enkel inloggnings- och enstaka utskrivningsslutpunkter. Dessa webbadresser representerar adresserbara platser - de är inte bara identifierare - så att du kan gå till slutpunkten för att läsa metadata.

* Den klientspecifika slutpunkten finns `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`på . * \<Den KlientDomainName>* platshållaren representerar ett registrerat domännamn eller TenantID GUID för en Azure AD-klientorganisation. Federationsmetadata för den contoso.com klienten finns till exempel på:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Slutpunkten för klientoberoende `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`finns på . I den här slutpunktsadressen visas **vanligt** i stället för ett klientdomännamn eller id-kort.

Information om federationsmetadatadokument som Azure AD publicerar finns i [Federationsmetadata](../azuread-dev/azure-ad-federation-metadata.md).
