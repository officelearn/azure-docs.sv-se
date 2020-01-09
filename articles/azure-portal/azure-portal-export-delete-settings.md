---
title: Exportera eller ta bort Azure Portal inställningar | Microsoft Docs
description: Lär dig hur du kan exportera eller ta bort användar inställningar, privata instrument paneler och anpassade inställningar i Azure Portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640184"
---
# <a name="export-or-delete-user-settings"></a>Exportera eller ta bort användarinställningar

Du kan använda inställningar och funktioner i Azure Portal för att skapa en anpassad upplevelse. Information om dina anpassade inställningar lagras i Azure. Du kan exportera eller ta bort följande användar data:

* Privata instrument paneler i Azure Portal
* Användar inställningar som favorit prenumerationer eller kataloger och senast inloggade katalog
* Teman och andra anpassade Portal inställningar

Det är en bra idé att exportera och granska dina inställningar innan du tar bort dem. Det kan vara tids krävande att skapa instrument paneler eller göra om anpassade inställningar.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportera eller ta bort dina Portal inställningar

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Inställningar**i portalens rubrik.

    ![Skärm bild som visar maskin varan för Portal inställningar](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Välj **Exportera alla inställningar** eller **ta bort alla inställningar och privata instrument paneler**.

    ![Skärm bild som visar portalen exportera och ta bort inställningar](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      I följande tabell beskrivs dessa åtgärder.

      | Åtgärd | Beskrivning |
      | --- | --- |
      | **Exportera alla inställningar** | Skapar en. JSON-fil som innehåller dina användar inställningar, t. ex. ditt färg tema, favoriter och privata instrument paneler.|
      | **Ta bort alla inställningar och privata instrument paneler** | Tar bort alla länkar till privata instrument paneler och andra anpassade inställningar som du har gjort i portalen. |

> [!NOTE]
> På grund av den dynamiska typen av användar inställningar och risk för skadade data kan du inte importera inställningar från. JSON-filen.
>
>


## <a name="next-steps"></a>Nästa steg

* [Skapa och dela Azure-instrumentpaneler](azure-portal-dashboard-share-access.md)
* [Lägg till, ta bort och sortera favoriter](azure-portal-add-remove-sort-favorites.md)
