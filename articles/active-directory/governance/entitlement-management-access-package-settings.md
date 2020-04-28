---
title: Dela länk för att begära ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du delar en länk för att begära ett åtkomst paket i Azure Active Directory rättighets hantering.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968752"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Dela länk för att begära ett Access-paket i Azure AD-hantering av rättigheter

De flesta användare i din katalog kan logga in på portalen My Access och automatiskt se en lista över de åtkomst paket som de kan begära. Men för externa affärs partner användare som ännu inte finns i din katalog måste du skicka dem en länk som de kan använda för att begära ett Access-paket. 

Så länge katalogen för Access-paketet är [aktive rad för externa användare](entitlement-management-catalog-create.md) och du har en [princip för den externa användarens katalog](entitlement-management-access-package-request-policy.md), kan den externa användaren använda länken My Access Portal för att begära åtkomst paketet.

## <a name="share-link-to-request-an-access-package"></a>Dela länk för att begära ett Access-paket

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. På sidan Översikt kopierar du **länken min åtkomst Portal**.

    ![Översikt över åtkomst paket – länken min åtkomst Portal](./media/entitlement-management-shared/my-access-portal-link.png)

    Det är viktigt att du kopierar hela min åtkomst Portal-länken när du skickar den till en intern affärs partner. Detta säkerställer att partnern får åtkomst till din katalogs Portal för att göra sin begäran. Länken börjar med `myaccess`, innehåller ett katalog tips och slutar med ett Access-paket-ID.  (För amerikanska myndigheter är domänen i min åtkomst Portal länk `myaccess.microsoft.us`.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. E-posta eller skicka länken till din externa affärs partner. De kan dela länken med sina användare för att begära åtkomst paketet.

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett Access-paket](entitlement-management-request-access.md)
- [Godkänn eller neka åtkomst begär Anden](entitlement-management-request-approve.md)