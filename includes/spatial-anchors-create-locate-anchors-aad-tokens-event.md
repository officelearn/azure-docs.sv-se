---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187045"
---
Precis som med åtkomsttoken, om en Azure AD-token inte har angetts, måste du hantera händelsen TokenRequired eller implementera tokenRequired-metoden i delegate-protokollet.

Du kan hantera händelsen synkront genom att ange egenskapen för händelse argumenten.
