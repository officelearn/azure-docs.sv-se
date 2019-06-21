---
title: Kontinuerlig distribution för Azure Functions | Microsoft Docs
description: Använda kontinuerlig distribution-funktioner i Azure App Service för att publicera dina funktioner.
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
ms.openlocfilehash: 2dc5fab0966c2ead0276cd8b23ea764bd4f9ef59
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190480"
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions

Azure Functions kan du distribuera din kod kontinuerligt via [källkontrollsintegration](functions-deployment-technologies.md#source-control). Detta gör att ett arbetsflöde där koden uppdaterar utlösaren distribution till Azure. Om du är nybörjare på Azure Functions, Kom igång med den [översikt över Azure Functions](functions-overview.md).

Kontinuerlig distribution är ett bra alternativ för projekt där du integrerar flera och frekventa bidrag. Du kan även behålla en enda sanningskällan för funktionskoden. Du kan konfigurera kontinuerlig distribution i Azure Functions från följande platser för käll-kod:

* [Azure-lagringsplatser](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Enhet för distribution för Azure functions är funktionsappen. Det innebär att alla funktioner i en funktionsapp distribueras på samma gång. När kontinuerlig distribution har aktiverats kan åtkomst till Funktionskoden i Azure-portalen har konfigurerats som *skrivskyddad*eftersom källa till sanningen är inställd på att någon annanstans.

## <a name="requirements-for-continuous-deployment"></a>Krav för kontinuerlig distribution

Directory-struktur måste vara kompatibel med följande grundläggande mappstrukturen som förväntar sig att Azure Functions för kontinuerlig distribution ska lyckas:

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Konfigurera kontinuerlig distribution

Använd den här proceduren för att konfigurera kontinuerlig distribution för en befintlig funktionsapp. De här stegen visar integrering med en GitHub-lagringsplats, men liknande steg som gäller för Azure-databaser eller andra källkodslager.

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com)väljer **plattformsfunktioner** > **distributionscenter**.

    ![Öppna deployment center](./media/functions-continuous-deployment/platform-features.png)

2. I den **Deployment Center**väljer **GitHub**, och välj sedan **auktorisera**. Om du har redan behörighet GitHub, väljer du **Fortsätt**. 

    ![Distributionscenter](./media/functions-continuous-deployment/github.png)

3. I GitHub, väljer **auktorisera AzureAppService**. 

    ![Auktorisering](./media/functions-continuous-deployment/authorize.png)
    
    I Azure-portalen **Deployment Center**väljer **Fortsätt**.

4. Välj något av följande build-providers:

    * **App Service byggtjänst** – det är bäst om du inte behöver en version eller om du behöver en allmän version.
    * **Azure Pipelines (förhandsversion)** – det är bäst när du behöver mer kontroll över versionen. Den här providern förhandsvisas just nu.

    ![Att välja en build-provider](./media/functions-continuous-deployment/build.png)

5. Konfigurera information som är specifika för källalternativet för kontrollen som du har angett. För GitHub, måste du ange den **organisation**, **databasen**, och **gren** var koden finns. Välj **Fortsätt**.

    ![Konfigurera GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Granska all information och välj slutligen **Slutför** att slutföra din konfiguration av distributionen.

    ![Sammanfattning](./media/functions-continuous-deployment/summary.png)

När processen är klar distribueras all kod från angiven källa till din app. I det här läget Utlös ändringar i distributionskälla en distribution av dessa ändringar till din funktionsapp i Azure.

## <a name="deployment-scenarios"></a>Distributionsscenarier

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner för kontinuerlig distribution

Om du redan har skrivit funktioner den [Azure-portalen](https://portal.azure.com) och vill hämta innehållet i din app innan du växlar till kontinuerlig distribution, du bör gå till den **översikt** i din funktion appen och klicka på den **ladda ned appinnehåll** knappen.

![Ladda ned appinnehåll](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> När du konfigurerar kontinuerlig integrering, kan du inte längre redigera källfilerna i Functions-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
