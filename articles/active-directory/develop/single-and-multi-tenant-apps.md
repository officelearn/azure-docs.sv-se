---
title: Enstaka och flera innehavare appar i Azure Active Directory
description: Läs mer om funktioner och skillnader mellan en enda klient och flera innehavare appar i Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ec1939dd1f5445583200776e489f7596f572f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250313"
---
# <a name="tenancy-in-azure-active-directory"></a>Innehavare i Azure Active Directory

Azure Active Directory (Azure AD) organiserar objekt som användare och appar i grupper kallas *klienter*. Klienter kan en administratör att ange principer för användare i organisationen samt de appar som organisationen äger för att uppfylla säkerhets- och operativa riktlinjer. 

## <a name="who-can-sign-in-to-your-app"></a>Vem som kan logga in till din app?

När det gäller att utveckla appar utvecklare kan välja att konfigurera appen för att vara enda klient eller flera innehavare under registreringen i den [Azure-portalen](https://portal.azure.com).
* Appar för en enskild klient finns bara i klienten har registrerats i, även kallat sina startklientorganisation.
* Appar för flera appar är tillgängliga för användare i både sina startklientorganisation och andra klienter.

Du kan konfigurera din app för att vara enda klient eller flera innehavare genom att ange målgruppen på följande sätt i Azure-portalen.

| Målgrupp | En/flera-tenant | Vem som kan logga in | 
|----------|--------| ---------|
| Konton i den här katalogen | Enskild klientorganisation | Alla användare och gästkonton i katalogen kan använda ditt program eller API.<br>*Använd det här alternativet om målgruppen är interna för din organisation.* |
| Konton i Azure valfri AD-katalog | Flera innehavare | Alla användare och gäster med ett arbets- eller skolkonto konto från Microsoft kan använda ditt program eller din API. Detta inkluderar skolor och företag som använder Office 365.<br>*Använd det här alternativet om målgruppen är företag eller högskolor kunder.* |
| Konton i alla Azure AD-katalog och personliga Microsoft-konton (till exempel Skype, Xbox, Outlook.com) | Flera innehavare | Alla användare med ett arbets- eller Skol- eller personligt Microsoft-konto kan använda ditt program eller din API. Det omfattar skolor och företag som använder Office 365 samt personliga konton som används för att logga in på tjänster som Xbox och Skype.<br>*Använd det här alternativet för att rikta den bredaste uppsättningen Microsoft-konton.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Metodtips för appar för flera klienter

Att skapa bra appar för flera innehavare kan vara svårt eftersom antalet olika principer som IT-administratörer kan ange i sina klienter. Om du väljer att skapa en app för flera klienter, följa dessa rekommendationer:

* Testa din app i en klient som konfigurerats [principer för villkorlig åtkomst](conditional-access-dev-guide.md).
* Följ principen om lägsta användaråtkomst så att appen endast begär behörigheter som behövs. Undvik begär behörigheter som kräver administratörens godkännande som det kan hindra användare från att förvärva din app alls i vissa organisationer. 
* Ange rätt namn och beskrivningar för alla behörigheter som du exponera som en del av din app. Detta hjälper användare och administratörer vet vad de accepterar när de försöker använda API: er för din app. Mer information finns i avsnittet om bästa praxis i den [guide behörigheter](v1-permissions-and-consent.md).

## <a name="next-steps"></a>Nästa steg

* [Så här konverterar du en app för att vara flera innehavare](howto-convert-app-to-be-multi-tenant.md)
