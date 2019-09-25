---
title: Inaktivera e-postverifiering under kund registrering i Azure Active Directory B2C
description: Lär dig hur du inaktiverar e-postverifiering under kund registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7adc04991dd4a472bdaf1aa47aacaf6cdeb190
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256918"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kund registrering i Azure Active Directory B2C

Som standard verifierar Azure Active Directory B2C (Azure AD B2C) din kunds e-postadress för lokala konton (konton för användare som registrerar sig för e-postadress eller användar namn). Azure AD B2C garanterar giltiga e-postadresser genom att kräva att kunderna verifierar dem under registrerings processen. Det förhindrar också att skadliga aktörer använder automatiserade processer för att generera bedrägliga konton i dina program.

Vissa programutvecklare föredrar att hoppa över e-postverifiering under registrerings processen och får i stället kunder att verifiera sin e-postadress senare. Azure AD B2C kan konfigureras för att inaktivera e-postverifiering för att stödja detta. Detta skapar en smidigare registrerings process och ger utvecklare flexibiliteten att särskilja kunder som har verifierat sin e-postadress från kunder som inte har det.

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

> [!WARNING]
> Att inaktivera e-postverifiering i registrerings processen kan leda till skräp post. Om du inaktiverar standard Azure AD B2C e-postverifieringen från e-post, rekommenderar vi att du implementerar ett system för ersättnings verifiering.
