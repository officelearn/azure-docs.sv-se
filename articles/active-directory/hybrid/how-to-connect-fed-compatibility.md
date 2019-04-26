---
title: Kompatibilitetslista för Azure AD-federation
description: Den här sidan har icke-Microsoft identitetsleverantörer som kan användas för att implementera enkel inloggning.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5090101c486562e33de56402db348c6038c8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60244750"
---
# <a name="azure-ad-federation-compatibility-list"></a>Kompatibilitetslista för Azure AD-federation
Azure Active Directory tillhandahåller enkel inloggning och förbättrad säkerhet för åtkomst för Office 365 och andra Microsoft Online-tjänster för hybriddata och molnbaserad implementeringar utan någon lösning från tredje part. Office 365, som de flesta av Microsofts onlinetjänster, är integrerad med Azure Active Directory för katalogtjänster, autentisering och auktorisering. Azure Active Directory tillhandahåller också enkel inloggning till tusentals SaaS-program och lokala webbprogram. Se Azure Active Directory [programgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) för SaaS-program som stöds. 

## <a name="idp-validation"></a>IDP-verifiering
Om din organisation använder en federationslösning från tredje part, kan du konfigurera enkel inloggning för din lokala Active Directory-användare med Microsoft Online-tjänster, till exempel Office 365, förutsatt att den tredje parts federation är kompatibla med Azure Active Directory.  Frågor angående kompatibilitet kan du kontakta din identitetsprovider.  Om du vill se en lista över identitetsleverantörer som tidigare har testats för kompatibilitet med Azure AD av Microsoft, klicka på [här](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft tillhandahåller inte längre valideringstestning till oberoende identitetsleverantörer för kompatibilitet med Azure Active Directory. Om du vill testa din produkt för samverkan ser du följande [riktlinjer](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Nästa steg

- [Integrerar dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
