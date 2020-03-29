---
title: Oväntad begäran om godkännande när du loggar in på ett program | Microsoft-dokument
description: Felsöka när en användare ser en medgivandefråga för ett program som du har integrerat med Azure AD som du inte förväntade dig
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83d043ecef152f977437e21e2caec40d1c40ce0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781157"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Oväntad begäran om godkännande när du loggar in på ett program

Många program som integreras med Azure Active Directory kräver behörigheter till olika resurser för att kunna köras. När dessa resurser också är integrerade med Azure Active Directory, begärs behörigheter för att komma åt dem med hjälp av Azure AD-medgivanderamverket. 

Detta resulterar i att en samtyckesfråga visas första gången ett program används, vilket ofta är en engångsåtgärd. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenarier där användare ser uppmaningar om medgivande

Ytterligare uppmaningar kan förväntas i olika scenarier:

* Den uppsättning behörigheter som krävs av programmet har ändrats.

* Användaren som ursprungligen samtyckte till programmet var inte administratör, och nu använder en annan (icke-administratör) användare programmet för första gången.

* Användaren som ursprungligen samtyckte till programmet var administratör, men de samtyckte inte för hela organisationens räkning.

* Programmet använder [inkrementellt och dynamiskt samtycke](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) för att begära ytterligare behörigheter efter att medgivandet ursprungligen beviljades. Detta används ofta när valfria funktioner i ett program ytterligare kräver behörigheter utöver de som krävs för baslinjefunktioner.

* Samtycke återkallades efter att ha beviljats ursprungligen.

* Utvecklaren har konfigurerat programmet så att det kräver en medgivandefråga varje gång det används (observera: detta är inte bästa praxis).

## <a name="next-steps"></a>Nästa steg

-   [Appar, behörigheter och medgivande i Azure Active Directory (v1.0-slutpunkt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Scope, behörigheter och medgivande i Azure Active Directory (v2.0-slutpunkten)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


