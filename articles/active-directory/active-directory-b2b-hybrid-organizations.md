---
title: "Azure Active Directory B2B-samarbete för hybrid organisationer | Microsoft Docs"
description: "Ge partners åtkomst till både lokala och molnresurser med Azure AD B2B-samarbete"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samarbete för hybrid organisationer

I en hybrid organisation, där du har både lokalt och molnresurser, kanske du vill ge externa partners åtkomst till både lokala och molnresurser. För åtkomst till lokala resurser, kan du hantera partner kontona lokalt i din lokala Active Directory-miljö. Partners logga in med autentiseringsuppgifterna för kontopartner åtkomst till organisationens resurser. Om du vill ge partners åtkomst till molnresurser med samma autentiseringsuppgifter, nu kan du använda Azure Active Directory (AD Azure) Connect för att synkronisera partnerkonton till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst).

## <a name="identify-unique-attributes-for-usertype"></a>Identifiera unika attribut för UserType

Innan du aktiverar synkronisering av UserType-attributet måste du bestämma hur att härleda UserType-attributet från lokala Active Directory. Med andra ord vilka parametrar i din lokala miljö är unik för din externa samarbetspartners? Fastställa en parameter som särskiljer dessa externa samarbetspartners från medlemmarna i din organisation.

Två vanliga metoder för detta är att:

- Ange en oanvända lokala Active Directory-attribut (till exempel extensionAttribute1) ska användas som källattributet. 
- Du kan också härledd värdet för UserType-attributet från andra egenskaper. Till exempel du vill synkronisera alla användare som gäst om sina lokala Active Directory-UserPrincipalName attributet slutar med domänen  *@partners.fabrikam123.org* .
 
För detaljerad attributet krav, se [aktivera synkroniseringen av UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurera Azure AD Connect sync användare till molnet

När du identifierar unikt attribut kan du konfigurera Azure AD Connect att synkronisera dessa användare till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Dessa användare kan särskiljas från B2B-användare som skapats via Azure AD B2B-samarbete inbjudan processen från ett tillstånd synvinkel.

Implementering instruktioner finns i [aktivera synkroniseringen av UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B-samarbete finns [vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- En översikt av Azure AD Connect finns [integrera dina lokala kataloger med Azure Active Directory](connect/active-directory-aadconnect.md).

