---
title: Azure App konfiguration REST API-auktorisering
description: Referens sidor för auktorisering med Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424408"
---
# <a name="authorization"></a>Auktorisering

Auktorisering syftar på den procedur som används för att fastställa vilka behörigheter en anropare har när en begäran görs. Det finns flera auktoriserings modeller. Den auktoriserings modell som används för en begäran beror på vilken [autentiseringsmetod](./rest-api-authentication-index.md) som används. Auktoriserings modeller visas nedan.

## <a name="hmac"></a>HMAC

Modellen för [auktoriserings modell](./rest-api-authorization-hmac.md) som är ASSOCIERAD med HMAC-autentisering delar upp behörigheter i skrivskyddad eller Läs-och Skriv behörighet. Mer information finns på sidan för [HMAC-auktorisering](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Den [auktoriserings modell](./rest-api-authorization-azure-ad.md) som är kopplad till Azure Active Directory (Azure AD)-autentisering använder Azure RBAC för att kontrol lera behörigheter. Mer information finns på sidan [Azure AD-auktorisering](./rest-api-authorization-azure-ad.md) .
