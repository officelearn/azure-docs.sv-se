---
title: Oväntat medgivande när du loggar in till ett program | Microsoft Docs
description: Så här felsöker när en användare ser Kommandotolken medgivande för ett program som du har integrerat med Azure AD som du inte väntade dig
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: a85c10e01b714bba6bab9fdf815803f36195fdde
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184010"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Oväntat medgivande när du loggar in till ett program

Många program som integreras med Azure Active Directory kräver behörighet till olika resurser för att köra. När dessa resurser är också integrerat med Azure Active Directory, behörighet att komma åt dem har begärts med hjälp av Azure AD-ramverket för medgivande. 

Detta resulterar i en medgivande fråga som visas första gången ett program används, vilket ofta är en engångsåtgärd. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenarier där användarna ser godkänna anvisningarna

Ytterligare anvisningar kan förväntas i olika scenarier:

* Uppsättningen behörigheter som krävs av programmet har ändrats.

* Den användare som ursprungligen har samtyckt till programmet var inte en administratör och nu en annan (icke-administratörer)-användare använder programmet för första gången.

* Den användare som ursprungligen har samtyckt till programmet har en administratör, men de gav inte sitt godkännande på uppdrag av hela organisationen.

* Programmet använder [inkrementell och dynamiska medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) och begär ytterligare behörighet efter ett godkännande från början. Det här används ofta när valfria funktioner i ett program som är ytterligare kräver behörigheter utöver de som krävs för grundläggande funktioner.

* Medgivande återkallades efter beviljas från början.

* Utvecklaren har konfigurerat program för att kräva en medgivandetext för varje gång den används (Obs!: det här är inte bästa praxis).

## <a name="next-steps"></a>Nästa steg

-   [Appar, behörigheter och godkännande i Azure Active Directory (v1.0 slutpunkt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Omfattningar, behörigheter och godkännande i Azure Active Directory (v2.0-slutpunkt)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


