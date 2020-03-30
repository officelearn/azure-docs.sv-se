---
title: Skapa en Azure API Management-instans | Microsoft Docs
description: Följ stegen i den här självstudien för att skapa en ny Azure API Management-instans.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: acfa10ea506e063bf6b230386ca0722f1c959d18
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75442565"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Skapa en ny Azure API Management-tjänstinstans

Azure API Management (APIM) hjälper organisationer att publicera API:er till externa partner och interna utvecklare så att de kan få ut maximalt av sina data och tjänster. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med APIM kan du skapa och hantera moderna API-gatewayer för befintliga serverdeltjänster som ligger var som helst. Mer information finns i ämnet [Översikt](api-management-key-concepts.md).

I den här snabbstarten beskrivs stegen för att skapa en ny API Management-instans med Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![API-hanteringsinstans](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-new-service"></a>Skapa en ny tjänst

1. På Portal-menyn i Azure väljer du **Skapa en resurs**. Du kan också välja **Skapa en resurs** på Startsidan **för** Azure. 
   
   ![Välj Skapa en resurs](./media/get-started-create-service-instance/00-CreateResource-01.png)
   
1. På skärmen **Nytt** väljer du **Integrering** och väljer sedan **API Management**.
   
   ![Ny Azure API Management-instans](./media/get-started-create-service-instance/00-CreateResource-02.png)
   
1. Ange inställningar på **api management-tjänstens** skärm.
   
   ![ny instans](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)
   
   | Inställning                 | Föreslaget värde                               | Beskrivning                                                                                                                                                                                                                                                                                                                         |
|-------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namn**                | Ett unikt namn för din API Management-tjänst | Namnet kan inte ändras senare. Tjänstens namn används för att generera en ett standarddomännamn med formatet of *{name}.azure-api.net.* Om du vill använda ett anpassat domännamn läser du [Konfigurera ett anpassat domännamn](configure-custom-domain.md). <br/> Tjänstens namn används för att referera till tjänsten och motsvarande Azure-resurs. |
| **Prenumeration**        | Din prenumeration                             | Den prenumeration som den här nya tjänstinstansen kommer att skapas för. Du kan välja prenumeration bland de olika Azure-prenumerationer som du har åtkomst till.                                                                                                                                                            |
| **Resursgrupp**      | *apimResourceGroup*                           | Du kan välja en ny eller befintlig resurs. En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Läs mer [här](../azure-resource-manager/management/overview.md#resource-groups).                                                                                                  |
| **Location**            | *USA, västra*                                    | Välj den geografiska regionen närmast dig. Endast de tillgängliga API Management-regionerna visas i listrutan.                                                                                                                                                                                                          |
| **Organisationens namn**   | Namnet på din organisation                 | Namnet används på ett antal platser, däribland titeln på utvecklarportalen och avsändaren av e-postmeddelanden.                                                                                                                                                                                                             |
| **E-postadress för administratör** | *admin\@org.com*                               | Ange den e-postadress som alla meddelanden från **API Management** ska skickas från.                                                                                                                                                                                                                                              |
| **Prisnivå**        | *Developer*                                   | Ställ in nivån **Developer** för att utvärdera tjänsten. Den här nivån ska inte användas för produktion. Mer information om att skala API Management-nivåerna finns i avsnittet [Uppgradera och skala](upgrade-and-scale.md).                                                                                                                                    |

3. Välj **Skapa**.

    > [!TIP]
    > Det tar vanligtvis mellan 20 och 30 minuter att skapa en API Management-tjänst. Om du väljer **Fäst vid instrumentpanelen** blir det enklare att hitta en nyligen skapad tjänst.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan följande steg användas för att ta bort resursgruppen och alla relaterade resurser:

1. Sök efter och välj **Resursgrupper**i Azure-portalen . Du kan också välja **Resursgrupper** på **startsidan.** 

   ![Navigering för resursgrupper](./media/get-started-create-service-instance/00-DeleteResource-01.png)

1. Välj resursgruppen på sidan **Resursgrupper.**

   ![Navigering för resursgrupper](./media/get-started-create-service-instance/00-DeleteResource-02.png)

1. På resursgruppssidan väljer du **Ta bort resursgrupp**. 
   
1. Skriv namnet på resursgruppen och välj sedan **Ta bort**.

   ![Ta bort resursgrupp](./media/get-started-create-service-instance/00-DeleteResource-03.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera och publicera ditt första API](import-and-publish.md)
