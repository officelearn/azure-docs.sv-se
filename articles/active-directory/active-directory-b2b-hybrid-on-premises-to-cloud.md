---
title: Bevilja hanteras lokalt partnerkonton som har åtkomst till molnresurser som Azure AD B2B-användare | Microsoft Docs
description: Ge hanteras lokalt externa partners åtkomst till både lokala och molnresurser med samma autentiseringsuppgifter med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/24/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: dc4c8b3f5cb20eaf76fc0ee47d195aca26d06f90
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928582"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Bevilja hanteras lokalt partneråtkomst till molnresurser med Azure AD B2B-samarbete

Innan Azure Active Directory (Azure AD) har organisationer med lokala identitetssystem traditionellt hanterade partnerkonton i sina lokala katalog. I en organisation, när du startar flytta appar till Azure AD, som du vill kontrollera att dina partners kan komma åt resurser som de behöver. Det bör inte roll om resurserna som finns lokalt eller i molnet. Du bör också, partneranvändarna ska kunna använda samma autentiseringsuppgifter inloggningen för både lokalt och Azure AD-resurser. 

Om du skapar konton för externa partner i din lokala katalog (exempelvis kan du skapa ett konto med ett namn på ”wmoran” för en extern användare med namnet Wendy Moran i domänen partners.contoso.com), kan du nu synkronisera dessa konton till den moln. Mer specifikt kan du använda Azure AD Connect för att synkronisera partnerkonton till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Detta gör att partneranvändarna åtkomst till molnresurser med samma autentiseringsuppgifter som sina lokala konton utan att ge dem mer åtkomst än de behöver. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifiera unika attribut för UserType

Innan du aktiverar synkronisering av UserType-attributet måste du bestämma hur att härleda UserType-attributet från lokala Active Directory. Med andra ord vilka parametrar i din lokala miljö är unik för din externa samarbetspartners? Fastställa en parameter som särskiljer dessa externa samarbetspartners från medlemmarna i din organisation.

Två vanliga metoder för detta är att:

- Ange en oanvända lokala Active Directory-attribut (till exempel extensionAttribute1) ska användas som källattributet. 
- Du kan också härledd värdet för UserType-attributet från andra egenskaper. Till exempel du vill synkronisera alla användare som gäst om sina lokala Active Directory-UserPrincipalName attributet slutar med domänen *@partners.contoso.com*.
 
För detaljerad attributet krav, se [aktivera synkroniseringen av UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurera Azure AD Connect sync användare till molnet

När du identifierar unikt attribut kan du konfigurera Azure AD Connect att synkronisera dessa användare till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Dessa användare kan särskiljas från B2B-användare som skapats via Azure AD B2B-samarbete inbjudan processen från ett tillstånd synvinkel.

Implementering instruktioner finns i [aktivera synkroniseringen av UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för hybrid organisationer](active-directory-b2b-hybrid-organizations.md)
- [Grant B2B-användare i Azure AD åtkomst till lokala program](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- En översikt av Azure AD Connect finns [integrera dina lokala kataloger med Azure Active Directory](connect/active-directory-aadconnect.md).

