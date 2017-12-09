---
title: "Distribuera en webbtjänst till flera regioner | Microsoft Docs"
description: "Steg för att distribuera (kopiera) en ny webbtjänst till andra regioner."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 8e616bd6f13c4f97886a3eae225e188afadb2305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Så här distribuerar du en webbtjänst till flera regioner
Den nya Azure Web Services kan du enkelt distribuera en webbtjänst till flera regioner utan att behöva flera prenumerationer eller arbetsytor. 

Priser är regionspecifika, därför måste du definiera ett faktureringsavtal för varje region där du ska distribuera webbtjänsten.

## <a name="to-create-a-plan-in-another-region"></a>Att skapa en plan i en annan region
1. Logga in på [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/).
2. Klicka på den **planer** menyalternativet.
3. Klicka på planer över sidan Visa **ny**.
4. Från den **prenumeration** listrutan väljer du den prenumeration som den nya planen kommer att finnas.
5. Från den **Region** listrutan, Välj en region för den nya planen. Planera inställningar för den valda regionen visas i den **planera alternativ** avsnitt på sidan.
6. Från den **resursgruppen** listrutan, Välj en resursgrupp för planen. För mer information om resursgrupper finns [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. I **schemanamn** skriver du namnet på planen.
8. Under **alternativ**, klicka på nivån fakturering för den nya planen.
9. Klicka på **Skapa**.

## <a name="deploying-the-web-service-to-another-region"></a>Distribuera webbtjänsten till en annan region
1. Klicka på den **Web Services** menyalternativet.
2. Välj den webbtjänst som du distribuerar till en ny region.
3. Klicka på **kopiera**.
4. I **Webbtjänstnamn**, ange ett nytt namn för webbtjänsten.
5. I **Web tjänstbeskrivningen**, ange en beskrivning för webbtjänsten.
6. Från den **prenumeration** listrutan väljer du den prenumeration som den nya webbtjänsten kommer att finnas.
7. Från den **resursgruppen** listrutan, Välj en resursgrupp för webbtjänsten. För mer information om resursgrupper finns [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Från den **Region** listrutan väljer du den region där du vill distribuera webbtjänsten.
9. Från den **lagringskonto** listrutan väljer du ett storage-konto för lagring av webbtjänsten.
10. Från den **prisplan** listrutan, Välj en plan i den region som du valde i steg 8.
11. Klicka på **kopiera**.

