---
title: Inaktivera e-postverifiering under konsument registrering i Azure Active Directory B2C | Microsoft Docs
description: Ett ämne som demonstrerar hur du inaktiverar e-postverifiering under konsument registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065625"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under konsument registrering i Azure Active Directory B2C
När Azure Active Directory B2C (Azure AD B2C) ger en konsument möjlighet att registrera sig för program genom att ange en e-postadress och skapa ett lokalt konto. Azure AD B2C garanterar giltiga e-postadresser genom att kräva att användarna verifierar dem under registrerings processen. Det förhindrar också en skadlig automatiserad process från att generera falska konton för programmen.

Vissa programutvecklare föredrar att hoppa över e-postverifiering under registrerings processen och måste i stället låta konsumenterna verifiera e-postadressen senare. Azure AD B2C kan konfigureras för att inaktivera e-postverifiering för att stödja detta. Om du gör det skapas en smidigare registrerings process som gör det möjligt för utvecklare att särskilja de konsumenter som har verifierat sin e-postadress från de konsumenter som inte har det.

Som standard har inloggnings användar flöden aktiverat e-postverifiering. Använd följande steg för att inaktivera det:

1. Klicka på **användar flöden**.
2. Klicka på ditt användar flöde (till exempel "B2C_1_SiUp") för att öppna det.
3. Klicka på **sidlayouter**.
4. Klicka på **registrerings sidan för lokalt konto**.
5. Klicka på **e-postadress** i kolumnen **namn** under avsnittet **användarattribut** .
6. Under **kontrollen krävs**väljer du **Nej**.
7. Klicka på **Spara** överst på bladet. Du är klar!

> [!NOTE]
> Att inaktivera e-postverifiering i registrerings processen kan leda till skräp post. Om du inaktiverar standard alternativet rekommenderar vi att du lägger till ett eget verifierings system.
>
>
