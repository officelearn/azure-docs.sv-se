---
title: Exportera eller ta bort Azure Portal inställningar
description: Lär dig hur du kan exportera eller ta bort användar inställningar, privata instrument paneler och anpassade inställningar i Azure Portal.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900746"
---
# <a name="export-or-delete-user-settings"></a>Exportera eller ta bort användarinställningar

Du kan använda inställningar och funktioner i Azure Portal för att skapa en anpassad upplevelse. Information om dina anpassade inställningar lagras i Azure. Du kan exportera eller ta bort följande användar data:

* Privata instrument paneler i Azure Portal
* Användar inställningar som favorit prenumerationer eller kataloger och senast inloggade katalog
* Teman och andra anpassade Portal inställningar

Det är en bra idé att exportera och granska dina inställningar innan du tar bort dem. Det kan vara tids krävande att skapa instrument paneler eller göra om anpassade inställningar.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportera eller ta bort dina Portal inställningar

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I rubriken för portalen väljer du ![Inställningar ikon](media/azure-portal-export-delete-settings/settings-icon.png) **Inställningar**.

1. Välj **Exportera alla inställningar** eller **ta bort alla inställningar och privata instrument paneler**.

    ![Azure Portal inställningar och inställnings alternativ](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      I följande tabell beskrivs dessa åtgärder.

      | Åtgärd | Beskrivning |
      | --- | --- |
      | **Exportera alla inställningar** | Skapar en *. JSON* -fil som innehåller dina användar inställningar, t. ex. ditt färg tema, favoriter och privata instrument paneler.|
      | **Ta bort alla inställningar och privata instrument paneler** | Tar bort alla länkar till privata instrument paneler och andra anpassade inställningar som du har gjort i portalen. |

> [!NOTE]
> På grund av den dynamiska typen av användar inställningar och risk för skadade data kan du inte importera inställningar från *. JSON* -filen.
>
>

## <a name="next-steps"></a>Nästa steg

* [Dela Azure-instrumentpaneler med hjälp av rollbaserad Access Control](azure-portal-dashboard-share-access.md)
* [Lägg till, ta bort och ordna om favoriter](azure-portal-add-remove-sort-favorites.md)
