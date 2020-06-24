---
title: Visa information om autentisering för Azure Maps
description: Använd Azure Portal för att visa information om autentiseringen av Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988679"
---
Du kan visa information om Azure Maps-kontots autentisering i Azure Portal. I ditt konto går du till menyn **Inställningar** och väljer **autentisering**.

![Information om autentisering](../media/how-to-manage-authentication/how-to-view-auth.png)

När ett Azure Maps-konto har skapats finns Azure Maps- `x-ms-client-id` värdet på sidan Azure Portal information om autentisering. Det här värdet representerar kontot som ska användas för REST API begär Anden. Det här värdet ska lagras i program konfigurationen och hämtas innan du gör HTTP-förfrågningar när du använder Azure AD-autentisering med Azure Maps.
