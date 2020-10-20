---
author: baanders
description: inkludera fil för steg översikt i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205513"
---
>[!NOTE]
>Dessa åtgärder är avsedda att slutföras av en användare med möjlighet att hantera både resurser och användar åtkomst i Azure-prenumerationen. Även om vissa steg kan slutföras med lägre behörigheter, krävs samarbetet för någon med dessa behörigheter för att helt kunna konfigurera en användbar instans. Visa mer information om detta i avsnittet krav [*: nödvändiga behörigheter*](#prerequisites-permission-requirements) nedan.

En fullständig installation av en ny Azure Digitals dubbla instanser består av två delar:
1. **Skapar instansen**
2. Konfigurera **användar åtkomst behörigheter**: Azure-användare måste ha rollen *Azure Digitals-ägare (för hands version)* på Azures digitala dubbla instansen för att kunna hantera den och dess data. I det här steget ska du som ägare/administratör av Azure-prenumerationen tilldela rollen till den person som ska hantera din Azure Digital-instansen. Detta kan vara själv eller någon annan i din organisation.