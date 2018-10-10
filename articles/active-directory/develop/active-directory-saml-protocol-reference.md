---
title: Hur Azure AD använder SAML-protokollet | Microsoft Docs
description: Den här artikeln innehåller en översikt över enkel inloggning och enkel utloggning SAML-profiler i Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: b9ae7cf9d6fd383c7cdfa3957b5d9b94c7207d50
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900663"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Hur Azure AD använder SAML-protokoll

Azure Active Directory (Azure AD) använder SAML 2.0-protokollet att aktivera program för att tillhandahålla en enkel inloggning till sina användare. Den [enkel inloggning](single-sign-on-saml-protocol.md) och [enkel utloggning](single-sign-out-saml-protocol.md) SAML-profiler i Azure AD förklarar hur SAML intyg, protokoll och bindningar används i identitetstjänst för providern.

SAML-protokoll kräver identitetsprovider (Azure AD) och service provider (programmet) att utbyta information om sig själva.

När ett program registreras med Azure AD, registrerar apputvecklaren federation-relaterad information med Azure AD. Informationen omfattar den **omdirigerings-URI** och **Metadata URI** av programmet.

Azure AD använder molntjänstens **Metadata URI** att hämta signeringsnyckeln och utloggning URI. Om programmet inte stöder en metadata URI kan utvecklare kontakta Microsoft support för att ge ut URI och nyckel för signeringscertifikatet.

Azure Active Directory exponerar klientspecifik och vanliga (klient-oberoende) enkel inloggning och enkel utloggning slutpunkter. Dessa URL: er representerar adresserbara platser – de är inte bara identifierare – så att du kan gå till slutpunkten för att läsa metadata.

* Klientspecifik slutpunkten finns på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. Den *<TenantDomainName>* är ett registrerat domännamn eller TenantID GUID för en Azure AD-klient. Federationsmetadata för klienten contoso.com är till exempel på: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Klient-oberoende slutpunkten finns på `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. I den här slutpunktsadress **vanliga** visas i stället för en klientdomännamn eller -ID.

Information om de dokument som federation metadata som publicerar Azure AD finns i [Federationsmetadata](azure-ad-federation-metadata.md).
