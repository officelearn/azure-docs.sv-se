---
title: Kontinuerlig distribution för Azure Functions | Microsoft Docs
description: Använda funktioner för kontinuerlig distribution i Azure App Service för att publicera dina funktioner.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594538"
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions

Du kan använda Azure Functions för att distribuera din kod kontinuerligt med hjälp av [källkontrollsintegration](functions-deployment-technologies.md#source-control). Integrering av källkontroll möjliggör ett arbetsflöde som utlöser en uppdatering distribution till Azure. Om du inte har använt Azure Functions kan komma igång genom att granska den [översikt över Azure Functions](functions-overview.md).

Kontinuerlig distribution är ett bra alternativ för projekt där du integrera flera och frekventa bidrag. När du använder kontinuerlig distribution kan ha du en enda sanningskällan för din kod, vilket gör att team att enkelt samarbeta. Du kan konfigurera kontinuerlig distribution i Azure Functions från följande platser för käll-kod:

* [Azure-lagringsplatser](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Enhet för distribution för funktioner i Azure är funktionsappen. Alla funktioner i en funktionsapp distribueras på samma gång. När du aktiverar kontinuerlig distribution får åtkomst till Funktionskoden i Azure-portalen har konfigurerats som *skrivskyddad* eftersom källa till sanningen är inställd på att någon annanstans.

## <a name="requirements-for-continuous-deployment"></a>Krav för kontinuerlig distribution

Din katalogstruktur måste vara kompatibla med grundläggande mappstrukturen som förväntar sig att Azure Functions för kontinuerlig distribution ska lyckas.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Konfigurera kontinuerlig distribution

Gör följande om du vill konfigurera kontinuerlig distribution för en befintlig funktionsapp. Stegen visar integrering med en GitHub-lagringsplats, men liknande steg som gäller för Azure-databaser eller andra källkodslager.

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com)väljer **plattformsfunktioner** > **Deployment Center**.

    ![Öppna Deployment Center](./media/functions-continuous-deployment/platform-features.png)

2. I **Deployment Center**väljer **GitHub**, och välj sedan **auktorisera**. Om du har redan behörighet GitHub, Välj **Fortsätt**. 

    ![Distributionscenter för Azure App Service](./media/functions-continuous-deployment/github.png)

3. I GitHub, väljer du den **auktorisera AzureAppService** knappen. 

    ![Auktorisera Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    I **Deployment Center** i Azure-portalen väljer du **Fortsätt**.

4. Välj något av följande build-providers:

    * **App Service byggtjänst**: Bästa när du inte behöver en version eller om du behöver en allmän version.
    * **Azure Pipelines (förhandsversion)** : Bäst när du behöver mer kontroll över versionen. Den här providern är för närvarande i förhandsversion.

    ![Välj en build-provider](./media/functions-continuous-deployment/build.png)

5. Konfigurera information som är specifika för källalternativet för kontrollen som du har angett. För GitHub, måste du ange eller Välj värden för **organisation**, **databasen**, och **gren**. Värdena är baserade på platsen för din kod. Välj **Fortsätt**.

    ![Konfigurera GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Granska all information och välj sedan **Slutför** att slutföra din konfiguration av distributionen.

    ![Sammanfattning](./media/functions-continuous-deployment/summary.png)

När processen är klar distribueras all kod från angiven källa till din app. I det här läget Utlös ändringar i distributionskälla en distribution av dessa ändringar till din funktionsapp i Azure.

## <a name="deployment-scenarios"></a>Distributionsscenarier

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner för kontinuerlig distribution

Om du redan har skrivit funktioner den [Azure-portalen](https://portal.azure.com) och du vill hämta innehållet i din app innan du växlar till kontinuerlig distribution, gå till den **översikt** fliken på din funktionsapp. Välj den **ladda ned appinnehåll** knappen.

![Ladda ned appinnehåll](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> När du konfigurerar kontinuerlig integrering, kan du inte längre redigera källfilerna i Functions-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
