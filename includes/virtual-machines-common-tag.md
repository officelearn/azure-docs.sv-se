---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182289"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Tagga en virtuell dator med hjälp av mallar
Först ska vi titta på taggning genom mallar. [Den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) placerar taggar på följande resurser: Compute (virtuell dator), lagring (lagrings konto) och nätverk (offentlig IP-adress, Virtual Network och nätverks gränssnitt). Den här mallen gäller för en virtuell Windows-dator men kan anpassas för virtuella Linux-datorer.

Klicka på knappen **distribuera till Azure** från [länken mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). På så sätt navigerar du till [Azure Portal](https://portal.azure.com/) där du kan distribuera den här mallen.

![Enkel distribution med Taggar](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Den här mallen innehåller följande Taggar: *avdelning*, *program*och *skapade av*. Du kan lägga till/redigera taggarna direkt i mallen om du vill ha olika taggnamn.

![Azure-Taggar i en mall](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Som du kan se definieras taggarna som nyckel/värde-par, avgränsade med kolon (:). Taggarna måste definieras i följande format:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Spara mallfilen när du är klar med att redigera den med de taggar du väljer.

Sedan kan du fylla i värdena för taggarna i avsnittet **Redigera parametrar** .

![Redigera taggar i Azure Portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klicka på **skapa** för att distribuera den här mallen med dina märkes värden.

## <a name="tagging-through-the-portal"></a>Tagga via portalen
När du har skapat dina resurser med taggar kan du Visa, lägga till och ta bort taggar i portalen.

Välj ikonen taggar för att visa dina Taggar:

![Ikonen Taggar i Azure Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Lägg till en ny tagg via portalen genom att definiera ditt eget nyckel/värde-par och spara det.

![Lägg till en ny tagg i Azure Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Den nya taggen bör nu visas i listan över taggar för din resurs.

![En ny tagg sparades i Azure Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

