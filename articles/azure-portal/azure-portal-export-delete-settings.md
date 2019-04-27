---
title: Exportera eller ta bort inställningar för Azure portal | Microsoft Docs
description: Lär dig hur du kan exportera eller ta bort dina användarinställningar, privata instrumentpaneler och anpassade inställningar i Azure-portalen.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551675"
---
# <a name="export-or-delete-user-settings"></a>Exportera eller ta bort användarinställningar

Du kan använda inställningar och funktioner i Azure-portalen för att skapa en anpassad upplevelse. Information om de anpassade inställningarna lagras i Azure. Du kan exportera eller ta bort följande användardata:

* Privata instrumentpaneler i Azure portal
* Användarinställningar som favorit prenumerationer eller kataloger och senaste inloggningskatalog
* Teman och andra anpassade portalinställningar

Det är en bra idé att exportera och granska dina inställningar innan du tar bort dem. Återskapa instrumentpaneler eller göra om anpassade inställningar kan ta lång tid.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportera eller ta bort dina portalinställningar

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I rubriken i portalen, väljer **inställningar**.

    ![Skärmbild som visar portal kuggjhulsikonen för inställlningar](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Välj **exportera alla inställningar** eller **ta bort alla inställningar och privata instrumentpaneler**.

    ![Skärmbild som visar portalen exportera och ta bort inställningar](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      I följande tabell beskrivs de här åtgärderna.

      | Åtgärd | Beskrivning |
      | --- | --- |
      | **Exportera alla inställningar** | Skapar en JSON-fil som innehåller dina användarinställningar färgtema, Favoriter och privata instrumentpaneler.|
      | **Ta bort alla inställningar och privata instrumentpaneler** | Tar bort alla länkar till privata instrumentpaneler och andra anpassade inställningar som du har gjort i portalen. |

> [!NOTE]
> Du kan inte importera inställningar från JSON-fil på grund av den dynamiska natur användarinställningar och risken för korrupta data.
>
>


## <a name="next-steps"></a>Nästa steg

* [Skapa och dela Azure-instrumentpaneler](azure-portal-dashboard-share-access.md)
* [Lägga till, ta bort och sortera Favoriter](azure-portal-add-remove-sort-favorites.md)
