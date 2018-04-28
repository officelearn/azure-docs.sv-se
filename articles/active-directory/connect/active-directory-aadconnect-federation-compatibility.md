---
title: Kompatibilitetslista för Azure AD-federation
description: Den här sidan har icke-Microsoft leverantörer som kan användas för att implementera enkel inloggning.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: billmath
ms.openlocfilehash: e7239ae88c6b4e56fa7c49f64c30fe602d8ec5fe
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-federation-compatibility-list"></a>Kompatibilitetslista för Azure AD-federation
Azure Active Directory tillhandahåller enkel inloggning på och förbättrad säkerhet för åtkomst för Office 365 och andra Microsoft Online-tjänster för hybrid och endast molnbaserad implementeringar utan någon lösning från tredje part. Office 365, som de flesta av Microsofts onlinetjänster, är integrerad med Azure Active Directory för katalogtjänster, autentisering och auktorisering. Azure Active Directory ger även enkel inloggning till tusentals SaaS-program och lokala webbprogram. Se Azure Active Directory [programgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps) för SaaS-program som stöds. 

## <a name="idp-validation"></a>IDP-validering
Om din organisation använder en lösning från tredje part federation, kan du konfigurera enkel inloggning för din lokala Active Directory-användare med Microsoft Online-tjänster, till exempel Office 365, under förutsättning att den tredje parts federation är kompatibla med Azure Active Directory.  Kontakta din identitetsleverantör om du har frågor angående kompatibilitet.  Om du vill se en lista över identitetsleverantörer som tidigare har testats för kompatibilitet med Azure AD från Microsoft, klicka på [här](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft tillhandahåller inte längre validering testningen till oberoende identitetsleverantörer för kompatibilitet med Azure Active Directory. Om du vill testa produktens samverkan läser du dessa [riktlinjer](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Nästa steg

- [Integrerar dina lokala kataloger med Azure Active Directory](active-directory-aadconnect.md)
- [Azure AD Connect och federation](active-directory-aadconnectfed-whatis.md)
