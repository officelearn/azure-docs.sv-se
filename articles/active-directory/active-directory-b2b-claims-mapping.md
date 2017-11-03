---
title: "B2B-samarbete användaranspråk mappning i Azure Active Directory | Microsoft Docs"
description: "anspråk mappning referens för Azure Active Directory B2B-samarbete"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B-samarbete användaranspråk mappning i Azure Active Directory

Azure Active Directory (AD Azure) stöder anpassa anspråk som utfärdats i SAML-token för B2B-samarbete användare. När en användare autentiseras för utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn. Du kan visa eller redigera anspråk skickas i SAML-token till programmet under fliken attribut.

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token.

1. Programmet har skrivits till kräver en annan uppsättning anspråk URI: er eller anspråksvärden

2. NameIdentifier att anspråket ska vara något annat än användarens huvudnamn som lagras i Azure Active Directory krävs för ditt program.

  ![Visa anspråk i SAML-token](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Information om hur du lägger till och redigera anspråk finns i följande artikel på anspråk anpassning [anpassa anspråk som utfärdats i SAML-token för förintegrerade appar i Azure Active Directory](develop/active-directory-saml-claims-customization.md). Användare, mappning NameID och UPN cross-klient som inte av säkerhetsskäl för B2B-samarbete.


## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Egenskaper för användare av B2B-samarbete](active-directory-b2b-user-properties.md)
* [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)
* [Delegera B2bB samarbete inbjudningar](active-directory-b2b-delegate-invitations.md)
* [Dynamiska grupper och B2B-samarbete](active-directory-b2b-dynamic-groups.md)
* [B2B-samarbete kod och PowerShell-exempel](active-directory-b2b-code-samples.md)
* [Konfigurera SaaS-appar för B2B-samarbete](active-directory-b2b-configure-saas-apps.md)
* [Extern delning av Office 365](active-directory-b2b-o365-external-user.md)
* [Användartoken för B2B-samarbete](active-directory-b2b-user-token.md)
* [Aktuella begränsningar för B2B-samarbete](active-directory-b2b-current-limitations.md)
