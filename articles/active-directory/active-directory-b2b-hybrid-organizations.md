---
title: B2B-samarbete för hybrid - organisationer i Azure Active Directory | Microsoft Docs
description: Ge partners åtkomst till både lokalt och molnresurser med Azure AD B2B-samarbete.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/26/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 748d5f7497c3ab65a669bf3f205e9486dfcaed92
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samarbete för hybrid organisationer

Azure Active Directory (AD Azure) B2B-samarbete är det enkelt för dig att ge dina externa partners åtkomst till appar och resurser i din organisation. Detta gäller även en hybrid-konfiguration där du har både lokala och molnbaserade resurser. Det spelar ingen roll om du för närvarande hanterar externa partnerkonton lokalt i ditt lokala identitetssystem, eller om du hanterar externa konton i molnet som Azure AD B2B-användare. Du kan nu tilldela dessa användare åtkomst till resurser i båda fallen använder du samma inloggningsuppgifter inloggning för båda miljöerna.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Grant B2B-användare i Azure AD åtkomst till dina lokala appar

Om organisationen använder Azure AD B2B-samarbetesfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD, kan du nu ange dessa B2B-användare åtkomst till lokala appar.

För appar som använder SAML-baserad autentisering, kan du göra dessa appar tillgängliga för B2B användare via Azure-portalen med hjälp av Azure AD Application Proxy för autentisering.

För appar som använder integrerad Windows Windowsautentisering (IWA) med Kerberos-begränsad delegering (KCD) kan använda du också Azure AD-Proxy för autentisering. Om tillstånd ska fungera krävs dock ett användarobjekt i lokala Windows Server Active Directory. Det finns två metoder som du kan använda för att skapa lokala användare-objekt som representerar dina B2B-gästanvändare.

- Du kan använda Microsoft Identity Manager (MIM) 2016 SP1 och MIM-hanteringsagenten för Microsoft Graph.
- Du kan använda ett PowerShell-skript. (Den här lösningen kräver inte MIM.)

Mer information om hur du implementerar dessa lösningar finns [Grant B2B-användare i Azure AD åtkomst till lokala program](active-directory-b2b-hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Bevilja hanteras lokalt partnerkonton som har åtkomst till molnresurser

Innan Azure AD har traditionellt organisationer med lokala identitetssystem hanterade partnerkonton i sina lokala katalog. Om du är sådan organisation, som du vill kontrollera att dina partners fortsätter att ha åtkomst när du flyttar dina appar och andra resurser i molnet. Vi rekommenderar för dessa användare använder samma uppsättning autentiseringsuppgifter för åtkomst både till molnet och lokala resurser. 

Vi nu erbjudande metoder där du kan använda Azure AD Connect för att synkronisera dessa lokala konton till molnet som ”gästanvändare”, där konton fungerar precis som Azure AD B2B-användare.

För att skydda företagets data, kan du styra åtkomsten till precis rätt resurser och konfigurera auktoriseringsprinciper som behandlar dessa gästanvändare annorlunda än dina anställda.

Implementeringsdetaljer, se [bevilja hanteras lokalt partnerkonton som har åtkomst till molnresurser med Azure AD B2B-samarbete](active-directory-b2b-hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Nästa steg

- [Grant B2B-användare i Azure AD åtkomst till lokala program](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- [Bevilja hanteras lokalt partneråtkomst till molnresurser med Azure AD B2B-samarbete](active-directory-b2b-hybrid-on-premises-to-cloud.md)


