---
title: Hantera åtkomst till Azure-resurser med Azure Active Directory
description: Lär dig mer om olika sätt att hantera åtkomst till Azure-resurser med hjälp av olika funktioner i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 55bd18b127fea8a06e58f14873a584f0b5849906
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203245"
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Hantera åtkomst till Azure-resurser med Azure Active Directory

Identitets- och åtkomsthantering för molnresurser är en viktig funktion för alla företag som använder molnet. Azure Active Directory (AD Azure) är identitets- och systemet för Microsoft Azure.  

Innan du börjar utforska stödfiler funktion områden av Azure AD, ta en titt på följande video: ”låsa åtkomsten till Azure-molnet använder SSO, roller baserad åtkomstkontroll och Conditional”. Du lär dig mer om:

- Metodtips för att konfigurera enkel inloggning till Azure-portalen med lokala Active Directory.
- Med Azure RBAC för detaljerad åtkomstkontroll till resurser i prenumerationer.
- Tvingande stark autentisering regler med hjälp av Azure AD villkorlig åtkomst.
- Begreppet hanterade tjänstidentiteten, där Azure-resurser automatiskt kan autentisera till Azure-tjänster och utvecklare behöver inte hantera API-nycklar och hemligheter.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Funktionsområden
Azure AD innehåller följande funktioner för att hantera åtkomst till Azure-resurser:

|||
|---|---|
| [Förhållandet mellan Azure AD-klienter och prenumerationer](rbac-and-directory-admin-roles.md) | Mer information om hur Azure AD är betrodd källa för användare och grupper för en Azure-prenumeration. |
| [Rollbaserad åtkomstkontroll (RBAC)](overview.md) | Ger detaljerad åtkomsthantering via roller som tilldelas användare, grupper eller tjänstens huvudnamn. |
| [Privileged Identity Management med RBAC](pim-azure-resource.md) | Kontrollen mycket privilegierad åtkomst genom att tilldela Privilegierade roller just-in-time. |
| [Villkorlig åtkomst för hantering av Azure](conditional-access-azure-management.md) | Konfigurera principer för villkorlig åtkomst att tillåta eller blockera åtkomst till av Azure-hanteringsslutpunkter. |
| [Hanterade tjänstidentiteten (MSI)](../active-directory/pp/msi-overview.md) | Ge Azure-resurser som virtuella datorer som sin egen identitet så att du inte behöver placera autentiseringsuppgifter i din kod. |

 