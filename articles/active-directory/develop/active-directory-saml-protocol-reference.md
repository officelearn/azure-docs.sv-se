---
title: "Referens för Azure AD-SAML-protokollet | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över enkel inloggning och enkel Sign-Out SAML-profiler i Azure Active Directory."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: priyamo
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: d5ffba5d0c409fe9de7a9e82c6faa4ca2702ab95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# Hur Azure Active Directory använder SAML-protokoll
Azure Active Directory (AD Azure) använder SAML 2.0-protokollet för att programmen ska tillhandahålla en enkel inloggning för sina användare. Den [enkel inloggning](active-directory-single-sign-on-protocol-reference.md) och [enkel utloggning](active-directory-single-sign-out-protocol-reference.md) SAML-profiler i Azure AD förklarar hur SAML intyg, protokoll och bindningar används i tjänsten identitet provider.

SAML-protokoll kräver identitetsprovider (Azure AD) och service provider (programmet) för att utbyta information om sig själva.

När ett program registreras med Azure AD, registrerar apputvecklaren federation-relaterad information med Azure AD. Detta inkluderar den **omdirigerings-URI** och **Metadata URI** av programmet.

Azure AD-använder den **Metadata URI** för Molntjänsten att hämta signeringsnyckeln och logga ut URI för Molntjänsten. Om programmet inte har stöd för metadata URI utvecklaren måste kontakta Microsoft-supporten om du vill ange logga ut URI och nyckel för signeringscertifikatet.

Azure Active Directory visar specifika klient- och vanliga (oberoende av klient) enkel inloggning och enkel utloggning slutpunkter. Dessa URL: er representerar adresserbara platser – de är inte bara en identifierare--så att du kan gå till slutpunkten att läsa metadata.

* Klient-specifika slutpunkten finns på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Den <TenantDomainName> är ett registrerat domännamn eller TenantID GUID för en Azure AD-klient. Till exempel federationsmetadata för contoso.com-klienten är på: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Slutpunkten oberoende av klient finns på `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. I den här slutpunktsadress **vanliga** visas i stället för en klient domän- eller -ID.

Information om Federationsmetadata dokument som Azure AD publicerar finns [Federationsmetadata](active-directory-federation-metadata.md).
