---
title: "Skapa din första Java-webbapp i Azure"
description: "Distribuera en grundläggande Java-app och lär dig hur du kör webbappar i App Service."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/08/2017
ms.author: cephalin;robmcm
ms.custom: mvc, devcenter
ms.openlocfilehash: d44fff1e59198d662356c4d7739c05e538ba57b9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="create-your-first-java-web-app-in-azure"></a>Skapa din första Java-webbapp i Azure

Azure [Web Apps](app-service-web-overview.md) ger en mycket skalbar, automatisk uppdatering värdtjänst. Den här snabbstarten visar hur du distribuerar en Java-webbapp till App Service med hjälp av [Eclipse IDE för Java EE-utvecklare](http://www.eclipse.org/).

När du har slutfört den här snabbstarten, ser ditt program ut ungefär som följande bild, när du visar den i en webbläsare:

!["Hello Azure!" exempelwebbapp](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="prerequisites"></a>Krav

Installera följande för att slutföra den här snabbstarten:

* Kostnadsfria <a href="http://www.eclipse.org/downloads/" target="_blank">Eclipse IDE för Java EE-utvecklare</a>. Den här snabbstarten använder Eclipse Neon.
* <a href="/java/azure/eclipse/azure-toolkit-for-eclipse-installation" target="_blank">Azure Toolkit för Eclipse</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Skapa ett dynamiskt webbprojekt i Eclipse

Välj **Arkiv** > **Nytt** > **Dynamic Web Project** (Dynamiskt webbprojekt) Eclipse.

I dialogrutan **New Dynamic Web Project** (Nytt dynamiskt webbprojekt) ger du projektet namnet **MyFirstJavaOnAzureWebApp** och väljer **Slutför**.
   
![Dialogrutan för nytt dynamiskt webbprojekt](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

### <a name="add-a-jsp-page"></a>Lägg till en JSP-sida

Om projektutforskaren inte visas kan du återställa den.

![Java EE-arbetsytan för Eclipse](./media/app-service-web-get-started-java/pe.png)

Expandera projektet **MyFirstJavaOnAzureWebApp** i projektutforskaren.
Högerklicka på **Webbinnehåll** och välj sedan **Ny** > **JSP-fil**.

![Menyn för en ny JSP-fil i projektutforskaren](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

I dialogrutan **Ny JSP-fil**:

* Namnge filen **index.jsp**.
* Välj **Slutför**.

  ![Dialogruta för en ny JSP-fil](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

I filen index.jsp ersätter du elementet `<body></body>` med följande kod:

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Spara ändringarna.

## <a name="publish-the-web-app-to-azure"></a>Publicera webbappen i Azure

Högerklicka på projektet i projektutforskaren och välj sedan **Azure** > **Publicera som Azure Web App**.

![Publicera som Azure Web App (snabbmeny)](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

I dialogrutan för **Azure-inloggning** behåller du alternativet **Interaktiv** och väljer sedan **Logga in**.

Följ anvisningarna för inloggning.

### <a name="deploy-web-app-dialog-box"></a>Dialogrutan Distribuera webbapp

När du har loggats in på Azure-kontot visas dialogrutan **Distribuera webbapp**.

Välj **Skapa**.

![Dialogrutan Distribuera webbapp](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

### <a name="create-app-service-dialog-box"></a>Dialogrutan Skapa App Service

Dialogrutan **Skapa App Service** visas med standardvärden. Numret **170602185241** som visas i följande bild skiljer sig från vad som visas i din dialogruta.

![Dialogrutan Skapa App Service](./media/app-service-web-get-started-java/cas1.png)

I dialogrutan **Skapa App Service**:

* Behåll det genererade namnet för webbappen. Användarnamnet måste vara unikt inom Azure. Namnet är en del av URL-adressen för webbappen. Exempel: om webbappens namn är **MyJavaWebApp** så är URL-adressen *myjavawebapp.azurewebsites.net*.
* Behåll webbehållaren som är standard.
* Välj en Azure-prenumeration.
* På fliken **App Service-plan**:

  * **Skapa ny**: Behåll standardvärdet, som är namnet på App Service-planen.
  * **Plats**: Välj **Europa, västra** eller en plats nära dig.
  * **Prisnivå**: Markera det kostnadsfria alternativet. Se [Priser för App Service](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) för funktioner.

   ![Dialogrutan Skapa App Service](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

### <a name="resource-group-tab"></a>Flik för resursgrupp

Välj fliken **Resursgrupp**. Behåll det genererade standardvärdet för resursgruppen.

![Flik för resursgrupp](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Välj **Skapa**.

<!--
### The JDK tab

Select the **JDK** tab. Keep the default, and then select **Create**.

![Create App Service plan](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)
-->

Webbappen skapas av Azure Toolkit och en dialogruta med en förloppsindikator visas.

![Dialogruta med förloppsindikator för hur apptjänsten skapas](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

### <a name="deploy-web-app-dialog-box"></a>Dialogrutan Distribuera webbapp

I dialogrutan **Distribuera webbapp** väljer du **Distribuera till rot**. Om du har en apptjänst på *wingtiptoys.azurewebsites.net* och inte distribuerar webbappen till roten så distribueras webbappen med namnet **MyFirstJavaOnAzureWebApp** till *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp*.

![Dialogrutan Distribuera webbapp](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

Dialogrutan visar valen för Azure, JDK och webbehållaren.

Välj **Distribuera** för att publicera webbappen i Azure.

När publiceringen är klar väljer du länken **Publicerade** i dialogrutan **Azure-aktivitetsloggen**.

![Dialogrutan för Azure aktivitetsloggen](./media/app-service-web-get-started-java/aal.png)

Grattis! Din webbapp har distribuerats till Azure. 

!["Hello Azure!" exempelwebbapp](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="update-the-web-app"></a>Uppdatera webbappen

Ändra JSP-exempelkod till ett annat meddelande.

```jsp
<body>
<h1><% out.println("Hello again Azure!"); %></h1>
</body>
```

Spara ändringarna.

Högerklicka på projektet i projektutforskaren och välj sedan **Azure** > **Publicera som Azure Web App**.

Dialogrutan **Distribuera webbapp** visas med apptjänsten som du skapade tidigare. 

> [!NOTE] 
> Välj **Distribuera till rot** varje gång du publicerar. 
> 

Välj webbappen och sedan **Distribuera**. Då publiceras ändringarna.

När länken **Publicering** visas väljer du den för att bläddra till webbappen och se ändringarna.

## <a name="manage-the-web-app"></a>Hantera webbappen

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att se den webbapp som du skapade.

Välj **Resursgrupper** på den vänstra menyn.

![Portalnavigering för resursgrupper](media/app-service-web-get-started-java/rg.png)

Välj resursgruppen. På sidan visas resurserna som du skapade i den här snabbstarten.

![Resursgruppen myResourceGroup](media/app-service-web-get-started-java/rg2.png)

Välj webbappen (**webbapp-170602193915** i den föregående bilden).

Sidan **Översikt** visas. Den här sidan ger dig en översikt över hur det går för appen. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationer som du kan öppna. 

![App Service-sidan på Azure Portal](media/app-service-web-get-started-java/web-app-blade.png)

[!INCLUDE [clean-up-section-portal-web-app](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mappa anpassad domän](app-service-web-tutorial-custom-domain.md)
