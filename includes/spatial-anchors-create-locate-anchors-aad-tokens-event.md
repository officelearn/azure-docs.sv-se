---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67187045"
---
Precis som med åtkomsttoken, om en Azure AD-token inte har angetts, måste du hantera tokenRequired-händelsen eller implementera metoden tokenRequired på delegatprotokollet.

Du kan hantera händelsen synkront genom att ange egenskapen på händelseargumenten.
