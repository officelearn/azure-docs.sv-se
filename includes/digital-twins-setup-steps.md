---
author: baanders
description: inkludera fil för steg översikt i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407596"
---
>[!NOTE]
>Dessa åtgärder är avsedda att slutföras av en användare med en *ägar* roll i Azure-prenumerationen. Även om vissa delar kan slutföras utan denna utökade behörighet, krävs en ägares samarbete för att helt konfigurera en användbar instans. Visa mer information om detta i avsnittet krav [*: nödvändiga behörigheter*](#prerequisites-permission-requirements) nedan.

En fullständig installation av en ny Azure Digitals dubblare-instans består av tre delar:
1. **Skapar instansen**
2. Konfigurera **användar åtkomst behörigheter**: Azure-användare måste ha rollen *Azure Digitals-ägare (för hands version)* på Azures digitala dubbla instansen för att kunna hantera den och dess data. I det här steget ska du som ägare i Azure-prenumerationen tilldela den här rollen till den person som ska hantera din Azure Digital-instansen. Detta kan vara själv eller någon annan i din organisation.
3. Konfigurera **åtkomst behörigheter för klient program**: det är vanligt att skriva ett klient program som ska användas för att interagera med din instans. För att klient programmet ska kunna komma åt Azures digitala dubbla, måste du konfigurera en *app-registrering* i [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) att klient programmet ska använda för att autentisera till instansen.

Du behöver en Azure-prenumeration för att kunna fortsätta. Du kan ställa in en kostnads fri [här](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
