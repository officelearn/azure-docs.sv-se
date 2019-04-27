---
title: Använda Azure-portalen för att distribuera tjänsten katalogapp | Microsoft Docs
description: Visar hur du distribuerar en tjänst katalogapp via Azure portal för användare av hanterade program.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588944"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Distribuera service catalog-app via Azure-portalen

I den [föregående Snabbstart](publish-managed-app-definition-quickstart.md), du har publicerat en definition för hanterade program. I den här snabbstarten skapar du en tjänst katalogapp från den definitionen.

## <a name="create-service-catalog-app"></a>Skapa service catalog-app

I Azure-portalen använder du följande steg:

1. Välj **Skapa en resurs**.

   ![Skapa en resurs](./media/deploy-service-catalog-quickstart/create-new.png)

1. Sök efter **hanteras av Tjänstkatalogprogram** och välj bland de tillgängliga alternativen.

   ![Sök efter tjänstkatalogprogram](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Du kan se en beskrivning av tjänsten hanteras. Välj **Skapa**.

   ![Välj Skapa](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Portalen visar definitioner för hanterade program som du har åtkomst till. Tillgängliga definitioner, väljer du den som du vill distribuera. I den här snabbstarten använder den **hanterat Lagringskonto** definition som du skapade i föregående snabbstarten. Välj **Skapa**.

   ![Välj definitionen för att distribuera](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Ange värden för den **grunderna** fliken. Välj den Azure-prenumeration att distribuera tjänsten catalog appen till. Skapa en ny resursgrupp med namnet **applicationGroup**. Välj en plats för din app. När du är klar väljer **OK**.

   ![Ange värden för basic](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Ange ett prefix för lagringskontonamnet. Välj typ av lagringskonto för att skapa. När du är klar väljer **OK**.

   ![Ange värden för lagring](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Granska sammanfattningen. När verifieringen lyckas, väljer **OK** att starta distributionen.

   ![Visa sammanfattning](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Visa resultat

När service catalog appen har distribuerats, har du två nya resursgrupper. En resursgrupp innehåller service catalog-app. Andra resursgruppen innehåller resurser för tjänstapp för katalogen.

1. Visa resursgruppen med namnet **applicationGroup** att se tjänstapp för katalogen.

   ![Visa program](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Visa resursgruppen med namnet **applicationGroup {hash-tecken}** att visa resurserna för service catalog-app.

   ![Visa resurser](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Nästa steg

* Läs hur du skapar definitionsfiler för ett hanterat program i [skapa och publicera en hanterad programdefinition](publish-service-catalog-app.md).
* Azure CLI, se [distribuera service catalog-app med Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* PowerShell, se [distribuera service catalog-app med PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).