---
title: Exportera eller ta bort Azure-portalinställningar
description: Lär dig hur du kan exportera eller ta bort dina användarinställningar, privata instrumentpaneler och anpassade inställningar i Azure-portalen.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900746"
---
# <a name="export-or-delete-user-settings"></a>Exportera eller ta bort användarinställningar

Du kan använda inställningar och funktioner i Azure-portalen för att skapa en anpassad upplevelse. Information om dina anpassade inställningar lagras i Azure. Du kan exportera eller ta bort följande användardata:

* Privata instrumentpaneler i Azure-portalen
* Användarinställningar som favoritprenumerationer eller kataloger och senast inloggad katalog
* Teman och andra anpassade portalinställningar

Det är en bra idé att exportera och granska inställningarna innan du tar bort dem. Det kan vara tidskrävande att återskapa instrumentpaneler eller göra om anpassade inställningar.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportera eller ta bort portalinställningarna

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I portalens sidhuvud ![väljer](media/azure-portal-export-delete-settings/settings-icon.png) du inställningsikonen **Inställningar**.

1. Välj **Exportera alla inställningar** eller Ta bort alla inställningar och privata **instrumentpaneler**.

    ![Alternativ för inställningar och inställningar för Azure-portal](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      I följande tabell beskrivs dessa åtgärder.

      | Åtgärd | Beskrivning |
      | --- | --- |
      | **Exportera alla inställningar** | Skapar en *.json-fil* som innehåller dina användarinställningar som färgtema, favoriter och privata instrumentpaneler.|
      | **Ta bort alla inställningar och privata instrumentpaneler** | Tar bort alla länkar till privata instrumentpaneler och andra anpassade inställningar som du har gjort på portalen. |

> [!NOTE]
> På grund av den dynamiska karaktären hos användarinställningar och risken för att data skadas kan du inte importera inställningar från *.json-filen.*
>
>

## <a name="next-steps"></a>Nästa steg

* [Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll](azure-portal-dashboard-share-access.md)
* [Lägga till, ta bort och ordna om favoriter](azure-portal-add-remove-sort-favorites.md)
