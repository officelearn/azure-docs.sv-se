---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471572"
---
Om du vill att användare ska uppmanas att ange en andra autentiseringsfaktor innan du beviljar åtkomst kan du konfigurera Azure Multi-Factor Authentication (MFA). Du kan konfigurera MFA per användare eller använda MFA via [villkorlig åtkomst](../articles/active-directory/conditional-access/overview.md).

* MFA per användare kan aktiveras utan extra kostnad. När du aktiverar MFA per användare uppmanas användaren att ange andra faktorautentisering mot alla program som är kopplade till Azure AD-klienten. Se [alternativ 1](#peruser) för steg.
* Villkorlig åtkomst möjliggör finare kontroll över hur en andra faktor bör främjas. Det kan tillåta tilldelning av MFA till endast VPN och utesluta andra program som är kopplade till Azure AD-klienten. Se [Alternativ 2](#conditional) för steg.