---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131640"
---
| Mekanism | Omfång |Gränser | Behörighets nivå som stöds |
|---|---|---|---|
| RBAC | Lagrings konton, behållare. <br>Kors resursens RBAC-roll tilldelningar på prenumerations-eller resurs grupps nivå. | 2000 RBAC-roll tilldelningar i en prenumeration | RBAC-roller (inbyggda eller anpassade) |
| ACL| Katalog, fil |32 ACL-poster (effektivt 28 ACL-poster) per fil och per katalog. Åtkomst-och standard-ACL: er har sin egen begränsning för 32 ACL-poster. |ACL-behörighet|
