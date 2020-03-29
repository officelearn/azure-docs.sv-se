---
title: Dela länk för att begära ett åtkomstpaket i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du delar länk för att begära ett åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968752"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Dela länk för att begära ett åtkomstpaket i Azure AD-berättigandehantering

De flesta användare i katalogen kan logga in på My Access-portalen och automatiskt se en lista över åtkomstpaket som de kan begära. För externa affärspartneranvändare som ännu inte finns i katalogen måste du dock skicka en länk till dem som de kan använda för att begära ett åtkomstpaket. 

Så länge katalogen för åtkomstpaketet är [aktiverad för externa användare](entitlement-management-catalog-create.md) och du har en [princip för den externa användarens katalog](entitlement-management-access-package-request-policy.md)kan den externa användaren använda länken Min åtkomstportal för att begära åtkomstpaketet.

## <a name="share-link-to-request-an-access-package"></a>Dela länk för att begära ett åtkomstpaket

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Kopiera **länken Min Access-portal**på sidan Översikt .

    ![Översikt över åtkomstpaket - Länken Min Access-portal](./media/entitlement-management-shared/my-access-portal-link.png)

    Det är viktigt att du kopierar hela Min Access-portallänk när du skickar den till en intern affärspartner. Detta säkerställer att partnern får tillgång till katalogens portal för att göra sin begäran. Länken börjar `myaccess`med , innehåller en katalog ledtråd och slutar med ett åtkomstpaket-ID.  (För den amerikanska regeringen kommer domänen i `myaccess.microsoft.us`länken Min Åtkomst-portal att vara .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Skicka länken via e-post eller skicka länken till din externa affärspartner. De kan dela länken med sina användare för att begära åtkomstpaketet.

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett åtkomstpaket](entitlement-management-request-access.md)
- [Godkänna eller neka åtkomstbegäranden](entitlement-management-request-approve.md)