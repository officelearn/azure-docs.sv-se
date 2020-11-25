---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017700"
---
| Mekanism | Omfång |Gränser | Behörighets nivå som stöds |
|---|---|---|---|
| Azure RBAC | Lagrings konton, behållare. <br>Kors resursens Azure Role-tilldelningar på prenumerations-eller resurs grupps nivå. | 2000 Azure-roll tilldelningar i en prenumeration | Azure-roller (inbyggt eller anpassat) |
| ACL| Katalog, fil |32 ACL-poster (effektivt 28 ACL-poster) per fil och per katalog. Åtkomst-och standard-ACL: er har sin egen begränsning för 32 ACL-poster. |ACL-behörighet|
