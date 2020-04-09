---
title: Appar med en och flera innehavare i Azure AD
titleSuffix: Microsoft identity platform
description: Lär dig mer om funktioner och skillnader mellan appar för en klient och flera innehavare i Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: ec59383f9ca2b71ec9f4b6df3ab2e24c6b52473b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881374"
---
# <a name="tenancy-in-azure-active-directory"></a>Hyresrätt i Azure Active Directory

Azure Active Directory (Azure AD) organiserar objekt som användare och appar i grupper som kallas *klienter*. Klienter tillåter en administratör att ange principer för användarna inom organisationen och de appar som organisationen äger för att uppfylla sina säkerhets- och driftsprinciper. 

## <a name="who-can-sign-in-to-your-app"></a>Vem kan logga in på din app?

När det gäller att utveckla appar kan utvecklare välja att konfigurera sin app så att den är antingen en enda klient eller flera innehavare vid appregistrering i [Azure-portalen](https://portal.azure.com).
* Appar med en klient är endast tillgängliga i klienten som de har registrerats i, även kallade deras hemklient.
* Appar med flera innehavare är tillgängliga för användare i både deras hemklient och andra klienter.

I Azure-portalen kan du konfigurera appen så att den är en enda klient eller flera innehavare genom att ange målgruppen enligt följande.

| Målgrupp | En-/flera innehavare | Vem kan logga in | 
|----------|--------| ---------|
| Konton i den här katalogen | Enskild klientorganisation | Alla användar- och gästkonton i katalogen kan använda ditt program eller API.<br>*Använd det här alternativet om målgruppen är intern i organisationen.* |
| Konton i valfri Azure AD-katalog | Flera innehavare | Alla användare och gäster med ett arbets- eller skolkonto från Microsoft kan använda ditt program eller ditt API. Detta inkluderar skolor och företag som använder Office 365.<br>*Använd det här alternativet om din målgrupp är företags- eller utbildningskunder.* |
| Konton i en Azure AD-katalog och personliga Microsoft-konton (till exempel Skype, Xbox, Outlook.com) | Flera innehavare | Alla användare med ett arbete eller en skola eller ett personligt Microsoft-konto kan använda ditt program eller ditt API. Det omfattar skolor och företag som använder Office 365 samt personliga konton som används för att logga in på tjänster som Xbox och Skype.<br>*Använd det här alternativet om du vill rikta in dig på den bredaste uppsättningen Microsoft-konton.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Metodtips för appar med flera innehavare

Att skapa bra appar för flera innehavare kan vara en utmaning på grund av antalet olika principer som IT-administratörer kan ange i sina klienter. Om du väljer att skapa en app med flera innehavare följer du dessa metodtips:

* Testa din app i en klient som har konfigurerat [principer för villkorlig åtkomst](../azuread-dev/conditional-access-dev-guide.md).
* Följ principen om minsta användaråtkomst för att säkerställa att din app bara begär behörigheter som den faktiskt behöver. Undvik att begära behörigheter som kräver administratörsmedgivande eftersom detta kan hindra användare från att skaffa din app alls i vissa organisationer. 
* Ange lämpliga namn och beskrivningar för alla behörigheter som du exponerar som en del av appen. Detta hjälper användare och administratörer att veta vad de godkänner när de försöker använda appens API:er. Mer information finns i avsnittet metodtips i [behörighetsguiden](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Nästa steg

* [Konvertera en app till flera innehavare](howto-convert-app-to-be-multi-tenant.md)
