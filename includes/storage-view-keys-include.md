---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e3b3d944508a4261b78def0b3bee13f7395a8bf0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748998"
---
Visa och kopiera åtkomst nycklar för lagrings kontot eller anslutnings strängen från Azure Portal:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Key** (Nyckel) **key1** och klicka på knappen **Kopiera** för att kopiera kontonyckeln.
5. Alternativt kan du kopiera hela anslutnings strängen. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**.

    ![Skärm bild som visar hur du visar åtkomst nycklar i Azure Portal](media/storage-view-keys-include/portal-connection-string.png)

Du kan använda antingen nyckeln för att komma åt Azure Storage, men i allmänhet är det en bra idé att använda den första nyckeln och reservera användningen av den andra nyckeln för när du roterar nycklar.
