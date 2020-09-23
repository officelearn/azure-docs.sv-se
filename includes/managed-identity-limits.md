---
title: inkludera fil
description: inkludera fil
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971026"
---
- Varje hanterad identitet räknas mot objekt kvot gränsen i en Azure AD-klient enligt beskrivningen i [Azure AD-tjänstens gränser och begränsningar](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   Den hastighet med vilken hanterade identiteter kan skapas har följande gränser:

    1. Per Azure AD-klient per Azure-region: 200 skapa åtgärder per 20 sekunder.
    2. Per Azure-prenumeration per Azure-region: 40 skapa åtgärder per 20 sekunder.

- När du skapar användarspecifika hanterade identiteter stöds endast alfanumeriska tecken (0-9, a-z och A-Z) och bindestreck (-). För att tilldelningen till en virtuell dator eller skalnings uppsättning för virtuella datorer ska fungera korrekt är namnet begränsat till 24 tecken.
