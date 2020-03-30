---
title: Tjänstberoenden för villkorlig åtkomst - Azure Active Directory
description: Lär dig hur villkor används i Azure Active Directory Villkorlig åtkomst för att utlösa en princip.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380016"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Vad är tjänstberoenden i Azure Active Directory Villkorlig åtkomst? 

Med principer för villkorlig åtkomst kan du ange åtkomstkrav för webbplatser och tjänster. Dina åtkomstkrav kan till exempel omfatta krav på MFA (Multi Factor Authentication) eller [hanterade enheter](require-managed-devices.md). 

När du öppnar en webbplats eller tjänst direkt är effekten av en relaterad princip vanligtvis lätt att bedöma. Om du till exempel har en princip som kräver att MFA för SharePoint Online är konfigurerad, tillämpas MFA för varje inloggning till SharePoint-webbportalen. Det är dock inte alltid rakt fram att bedöma effekten av en princip eftersom det finns molnappar med beroenden till andra molnappar. Microsoft Teams kan till exempel ge åtkomst till resurser i SharePoint Online. Så när du öppnar Microsoft Teams i vårt nuvarande scenario omfattas du också av SharePoint MFA-principen.   

## <a name="policy-enforcement"></a>Policyframtvingande 

Om du har konfigurerat ett tjänstberoende kan principen tillämpas med hjälp av tidig eller sen tvingande efterlevnad. 

- **Tidig principförseleving** innebär att en användare måste uppfylla den beroende tjänstprincipen innan han öppnar den anropande appen. En användare måste till exempel uppfylla SharePoint-principen innan han eller hon loggar in på MS Teams. 
- **Sen principkontroll** sker när användaren loggar in på den anropande appen. Tvingande skjuts upp till när appbegäranden anropas, en token för tjänsten nedströms. Exempel är MS Teams som använder Planner och Office.com åtkomst till SharePoint. 

Diagrammet nedan illustrerar MS Teams tjänstberoenden. Heldragna pilar indikerar tidig bunden verkställighet den streckade pilen för Planner indikerar sen bunden verkställighet. 

![SERVICEberoenden för MS Teams](./media/service-dependencies/01.png)

Du bör också ange gemensamma principer för relaterade appar och tjänster när det är möjligt. Att ha en konsekvent säkerhetsställning ger dig den bästa användarupplevelsen. Om du till exempel anger en gemensam princip i Exchange Online, SharePoint Online, Microsoft Teams och Skype för företag minskar antalet oväntade uppmaningar som kan uppstå från olika policyer som tillämpas på underordnade tjänster. 

I tabellen nedan visas ytterligare tjänstberoenden, där klientapparna måste uppfylla  

| Klientappar         | Nedströms tjänst                          | Verkställighet |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal och API) | Tidigt bunden |
| Microsoft-klassrum | Exchange                                    | Tidigt bunden |
|                     | SharePoint                                  | Tidigt bunden |
| Microsoft Teams     | Exchange                                    | Tidigt bunden |
|                     | MS-planerare                                  | Sen bunden  |
|                     | SharePoint                                  | Tidigt bunden |
|                     | Skype för företag – Online                   | Tidigt bunden |
| Office-portal       | Exchange                                    | Sen bunden  |
|                     | SharePoint                                  | Sen bunden  |
| Outlook-grupper      | Exchange                                    | Tidigt bunden |
|                     | SharePoint                                  | Tidigt bunden |
| PowerApps           | Microsoft Azure Management (portal och API) | Tidigt bunden |
|                     | Windows Azure Active Directory              | Tidigt bunden |
| Project             | Dynamics CRM                                | Tidigt bunden |
| Skype för företag  | Exchange                                    | Tidigt bunden |
| Visual Studio       | Microsoft Azure Management (portal och API) | Tidigt bunden |
| Microsoft Forms     | Exchange                                    | Tidigt bunden |
|                     | SharePoint                                  | Tidigt bunden |
| Microsoft To-Do     | Exchange                                    | Tidigt bunden |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du implementerar villkorlig åtkomst i din miljö finns i [Planera distributionen för villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
