---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182289"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Tagga en virtuell dator via mallar
Låt oss först titta på taggning genom mallar. [Den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) placerar taggar på följande resurser: Beräkning (virtuell dator), Lagring (lagringskonto) och Nätverk (offentlig IP-adress, virtuellt nätverk och nätverksgränssnitt). Den här mallen är för en Virtuell Windows-dator men kan anpassas för virtuella Linux-datorer.

Klicka på knappen **Distribuera till Azure** från [malllänken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Detta navigerar till [Azure-portalen](https://portal.azure.com/) där du kan distribuera den här mallen.

![Enkel distribution med taggar](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Den här mallen innehåller följande taggar: *Avdelning*, *Program*och *Skapad av*. Du kan lägga till/redigera dessa taggar direkt i mallen om du vill ha olika taggnamn.

![Azure-taggar i en mall](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Som du kan se definieras taggarna som nyckel-/värdepar, avgränsade med ett kolon (:). Taggarna måste definieras i det här formatet:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Spara mallfilen när du har redigerat den med valfria taggar.

I avsnittet **Redigera parametrar** kan du sedan fylla i värdena för taggarna.

![Redigera taggar i Azure-portalen](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klicka på **Skapa** om du vill distribuera den här mallen med taggvärdena.

## <a name="tagging-through-the-portal"></a>Tagga via portalen
När du har skapat dina resurser med taggar kan du visa, lägga till och ta bort taggar i portalen.

Välj taggikonen om du vill visa taggarna:

![Taggikon i Azure-portalen](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Lägg till en ny tagg via portalen genom att definiera ditt eget nyckel-/värde-par och spara den.

![Lägg till ny tagg i Azure-portalen](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Den nya taggen ska nu visas i listan med taggar för din resurs.

![Ny tagg sparad i Azure-portalen](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

