---
title: Löpande distribution för Azure Functions
description: Använd funktionerna för kontinuerlig distribution i Azure App Service för att publicera dina funktioner.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277029"
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions

Du kan använda Azure Functions för att distribuera koden kontinuerligt genom att använda [käll kontroll integrering](functions-deployment-technologies.md#source-control). Med käll kontroll integrering kan du använda ett arbets flöde där en kod uppdatering utlöser distribution till Azure. Om du inte har använt Azure Functions, kom igång genom att granska [Azure Functions översikten](functions-overview.md).

Kontinuerlig distribution är ett lämpligt alternativ för projekt där du integrerar flera och ofta förekommande bidrag. När du använder kontinuerlig distribution upprätthåller du en enda källa för sanningen för din kod, vilket gör det möjligt för team att samar beta på ett enkelt sätt. Du kan konfigurera kontinuerlig distribution i Azure Functions från följande käll kods platser:

* [Azure-lagringsplatser](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Enhets distributionen för funktioner i Azure är Function-appen. Alla funktioner i en Function-app distribueras samtidigt. När du aktiverar kontinuerlig distribution konfigureras åtkomst till funktions kod i Azure Portal som *skrivskyddad* eftersom källan till sanningen är inställt på någon annan stans.

## <a name="requirements-for-continuous-deployment"></a>Krav för kontinuerlig distribution

För att kontinuerlig distribution ska lyckas måste katalog strukturen vara kompatibel med den grundläggande mappstrukturen som Azure Functions förväntar sig.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Kontinuerlig distribution stöds ännu inte för Linux-appar som körs i en förbruknings plan. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Konfigurera kontinuerlig distribution

Slutför de här stegen för att konfigurera kontinuerlig distribution för en befintlig Function-app. Stegen demonstrerar integreringen med en GitHub-lagringsplats, men liknande steg gäller för Azure databaser eller andra käll kods centraler.

1. I din Function-app i [Azure Portal](https://portal.azure.com)väljer du **Platform features** > **Deployment Center**.

    ![Öppna distributions Center](./media/functions-continuous-deployment/platform-features.png)

2. I **distributions Center**väljer du **GitHub**och väljer sedan **auktorisera**. Om du redan har auktoriserat GitHub väljer du **Fortsätt**. 

    ![Azure App Service distributions Center](./media/functions-continuous-deployment/github.png)

3. I GitHub väljer du knappen **auktorisera AzureAppService** . 

    ![Auktorisera Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    I **distributions Center** i Azure Portal väljer du **Fortsätt**.

4. Välj en av följande build-providers:

    * **App Service Build Service**: bäst när du inte behöver en version eller om du behöver en allmän version.
    * **Azure-pipeliner (för hands version)**: bäst när du behöver mer kontroll över versionen. Den här providern är för närvarande en för hands version.

    ![Välj en build-Provider](./media/functions-continuous-deployment/build.png)

5. Konfigurera information som är specifik för det alternativ för käll kontroll som du har angett. För GitHub måste du ange eller välja värden för **organisation**, **lagrings plats**och **gren**. Värdena baseras på kodens plats. Välj sedan **Fortsätt**.

    ![Konfigurera GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Granska all information och välj sedan **Slutför** för att slutföra distributions konfigurationen.

    ![Sammanfattning](./media/functions-continuous-deployment/summary.png)

När processen är färdig distribueras all kod från den angivna källan till din app. Vid detta tillfälle utlöser ändringar i distributions källan en distribution av ändringarna till din Function-app i Azure.

## <a name="deployment-scenarios"></a>Distributionsscenarier

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner till kontinuerlig distribution

Om du redan har skrivit funktioner i [Azure Portal](https://portal.azure.com) och du vill ladda ned innehållet i din app innan du växlar till kontinuerlig distribution, går du till fliken **Översikt** i din Function-app. Välj knappen **Hämta appens innehåll** .

![Hämta app-innehåll](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> När du har konfigurerat kontinuerlig integrering kan du inte längre redigera dina källfiler i functions-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
