---
title: Extern delning av Office 365 och Azure Active Directory B2B-samarbete | Microsoft Docs
description: "anspråk mappning referens för Azure Active Directory B2B-samarbete"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/24/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 8486c7518e2cabb1d17a8f38442b7b5a77fddb01
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Extern delning av Office 365 och Azure Active Directory B2B-samarbete

Extern delning i Office 365 (OneDrive, SharePoint Online, enhetlig grupper, etc.) och Azure Active Directory (AD Azure) B2B-samarbete är tekniskt sett samma sak. Alla extern delning (utom OneDrive/SharePoint Online), inklusive gäster i Office 365-grupper, använder redan Azure AD B2B-samarbetsinbjudan API: er för att delas.

OneDrive/SharePoint Online finns en separat inbjudan manager. Stöd för extern delning i OneDrive/SharePoint Online igång innan Azure AD utvecklats stödet. Över tiden, OneDrive/SharePoint Online extern delning har uppstått flera funktioner och många miljoner användare som använder produkten har inbyggda delning mönster. Det finns emellertid vissa skillnader mellan hur OneDrive/SharePoint Online extern delning fungerar och hur fungerar Azure AD B2B-samarbete:

- OneDrive/SharePoint Online lägger till användare i katalogen när användare har lösts in deras inbjudan. Så innan åtgärd visas användaren i Azure AD-portalen. Om en användare inbjuder under tiden i en annan plats, skapas en ny inbjudan. Men när du använder Azure AD B2B-samarbete läggs användare omedelbart på inbjudan så att de visas överallt.

- Inlösning upplevelse i OneDrive/SharePoint Online ser ut upplevelse i Azure AD B2B-samarbete. När en användare redeems en inbjudan, upplevelser ser likadana ut.

- Azure AD B2B collaboration uppmanas användare kan nå från OneDrive/SharePoint Online delning av dialogrutor. OneDrive/SharePoint Online uppmanas användare visas också i Azure AD när de lösa deras inbjudan.

- För att hantera extern delning i OneDrive/SharePoint Online med Azure AD B2B-samarbete, ställa in OneDrive/SharePoint Online extern delning inställningen för att **Tillåt endast att dela med externa användare redan i katalogen**. Användare kan gå till externt delade webbplatser och välj från externa samarbetspartners som administratören har lagts till. Administratören kan lägga till externa samarbetspartners via B2B-samarbetsinbjudan API: er.

![OneDrive/SharePoint Online extern delning](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Egenskaper för användare av B2B-samarbete](active-directory-b2b-user-properties.md)
* [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)
* [Delegera B2B-samarbete inbjudningar](active-directory-b2b-delegate-invitations.md)
* [Dynamiska grupper och B2B-samarbete](active-directory-b2b-dynamic-groups.md)
* [B2B-samarbete kod och PowerShell-exempel](active-directory-b2b-code-samples.md)
* [Konfigurera SaaS-appar för B2B-samarbete](active-directory-b2b-configure-saas-apps.md)
* [Användartoken för B2B-samarbete](active-directory-b2b-user-token.md)
* [B2B-samarbete användaranspråk mappning](active-directory-b2b-claims-mapping.md)
* [Aktuella begränsningar för B2B-samarbete](active-directory-b2b-current-limitations.md)
