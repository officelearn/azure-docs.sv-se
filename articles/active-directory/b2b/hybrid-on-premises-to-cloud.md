---
title: Ge hanteras lokalt partnerkonton åtkomst till molnresurser som Azure AD B2B-användare | Microsoft Docs
description: Ge hanteras lokalt externa partners åtkomst till både lokala och molnresurser med samma autentiseringsuppgifter med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cbcb6095c59089008bd9a95b9bb0a3ddaa70a58
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984068"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Bevilja hanteras lokalt partneråtkomst till molnresurser med hjälp av Azure AD B2B-samarbete

Innan Azure Active Directory (AD Azure) har organisationer med lokala identitetssystem traditionellt hanterade partnerkonton i sina lokala katalog. I en organisation, när du börjar flytta appar till Azure AD, som du vill se till att dina partner kan komma åt de resurser som krävs. Det bör inte spelar någon roll om resurserna som finns lokalt eller i molnet. Dessutom bör användarna partner för att kunna använda samma autentiseringsuppgifter inloggning för både lokala och Azure AD-resurser. 

Om du skapar konton för externa partner i din lokala katalog (exempelvis kan du skapa ett konto med ett namn för ”wmoran” för en extern användare med namnet Wendy Moran i din partners.contoso.com domän), nu kan du synkronisera dessa konton till den moln. Mer specifikt kan du använda Azure AD Connect för att synkronisera partnerkonton till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Detta gör det möjligt för partner användarna åtkomst till molnresurser med samma autentiseringsuppgifter som deras lokala konton utan att ge dem mer åtkomst än vad de behöver. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifiera unika attribut för UserType

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur att härleda UserType-attributet från den lokala Active Directory. Med andra ord, vilka parametrar i din lokala miljö är unik för din externa medarbetare? Fastställa en parameter som särskiljer dessa externa medarbetare från medlemmar i din organisation.

Två vanliga metoder för detta är att:

- Ange en oanvända lokala Active Directory-attributet (till exempel extensionAttribute1) ska användas som källattributet. 
- Du kan också härleds värdet för UserType-attributet från andra egenskaper. Exempelvis kan du vill synkronisera alla användare som gäst om deras lokala Active Directory-UserPrincipalName-attribut som slutar med domänen *@partners.contoso.com*.
 
Detaljerad attributet krav finns i [Aktivera synkronisering av UserType](../connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurera Azure AD Connect att synkronisera användare till molnet

När du identifierar unikt attribut kan du konfigurera Azure AD Connect att synkronisera dessa användare i molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Från en auktorisering synsätt är dessa användare identiska med B2B-användare som skapats via Azure AD B2B-samarbete inbjudningsprocessen.

Implementering anvisningar finns i [Aktivera synkronisering av UserType](../connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för hybridorganisationer](hybrid-organizations.md)
- [Grant B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- En översikt av Azure AD Connect finns i [integrerar dina lokala kataloger med Azure Active Directory](../connect/active-directory-aadconnect.md).

