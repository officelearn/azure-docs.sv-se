---
title: "Så här lägger du till en befintlig prenumeration i din Azure AD-katalog | Microsoft Docs"
description: "Så här lägger du till en befintlig prenumeration i din Azure AD-katalog"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e14d1c54a084a66bd33904b8c90e2973c0f7d6fb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Så här lägger du till en prenumeration i din Azure Active Directory
Det här avsnittet innehåller information om relationen mellan en Azure-prenumeration och Azure AD (Active Directory Azure) och hur du lägger till en befintlig prenumeration till din Azure Active Directory-katalog. Din Azure-prenumeration har en förtroenderelation med Azure AD, vilket innebär att den har förtroende för katalogen när det gäller att autentisera användare, tjänster och enheter. Flera prenumerationer kan ha förtroende för samma katalog, men varje prenumeration har bara förtroende för en katalog. 

Förtroendet som en prenumeration har för en katalog skiljer sig från relationen som den har med andra resurser i Azure (webbplatser, databaser och så vidare). Om en prenumeration går ut stoppas även åtkomsten till de övriga resurser som är associerade med prenumerationen. Men en Azure Active Directory-katalog finns kvar i Azure och du kan associera en annan prenumeration med katalogen och hantera kataloganvändarna med den nya prenumerationen.

Alla användare har en enda arbetskatalog som autentiserar dem, men de kan även vara gäster i andra kataloger. I Azure Active Directory kan du bara se de kataloger som ditt användarkonto är medlem eller gäst i.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure Active Directory-katalog
Du måste logga in med ett konto som finns i både den aktuella katalogen som prenumerationen är associerad till och i den katalog som du vill lägga till den i. 

1. Logga in i [Azure-kontocentret](https://account.azure.com/Subscriptions) med ett konto som är kontoadministratör för prenumerationen vars äganderätt du vill överföra.
2. Se till att den användare som ska vara prenumerationsägare finns i målkatalogen.
3. Klicka på **överföra äganderätten till prenumerationen**.
4. Ange mottagaren. Mottagaren hämtar automatiskt ett e-postmeddelande med en länk för godkännande.
5. Mottagaren klickar på länken och följer instruktionerna, inklusive att ange sin betalningsinformation. När mottagaren lyckas överförs prenumerationen. 
6. Standardkatalogen för prenumerationen ändras till den katalog där användaren finns.


## <a name="next-steps"></a>Nästa steg
* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](active-directory-understanding-resource-access.md)
* Mer information om hur du tilldelar roller i Azure AD finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
