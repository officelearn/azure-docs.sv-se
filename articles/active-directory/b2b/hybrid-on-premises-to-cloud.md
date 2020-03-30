---
title: Synkronisera lokala partnerkonton med molnet som B2B-användare – Azure AD
description: Ge lokalt hanterade externa partner åtkomst till både lokala resurser och molnresurser med samma autentiseringsuppgifter med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272604"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser med Azure AD B2B-samarbete

Innan Azure Active Directory (Azure AD) har organisationer med lokala identitetssystem traditionellt hanterat partnerkonton i sin lokala katalog. I en sådan organisation, när du börjar flytta appar till Azure AD, vill du se till att dina partner kan komma åt de resurser de behöver. Det bör inte spela någon roll om resurserna är lokala eller i molnet. Du vill också att dina partneranvändare ska kunna använda samma inloggningsuppgifter för både lokala och Azure AD-resurser. 

Om du skapar konton för dina externa partner i din lokala katalog (till exempel skapar du ett konto med ett inloggningsnamn "wmoran" för en extern användare med namnet Wendy Moran i din partners.contoso.com-domän) ) kan du nu synkronisera dessa konton till Moln. Specifikt kan du använda Azure AD Connect för att synkronisera partnerkontona till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Detta gör det möjligt för dina partneranvändare att komma åt molnresurser med samma autentiseringsuppgifter som sina lokala konton, utan att ge dem mer åtkomst än de behöver. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifiera unika attribut för UserType

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur du ska härleda UserType-attributet från lokal Active Directory. Med andra ord, vilka parametrar i din lokala miljö är unika för dina externa medarbetare? Bestäm en parameter som skiljer dessa externa medarbetare från medlemmar i din egen organisation.

Två gemensamma tillvägagångssätt för detta är att:

- Ange ett oanvänt lokalt Active Directory-attribut (till exempel extensionAttribute1) som ska användas som källattribut. 
- Alternativt härleda värdet för UserType-attributet från andra egenskaper. Du vill till exempel synkronisera alla användare som gäst om deras lokala Active Directory * \@* UserPrincipalName-attribut slutar med domänen partners.contoso.com .
 
Detaljerade attributkrav finns i [Aktivera synkronisering av UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurera Azure AD Connect för synkronisering av användare till molnet

När du har identifierat det unika attributet kan du konfigurera Azure AD Connect för att synkronisera dessa användare till molnet som Azure AD B2B-användare (det vill säga användare med UserType = Gäst). Från en auktoriseringssynpunkt kan dessa användare inte skiljas från B2B-användare som skapats via Azure AD B2B-samarbetsanbjudan.

Instruktioner för implementering finns i [Aktivera synkronisering av UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för hybridorganisationer](hybrid-organizations.md)
- [Bevilja B2B-användare i Azure AD-åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- En översikt över Azure AD Connect finns i [Integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

