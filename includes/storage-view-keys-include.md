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
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208200"
---
Så här visar och kopierar du åtkomstnycklar för lagringskontot eller anslutningssträngen från Azure-portalen:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Under **Inställningar**väljer du **Åtkomstnycklar**. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Key** (Nyckel) **key1** och klicka på knappen **Kopiera** för att kopiera kontonyckeln.
5. Alternativt kan du kopiera hela anslutningssträngen. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**.

    ![Skärmbild som visar hur du visar åtkomstnycklar i Azure-portalen](media/storage-view-keys-include/portal-connection-string.png)

Du kan använda båda nycklarna för att komma åt Azure Storage, men i allmänhet är det en bra idé att använda den första nyckeln och reservera användningen av den andra nyckeln för när du roterar nycklar.
