---
title: Distribuera Studio-webbtjänst till flera regioner
titleSuffix: Azure Machine Learning Studio
description: Steg för att distribuera (kopiera) en ny webbtjänst till andra regioner. Enkelt distribuera en webbtjänst till flera regioner utan att behöva flera prenumerationer eller arbetsytor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 536a4ae0b740eae7f6072cbd23d96e199e1598e7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487089"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service-to-multiple-regions"></a>Distribuera en Azure Machine Learning Studio-webbtjänst till flera regioner

De nya webbtjänsterna i Azure kan du enkelt distribuera en Azure Machine Learning Studio-webbtjänst till flera regioner utan att behöva flera prenumerationer eller arbetsytor. 

Priserna beror på region, därför måste du definiera ett faktureringsavtal för varje region där du distribuerar webbtjänsten.

## <a name="to-create-a-plan-in-another-region"></a>Skapa en plan i en annan region
1. Logga in på [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/).
2. Klicka på den **planer** menyalternativ.
3. På planer över visningssida, klickar du på **New**.
4. Från den **prenumeration** listrutan väljer du den prenumeration där den nya planen kommer att finnas.
5. Från den **Region** listrutan väljer du en region för den nya planen. Planera alternativ för den valda regionen visas i den **planera alternativ** på sidan.
6. Från den **resursgrupp** listrutan, Välj en resursgrupp för planen. För mer information om resursgrupper, se [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. I **Plannamnet** skriver du namnet på planen.
8. Under **Avtalsalternativ**, klickar du på den faktureringen för den nya planen.
9. Klicka på **Skapa**.

## <a name="deploying-the-web-service-to-another-region"></a>Distribuera webbtjänsten till en annan region
1. Klicka på den **webbtjänster** menyalternativ.
2. Välj den webbtjänst som du distribuerar till en ny region.
3. Klicka på **kopiera**.
4. I **Webbtjänstnamn**, Skriv ett nytt namn för webbtjänsten.
5. I **Web tjänstbeskrivning**, ange en beskrivning för webbtjänsten.
6. Från den **prenumeration** listrutan väljer du den prenumeration där den nya webbtjänsten kommer att finnas.
7. Från den **resursgrupp** listrutan, Välj en resursgrupp för webbtjänsten. För mer information om resursgrupper, se [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Från den **Region** listrutan väljer du den region där du vill distribuera webbtjänsten.
9. Från den **lagringskonto** listrutan väljer du en storage-konto där du vill lagra webbtjänsten.
10. Från den **prisplan** listrutan väljer du en plan i den region som du valde i steg 8.
11. Klicka på **kopiera**.

