---
title: "Hur du lägger till ett program för flera innehavare i Azure AD application Gallery | Microsoft Docs"
description: Beskriver hur du kan ange anpassade utvecklade flera innehavare program i Azure AD Application Gallery
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 969273d1bc0750685a0b2010670915f4183ac8ed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Hur du lägger till ett program för flera innehavare i Azure AD application Gallery

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD Application Gallery?

Azure AD Application Gallery är ett bra sätt att få programmet framför alla miljontals Azure Active Directory-kunder, och utöka effekten av ditt program i marketplace. I nedanstående steg förklarar hur du kan visa ditt program i Azure AD Application Gallery.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Om programmet stöder SAML eller OpenIDConnect
Om du har ett program för flera innehavare som du vill visa i Azure AD Application Gallery, måste du först kontrollera att ditt program stöder en av följande enkel inloggning tekniker:

1. **OpenID Connect** -Direktintegrering med Azure AD med OpenID Connect för autentisering och Azure AD medgivande API för konfigurationen. Om du bara startar en integration och programmet har inte stöd för SAML är rekommenderar-läge.
2. **SAML** – ditt program redan har möjlighet att konfigurera från tredje part identitetsleverantörer med SAML-protokoll.

Om programmet stöder en av dessa lägen för enkel inloggning och du vill visa en lista med flera innehavare programmet i Azure AD Application Gallery, kan du följa stegen i dokumentet nedan. Att komma igång snabbt skicka ett e-postmeddelande till  **waadpartners@microsoft.com** .

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Om ditt program inte stöder SAML eller OpenIDConnect
Även om programmet inte stöder en av dessa lägen, integrera vi fortfarande den i vår galleriet med vår lösenord Single Sign-on-teknik. Om du vill utforska det här alternativet kan du skicka ett e-postmeddelande till  **waadpartners@microsoft.com** .

## <a name="next-steps"></a>Nästa steg
[Visa en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
