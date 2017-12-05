---
title: Redigera ett API med Azure-portalen | Microsoft Docs
description: "Den här kursen visar hur du använder API Management (APIM) för att redigera en API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 362c36181da706e3fe0a27cc5ab262271c2a1e57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="edit-an-api"></a>Redigera en API

Stegen i den här kursen visar hur du använder API Management (APIM) för att redigera en API. 

+ Du kan göra det genom att lägga till, ta bort, byta namn på åtgärder i APIM-instans. 
+ Du kan redigera din API swagger.

## <a name="prerequisites"></a>Krav

+ [Skapa en instans av Azure API Management](get-started-create-service-instance.md)
+ [Importera och publicera din första API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Redigera en API i APIM

![Redigera en api](./media/edit-api/edit-api001.png)

1. Klicka på den **API: er** fliken.
2. Välj en av de API: er som du tidigare har importerats.
3. Välj den **Design** fliken.
4. Välj en åtgärd som du vill redigera.
5. Byt namn på åtgärden genom att markera en **penna** i den **klientdel** fönster.

## <a name="update-the-swagger"></a>Uppdatera swagger

Du kan uppdatera din backbend API från Azure portal genom att följa dessa steg:

1. Välj **alla åtgärder**
2. Klicka på penna i den **klientdel** fönster.

    ![Redigera en api](./media/edit-api/edit-api002.png)

    Din API swagger visas.

    ![Redigera en api](./media/edit-api/edit-api003.png)

3. Uppdatera swagger.
4. Tryck på **spara**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [APIM princip prover](policy-samples.md)
> [transformera och skydda publicerade API](transform-api.md)