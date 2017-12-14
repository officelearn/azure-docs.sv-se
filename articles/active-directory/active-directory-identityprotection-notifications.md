---
title: Azure Active Directory Identity Protection-aviseringar | Microsoft Docs
description: "Lär dig hur meddelanden stöder undersökningen-aktiviteter."
services: active-directory
keywords: "Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection-aviseringar

Azure AD Identity Protection skickar två typer av automatisk e-postmeddelanden som hjälper dig att hantera användare risk och riskhändelser:

- Användare i riskzonen upptäckte e-post
- Varje vecka sammanfattad e-post

Den här artikeln ger en översikt över både e-postmeddelanden.


## <a name="users-at-risk-detected-email"></a>Användare i riskzonen upptäckte e-post

Som svar på ett konto som har upptäckts i fara, Azure AD Identity Protection genererar en e-postavisering med **användare i riskzonen identifierat** som ämne. E-postmeddelandet innehåller en länk till den  **[användare som har flaggats för risk](active-directory-reporting-security-user-at-risk.md)**  rapporten. Som bästa praxis bör du genast undersöka användare i riskzonen.

![Användare i riskzonen upptäckte e-post](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Konfiguration

Som administratör kan ange du:

- **Risknivå som utlöser genereringen av e-postmeddelandet** -risknivån är som standard till ”hög” risk.
- **Mottagare av e-postmeddelandet** -mottagare ingår som standard alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhetsadministratörerna, säkerhet läsare som mottagare.  


Klicka för att öppna dialogrutan relaterade **aviseringar** i den **inställningar** avsnitt i den **identitetsskydd** sidan.

![Användare i riskzonen upptäckte e-post](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>Varje vecka sammanfattad e-post

Varje vecka sammanfattad e-postmeddelandet innehåller en sammanfattning av nya riskhändelser.  
Det innehåller:

- Användare i farozonen

- Misstänkta aktiviteter

- Identifierade säkerhetsrisker

- Länkar till relaterade rapporter i Identity Protection

    ![Reparation](./media/active-directory-identityprotection-notifications/400.png "reparation")

### <a name="configuration"></a>Konfiguration

Som administratör kan växla du skicka en vecka digest-e-post.

![Användaren risker](./media/active-directory-identityprotection-notifications/62.png "användaren risker")

Klicka för att öppna dialogrutan relaterade **veckovisa sammanfattad** i den **inställningar** avsnitt i den **identitetsskydd** sidan.

![Användare i riskzonen upptäckte e-post](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>Se även

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
