---
title: "Oväntat medgivandetext när du loggar in till ett program | Microsoft Docs"
description: "Felsökning av när en användare ser en fråga om medgivande för ett program som du har integrerat med Azure AD som du inte förväntar dig"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e5b823e1251a7221f73efe6838d439f827f9665d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Oväntat medgivandetext när du loggar in till ett program

Många program som integreras med Azure Active Directory kräver behörigheter för olika resurser för att kunna köra. När dessa resurser också är integrerade med Azure Active Directory, behörighet att komma åt dem begärs med hjälp av Azure AD medgivande framework. 

Detta resulterar i en medgivande fråga som visas första gången ett program används, vilket ofta är en gång. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenarier som användarna ser medgivande prompter

Fler meddelanden kan förväntas i olika scenarier:

* En uppsättning behörigheter som krävs för programmet har ändrats.

* Den användare som ursprungligen har samtyckt till programmet var inte en administratör och nu en annan (icke-administratörer) användare använder programmet för första gången.

* Den användare som ursprungligen har samtyckt till att programmet har en administratör, men de samtycker inte på uppdrag av hela organisationen.

* Programmet använder [inkrementell och dynamiska medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) och begär ytterligare behörighet efter medgivande beviljats från början. Det här används ofta när valfria funktioner i ett program ytterligare kräver behörigheter utöver de som krävs för grundläggande funktioner.

* Medgivande återkallades efter beviljas från början.

* Utvecklaren har konfigurerat program kräver en fråga om medgivande varje gång den används (Obs: Detta är inte bästa praxis).

## <a name="next-steps"></a>Nästa steg

-   [Appar, behörigheter och medgivande i Azure Active Directory (v1.0 slutpunkten)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Scope, behörigheter och medgivande i Azure Active Directory (v2.0-slutpunkten)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


