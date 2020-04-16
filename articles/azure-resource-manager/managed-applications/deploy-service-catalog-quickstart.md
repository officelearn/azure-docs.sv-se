---
title: Använda Azure-portalen för att distribuera tjänstkatalogapp
description: Visar konsumenter av hanterade program hur du distribuerar en tjänstkatalogapp via Azure-portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391712"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Snabbstart: Distribuera tjänstkatalogapp via Azure Portal

I [föregående snabbstart](publish-service-catalog-app.md)publicerade du en hanterad programdefinition. I den här snabbstarten skapar du en tjänstkatalogapp från den definitionen.

## <a name="create-service-catalog-app"></a>Skapa tjänstkatalogapp

Gör så här i Azure-portalen:

1. Välj **Skapa en resurs**.

   ![Skapa en resurs](./media/deploy-service-catalog-quickstart/create-new.png)

1. Sök efter **Service Catalog Managed Application** och välj det bland de tillgängliga alternativen.

   ![Sök efter tjänstkatalogprogram](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Du ser en beskrivning av tjänsten Hanterat program. Välj **Skapa**.

   ![Välj Skapa](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Portalen visar de hanterade programdefinitioner som du har åtkomst till. Välj den som du vill distribuera från de tillgängliga definitionerna. I den här snabbstarten använder du definitionen **Hanterat lagringskonto** som du skapade i föregående snabbstart. Välj **Skapa**.

   ![Välj definition att distribuera](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Ange värden för fliken **Grunderna.** Välj Azure-prenumerationen för att distribuera din tjänstkatalogapp till. Skapa en ny resursgrupp med namnet **applicationGroup**. Välj en plats för din app. När du är klar väljer du **OK**.

   ![Ange värden för grundläggande](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Ange ett prefix för lagringskontonamnet. Välj vilken typ av lagringskonto som ska skapas. När du är klar väljer du **OK**.

   ![Ange värden för lagring](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Granska sammanfattningen. När valideringen har slutförts väljer du **OK** för att starta distributionen.

   ![Visa sammanfattning](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Visa resultat

När tjänstkatalogappen har distribuerats har du två nya resursgrupper. En resursgrupp innehåller tjänstkatalogappen. Den andra resursgruppen innehåller resurserna för tjänstkatalogappen.

1. Visa resursgruppen **applicationGroup** för att se tjänstkatalogappen.

   ![Visa program](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Visa resursgruppen **ApplicationGroup{hash-characters}** om du vill visa resurserna för tjänstkatalogappen.

   ![Visa resurser](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar definitionsfilerna för ett hanterat program finns i [Skapa och publicera en hanterad programdefinition](publish-service-catalog-app.md).
* För Azure CLI finns i [Distribuera tjänstkatalogapp med Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* För PowerShell finns i [Distribuera tjänstkatalogapp med PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
