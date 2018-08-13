---
title: Azure Active Directory Identity Protection-aviseringar | Microsoft Docs
description: Lär dig hur meddelanden Supportaktiviteter din undersökning.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0a546acd05246e011fa66abea8a667d0b3513588
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005906"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection-aviseringar

Azure AD Identity Protection skickar två typer av automatiskt meddelande om e-postmeddelanden för att hantera risker för användare och riskhändelser:

- Användare i farozonen har identifierats e-post
- Veckovis sammanfattad e-post

Den här artikeln ger en översikt över både e-postaviseringar.


## <a name="users-at-risk-detected-email"></a>Användare i farozonen har identifierats e-post

Som svar på ett konto som har identifierats i fara, Azure AD Identity Protection genererar en e-postavisering med **användare i farozonen har identifierats** som ämne. E-postmeddelandet innehåller en länk till den ** [användare som har flaggats för risk](../reports-monitoring/concept-user-at-risk.md) ** rapporten. Som bästa praxis bör du omedelbart undersöka användare i riskzonen.

![Användare i farozonen har identifierats e-post](./media/notifications/01.png)


### <a name="configuration"></a>Konfiguration

Som administratör kan ange du:

- **Den risknivå som utlöser generering av det här e-** -risknivån är som standard ”hög” risker.
- **Mottagare av e-postmeddelandet** -mottagare ingår som standard alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhetsadministratörer, Säkerhetsläsare som mottagare.  


Klicka för att öppna dialogrutan relaterade **aviseringar** i den **inställningar** delen av den **Identity Protection** sidan.

![Användare i farozonen har identifierats e-post](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>Veckovis sammanfattad e-post

Sammanfattad veckovis e-postmeddelandet innehåller en sammanfattning av nya riskhändelser.  
Det innehåller:

- Användare i farozonen

- Misstänkta aktiviteter

- Identifierade säkerhetsrisker

- Länkar till relaterade rapporter i Identity Protection

    ![Reparation](./media/notifications/400.png "reparation")

### <a name="configuration"></a>Konfiguration

Som administratör kan växla du skickar en veckovis e-post för sammandrag.

![Användaren risker](./media/notifications/62.png "användaren risker")

Klicka för att öppna dialogrutan relaterade **Veckosammandrag** i den **inställningar** delen av den **Identity Protection** sidan.

![Användare i farozonen har identifierats e-post](./media/notifications/04.png)


## <a name="see-also"></a>Se också

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
