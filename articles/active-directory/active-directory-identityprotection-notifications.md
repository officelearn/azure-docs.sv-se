---
title: Azure Active Directory Identity Protection-aviseringar | Microsoft Docs
description: "Lär dig hur meddelanden stöder undersökningen-aktiviteter."
services: active-directory
keywords: "Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: abc0f3926905295a9cf239146cce7fc57da7eb29
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection-aviseringar
Azure AD Identity Protection skickar två typer av automatisk e-postmeddelanden som hjälper dig att hantera användare risk och riskhändelser:

* Användaren komprometteras avisering e-post
* Varje vecka sammanfattad e-post

## <a name="user-compromised-alert-email"></a>Användaren komprometteras avisering e-post
En användare avslöjade e-postavisering genereras när Azure AD Identity Protection identifierar ett konto som komprometterats. E-postmeddelandet innehåller en länk till de användare som flaggats för risk rapporten i instrumentpanelen Identity Protection. Vi rekommenderar att du genast undersöka meddelanden om avslöjade konton.

## <a name="weekly-digest-email"></a>Varje vecka sammanfattad e-post
Varje vecka sammanfattad e-postmeddelandet innehåller en sammanfattning av nya riskhändelser.<br>
Det innehåller:

* Användare i farozonen
* Misstänkta aktiviteter
* Identifierade säkerhetsrisker
* Länkar till relaterade rapporter i Identity Protection

<br>
![Reparation](./media/active-directory-identityprotection-notifications/400.png "reparation")
<br>

Du kan växla skicka en vecka digest-e-post.
<br><br>
![Användaren risker](./media/active-directory-identityprotection-notifications/62.png "användaren risker")
<br>

**Att öppna dialogrutan relaterade configuration**:

1. På den **Azure AD Identity Protection** bladet, klickar du på **inställningar**.
   <br><br>
   ![Användaren riskprincipen](./media/active-directory-identityprotection-notifications/401.png "risk användarprincip")
   <br>
2. I den **allmänna** klickar du på **meddelanden**.
   <br><br>
   ![Användaren riskprincipen](./media/active-directory-identityprotection-notifications/405.png "risk användarprincip")
   <br>

## <a name="see-also"></a>Se även
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
