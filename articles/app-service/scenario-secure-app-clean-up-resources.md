---
title: Självstudie – rensa resurser | Azure
description: I den här självstudien får du lära dig hur du rensar de Azure-resurser som allokeras när du skapar webbappen.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428968"
---
# <a name="tutorial-clean-up-resources"></a>Självstudie: Rensa resurser

Om du har slutfört alla steg i den här självstudien för flera delar har du skapat en app service, en värd plan för app service och ett lagrings konto i en resurs grupp.  Du skapade också en app-registrering i Azure AD.  När de inte längre behövs kan du ta bort dessa resurser och registrera registreringen så att du inte fortsätter att Periodisera avgifter.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Ta bort de Azure-resurser som skapats när du följer självstudien

## <a name="delete-the-resource-group"></a>Ta bort resursgruppen
I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper** på Portal-menyn och väljer den resurs grupp som innehåller app service-och App Service-planen.

Välj **ta bort resurs grupp** för att ta bort resurs gruppen och alla resurser.

:::image type="content" alt-text="Ta bort resursgrupp" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Det kan ta flera minuter att köra det här kommandot.

## <a name="delete-the-app-registration"></a>Ta bort registrerings appen
Välj **Azure Active Directory** på menyn Portal, sedan **Appregistreringar** och sedan det program som du skapade.
:::image type="content" alt-text="Välj app-registrering" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

I Översikt över app Registration väljer du **ta bort**.
:::image type="content" alt-text="Ta bort registrering av app" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Ta bort de Azure-resurser som skapats när du följer självstudien

Lär dig hur du ansluter en [.net Core-app](tutorial-dotnetcore-sqldb-app.md), [python-app](tutorial-python-postgresql-app.md), [java-app](tutorial-java-spring-cosmosdb.md)eller [Node.js app](tutorial-nodejs-mongodb-app.md) till en databas.