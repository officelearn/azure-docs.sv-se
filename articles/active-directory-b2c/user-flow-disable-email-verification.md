---
title: Inaktivera e-postverifiering under kund registrering
titleSuffix: Azure AD B2C
description: Lär dig hur du inaktiverar e-postverifiering under kund registrering i Azure Active Directory B2C.
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126743"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kund registrering i Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Följ de här stegen för att inaktivera e-postverifiering:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd filtret för **katalog + prenumeration** på den översta menyn för att välja den katalog som innehåller Azure AD B2C klient.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj det användar flöde som du vill inaktivera e-postverifiering för. Till exempel *B2C_1_signinsignup*.
1. Välj **sidlayouter**.
1. Välj **inloggnings sida för lokalt konto**.
1. Under **användarattribut**väljer du **e-postadress**.
1. I list rutan **kräver verifiering** väljer du **Nej**.
1. Välj **Spara**. E-postverifiering är nu inaktiverat för det här användar flödet.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [anpassar användar gränssnittet i Azure Active Directory B2C](customize-ui-overview.md)

