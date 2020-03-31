---
title: B2B-samarbete för hybridorganisationer - Azure AD
description: Ge partner åtkomst till både lokala resurser och molnresurser med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272477"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samarbete för hybridorganisationer

Azure Active Directory (Azure AD) B2B-samarbete gör det enkelt för dig att ge dina externa partner åtkomst till appar och resurser i din organisation. Detta gäller även i en hybridkonfiguration där du har både lokala och molnbaserade resurser. Det spelar ingen roll om du för närvarande hanterar externa partnerkonton lokalt i ditt lokala identitetssystem, eller om du hanterar externa konton i molnet som Azure AD B2B-användare. Du kan nu bevilja dessa användare åtkomst till resurser på båda platserna med samma inloggningsuppgifter för båda miljöerna.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Bevilja B2B-användare i Azure AD-åtkomst till dina lokala appar

Om din organisation använder Azure AD B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD kan du nu ge dessa B2B-användare åtkomst till lokala appar.

För appar som använder SAML-baserad autentisering kan du göra dessa appar tillgängliga för B2B-användare via Azure-portalen med Hjälp av Azure AD Application Proxy för autentisering.

För appar som använder integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD) använder du även Azure AD Proxy för autentisering. För att auktoriseringen ska fungera krävs dock ett användarobjekt i den lokala Active Directory för Windows Server. Det finns två metoder som du kan använda för att skapa lokala användarobjekt som representerar dina B2B-gästanvändare.

- Du kan använda MICROSOFT Identity Manager (MIM) 2016 SP1 och MIM-hanteringsagenten för Microsoft Graph.
- Du kan använda ett PowerShell-skript. (Den här lösningen kräver inte MIM.)

Mer information om hur du implementerar dessa lösningar finns [i Bevilja B2B-användare i Azure AD-åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser

Innan Azure AD har organisationer med lokala identitetssystem traditionellt hanterat partnerkonton i sin lokala katalog. Om du är en sådan organisation vill du se till att dina partner fortsätter att ha åtkomst när du flyttar dina appar och andra resurser till molnet. Helst vill du att dessa användare ska använda samma uppsättning autentiseringsuppgifter för att komma åt både molnresurser och lokala resurser. 

Vi erbjuder nu metoder där du kan använda Azure AD Connect för att synkronisera dessa lokala konton till molnet som "gästanvändare", där kontona fungerar precis som Azure AD B2B-användare.

För att skydda företagets data kan du styra åtkomsten till precis rätt resurser och konfigurera auktoriseringsprinciper som behandlar dessa gästanvändare på ett annat sätt än dina anställda.

Information om implementering finns i [Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser med Azure AD B2B-samarbete](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Nästa steg

- [Bevilja B2B-användare i Azure AD-åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- [Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser med Azure AD B2B-samarbete](hybrid-on-premises-to-cloud.md)


