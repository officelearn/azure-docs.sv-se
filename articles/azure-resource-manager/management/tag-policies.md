---
title: Principer för att tagga resurser
description: Beskriver de Azure-principer som du kan tilldela för att säkerställa taggens kompatibilitet.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147856"
---
# <a name="assign-policies-for-tag-compliance"></a>Tilldela principer för tagg-kompatibilitet

Du använder [Azure policy](../../governance/policy/overview.md) för att tillämpa taggnings regler och konventioner. Genom att skapa en princip undviker du syftet med resurser som distribueras till din prenumeration som inte har de förväntade taggarna för din organisation. I stället för att manuellt tillämpa taggar eller söka efter resurser som inte är kompatibla, skapar du en princip som automatiskt tillämpar de taggar som krävs under distributionen. Taggar kan nu också tillämpas på befintliga resurser med den nya [ändra](../../governance/policy/concepts/effects.md#modify) -effekt och en [reparations uppgift](../../governance/policy/how-to/remediate-resources.md). I följande avsnitt visas exempel principer för taggar.

## <a name="policies"></a>Principer

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om att tagga resurser finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md).
* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](tag-support.md).
