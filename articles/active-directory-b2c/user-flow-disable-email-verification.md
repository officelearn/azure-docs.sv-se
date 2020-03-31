---
title: Inaktivera e-postverifiering under kundanmälning
titleSuffix: Azure AD B2C
description: Lär dig hur du inaktiverar e-postverifiering under kundanmälning i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126743"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kundanmälning i Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Så här inaktiverar du e-postverifiering:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd **katalog +** prenumerationsfiltret på den övre menyn för att välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Användarflöden**.
1. Välj det användarflöde som du vill inaktivera e-postverifiering för. Till exempel *B2C_1_signinsignup*.
1. Välj **Sidlayouter**.
1. Välj **registreringssida för lokalt konto**.
1. Under **Användarattribut**väljer du **E-postadress**.
1. I listrutan **KRÄVER VERIFIERING** väljer du **Nej**.
1. Välj **Spara**. E-postverifiering är nu inaktiverad för det här användarflödet.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [anpassar användargränssnittet i Azure Active Directory B2C](customize-ui-overview.md)

