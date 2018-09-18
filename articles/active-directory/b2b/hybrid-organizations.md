---
title: B2B-samarbete för hybridorganisationer - Azure Active Directory | Microsoft Docs
description: Ge partner åtkomst till både lokalt och i molnresurser med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 773ae5aefab59e79822c0320773753b47cfefdeb
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983877"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samarbete för hybridorganisationer

Azure Active Directory (Azure AD) B2B-samarbete gör det enkelt för dig att ge din externa partners åtkomst till appar och resurser i din organisation. Detta gäller även i en hybrid-konfiguration där du har både lokala och molnbaserade resurser. Det spelar ingen roll om du för närvarande hanterar externa partnerkonton lokalt i det lokala identitetssystemet, eller om du hanterar externa konton i molnet som Azure AD B2B-användare. Du kan nu tilldela dessa användare åtkomst till resurser på någon av platserna med autentiseringsuppgifterna för samma inloggning för båda miljöerna.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Grant B2B-användare i Azure AD åtkomst till lokala appar

Om din organisation använder Azure AD B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD, kan du nu ange dessa B2B-användare åtkomst till lokala appar.

För appar som använder SAML-baserad autentisering, kan du tillgängliggöra dessa appar för B2B-användare via Azure-portalen med hjälp av Azure AD Application Proxy för autentisering.

För appar som använder integrerad Windows autentisering (IWA) med Kerberos-begränsad delegering (KCD) kan använda du också Azure AD-Proxy för autentisering. Auktorisering att fungera, krävs dock ett användarobjekt i den lokala Windows Server Active Directory. Det finns två metoder som du kan använda för att skapa lokala objekt som representerar din B2B-gästanvändare.

- Du kan använda Microsoft Identity Manager (MIM) 2016 SP1 och MIM-hanteringsagenten för Microsoft Graph.
- Du kan använda ett PowerShell-skript. (Den här lösningen kräver inte MIM.)

Mer information om hur du implementerar dessa lösningar finns i [Grant B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Ge hanteras lokalt partnerkonton åtkomst till molnresurser

Innan Azure AD har organisationer med lokala identitetssystem traditionellt hanterade partnerkonton i sina lokala katalog. Om du är sådan organisation kan du se till att dina partner fortsätter att ha åtkomst när du flyttar dina appar och andra resurser till molnet. Vi rekommenderar för dessa användare ska använda samma uppsättning autentiseringsuppgifter för åtkomst både till molnet och lokala resurser. 

Vi nu erbjudandet metoder där du kan använda Azure AD Connect för att synkronisera dessa lokala konton till molnet som ”gästanvändare”, där konton som fungerar precis som Azure AD B2B-användare.

För att skydda företagets data, kan du styra åtkomsten till bara rätt resurser och konfigurera auktoriseringsprinciper som behandlar dessa gästanvändare annorlunda än dina anställda.

Mer information om implementering finns i [bevilja hanteras lokalt partnerkonton åtkomst till molnresurser med hjälp av Azure AD B2B-samarbete](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Nästa steg

- [Grant B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- [Bevilja hanteras lokalt partneråtkomst till molnresurser med hjälp av Azure AD B2B-samarbete](hybrid-on-premises-to-cloud.md)


