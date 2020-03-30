---
title: Löpande distribution för Azure Functions
description: Använd de kontinuerliga distributionsfunktionerna i Azure App Service för att publicera dina funktioner.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277029"
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions

Du kan använda Azure Functions för att distribuera koden kontinuerligt med hjälp av [källkontrollintegrering](functions-deployment-technologies.md#source-control). Källkontrollintegrering möjliggör ett arbetsflöde där en koduppdatering utlöser distribution till Azure. Om du inte har tidigare i Azure Functions kan du komma igång genom att granska [översikten Azure Functions](functions-overview.md).

Kontinuerlig distribution är ett bra alternativ för projekt där du integrerar flera och frekventa bidrag. När du använder kontinuerlig distribution behåller du en enda sanningskälla för din kod, vilket gör att teamen enkelt kan samarbeta. Du kan konfigurera kontinuerlig distribution i Azure Functions från följande källkodsplatser:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Enheten för distribution för funktioner i Azure är funktionsappen. Alla funktioner i en funktionsapp distribueras samtidigt. När du har aktiverat kontinuerlig distribution konfigureras åtkomst till funktionskod i Azure-portalen som *skrivskyddad* eftersom sanningens källa är inställd på att vara någon annanstans.

## <a name="requirements-for-continuous-deployment"></a>Krav för kontinuerlig distribution

För att kontinuerlig distribution ska lyckas måste katalogstrukturen vara kompatibel med den grundläggande mappstrukturen som Azure Functions förväntar sig.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Kontinuerlig distribution stöds ännu inte för Linux-appar som körs på en förbrukningsplan. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Konfigurera kontinuerlig distribution

Om du vill konfigurera kontinuerlig distribution för en befintlig funktionsapp slutför du de här stegen. Stegen demonstrerar integrering med en GitHub-databas, men liknande steg gäller för Azure-databaser eller andra källkodsdatabaser.

1. I funktionsappen i [Azure-portalen](https://portal.azure.com)väljer du **Plattformsfunktioner** > **Distributionscenter**.

    ![Öppna distributionscenter](./media/functions-continuous-deployment/platform-features.png)

2. Välj **GitHub**i **Distributionscenter**och välj sedan **Auktorisera**. Om du redan har auktoriserat GitHub väljer du **Fortsätt**. 

    ![Distributionscenter för Azure App-tjänst](./media/functions-continuous-deployment/github.png)

3. Välj knappen **Auktorisera AzureAppService** i GitHub. 

    ![Auktorisera Azure App-tjänst](./media/functions-continuous-deployment/authorize.png)
    
    Välj **Fortsätt**i **Distributionscenter** i Azure-portalen .

4. Välj en av följande byggleverantörer:

    * **App Service build service**: Bäst när du inte behöver en version eller om du behöver en generisk version.
    * **Azure Pipelines (Preview)**: Bäst när du behöver mer kontroll över bygget. Den här providern är för närvarande i förhandsversion.

    ![Välj en byggleverantör](./media/functions-continuous-deployment/build.png)

5. Konfigurera information som är specifik för det källkontrollalternativ som du angav. För GitHub måste du ange eller välja värden för **Organisation,** **Databas**och **Gren**. Värdena baseras på kodens plats. Välj sedan **Fortsätt**.

    ![Konfigurera GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Granska all information och välj sedan **Slutför** för att slutföra distributionskonfigurationen.

    ![Sammanfattning](./media/functions-continuous-deployment/summary.png)

När processen är klar distribueras all kod från den angivna källan till din app. Då utlöser ändringar i distributionskällan en distribution av dessa ändringar i funktionsappen i Azure.

## <a name="deployment-scenarios"></a>Distributionsscenarier

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner till kontinuerlig distribution

Om du redan har skrivit funktioner i [Azure-portalen](https://portal.azure.com) och vill hämta innehållet i din app innan du växlar till kontinuerlig distribution går du till fliken **Översikt för** din funktionsapp. Välj knappen **Ladda ned appinnehåll.**

![Ladda ned appinnehåll](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> När du har konfigurerat kontinuerlig integrering kan du inte längre redigera källfilerna i funktionsportalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
