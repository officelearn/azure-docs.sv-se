---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187045"
---
Precis som med åtkomsttoken, om en Azure AD-token inte har angetts, måste du hantera händelsen TokenRequired eller implementera tokenRequired-metoden i delegate-protokollet.

Du kan hantera händelsen synkront genom att ange egenskapen för händelse argumenten.
