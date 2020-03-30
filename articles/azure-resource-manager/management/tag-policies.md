---
title: Principer för taggning av resurser
description: Beskriver de Azure-principer som du kan tilldela för att säkerställa taggefterlevnad.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147023"
---
# <a name="assign-policies-for-tag-compliance"></a>Tilldela principer för taggefterlevnad

Du använder [Azure Policy](../../governance/policy/overview.md) för att genomdriva taggningsregler och konventioner. Genom att skapa en princip undviker du att det finns resurser som distribueras till prenumerationen och som inte har de förväntade taggarna för din organisation. I stället för att manuellt använda taggar eller söka efter resurser som inte är kompatibla skapar du en princip som automatiskt tillämpar de nödvändiga taggarna under distributionen. Taggar kan nu också tillämpas på befintliga resurser med den nya [ändra](../../governance/policy/concepts/effects.md#modify) effekten och en [reparationsaktivitet](../../governance/policy/how-to/remediate-resources.md). I följande avsnitt visas exempel på principer för taggar.

## <a name="policies"></a>Principer

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du taggar resurser finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md).
* Alla resurstyper stöder inte taggar. Information om du kan använda en tagg på en resurstyp finns i [Taggstöd för Azure-resurser](tag-support.md).
