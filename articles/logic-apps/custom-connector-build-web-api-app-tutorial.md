---
title: "Skapa egna kopplingar från Web API - Azure Logic Apps | Microsoft Docs"
description: "Skapa egna kopplingar från Web API: er"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Skapa egna kopplingar från Web API: er

Om du vill skapa en anpassad koppling som du kan använda i Logikappar i Azure, Microsoft Flow eller Microsoft PowerApps först skapa ett webb-API som värd med Azure-Webbappar, autentisera med Azure Active Directory och registrera som en koppling med Logic Apps flödet, eller PowerApps. Den här kursen visar hur du utför dessa uppgifter genom att skapa en ASP.NET Web API-app. För mönster som visar strukturen för din anslutningstjänst utlösare och åtgärder, se [skapa anpassade API: er som du kan anropa från logik app arbetsflöden](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Krav

* [Visual Studio 2013 eller senare](https://www.visualstudio.com/vs/). Den här självstudiekursen använder Visual Studio 2015.

* Kod för ditt webb-API. Om du inte har någon, försök med den här kursen: [komma igång med ASP.NET Web API 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

* En Azure-prenumeration. Om du inte har en prenumeration kan du starta med en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan registrera dig för en [betala per användning prenumeration](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Skapa och distribuera en ASP.NET-webbapp till Azure

Skapa ett Visual C# ASP.NET-webbprogram för den här självstudiekursen. 

1. Öppna Visual Studio och välj sedan **filen** > **nytt projekt**.

   1. Expandera **installerad**, gå till **mallar** > **Visual C#** > **Web**, och välj **ASP .NET-webbprogram**.

   2. Ange ett projektnamn och plats lösningens namn för din app och välj sedan **OK**.

   Exempel:

   ![Skapa ett Visual C# ASP.NET-webbprogram](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. I den **nytt ASP.NET-webbprogram** markerar den **Web API** mall. Om du inte redan har markerats väljer **värd i molnet**. Välj **ändra autentisering**.

   ![Välj mall för ”webb-API”, ”värd i molnet”, ”ändra autentisering”](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Välj **ingen autentisering**, och välj **OK**. Du kan konfigurera autentisering senare.

   ![Välj ”Ingen autentisering”](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. När den **nytt ASP.NET-webbprogram** igen och välj **OK**. 

5. I den **skapa Apptjänst** rutan, granska värd inställningarna som beskrivs i tabellen, ändra och välj **skapa**. 

   En [programtjänstplanen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) representerar en samling fysiska resurser som används som värd för appar i din Azure-prenumeration. Lär dig mer om [Apptjänst](../app-service/app-service-value-prop-what-is.md).

   ![Skapa Apptjänst](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | Din Azure arbets- eller skolkonto eller ditt personliga Microsoft-konto | *ditt användarkonto* | Välj ditt konto. | 
   | **Webbprogrammets namn** | *anpassad webb-api-appens namn-* eller standardnamnet | Ange namnet på din Web API-app som används i din app-URL, till exempel: http://*web api-appens namn-*. | 
   | **Prenumeration** | *Azure-prenumeration-name* | Ange den prenumeration som du vill använda. | 
   | **Resursgrupp** | *Azure-grupp-resursnamn* | Välj en befintlig Azure-resursgrupp eller skapa en resursgrupp om du inte redan gjort. <p>**Obs**: ett Azure-resursgrupp ordnar Azure-resurser i din Azure-prenumeration. | 
   | **App Service-Plan** | *App-Service-plan-name* | Välj en befintlig programtjänstplan eller om du inte redan har skapat en plan. | 
   |||| 

   Om du skapar en App Service-Plan, anger du de här inställningarna:

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Plats** | *distribution region* | Välj område för att distribuera din app. | 
   | **Storlek** | *App Service-plan-storlek* | Välj storleken på din plan, som bestämmer kostnad och beräkningskapacitet resurs för service-plan. | 
   |||| 

   Om du vill konfigurera andra resurser som krävs av din app väljer **utforska ytterligare Azure-tjänster**.

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Resurstyp** | *Azure-resurstyp* | Välj och konfigurera eventuella ytterligare resurser som krävs av din app. | 
   |||| 

6. När Visual Studio distribuerar ditt projekt, skapa koden för din app.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Skapa en OpenAPI (Swagger)-fil som beskriver ditt webb-API

Om du vill ansluta din Web API-app till Logic Apps behöver du en [OpenAPI (tidigare Swagger) filen](http://swagger.io/) som beskriver ditt API-åtgärder. Du kan skriva egna OpenAPI definition för din API med den [online Swagger-redigeraren](http://editor.swagger.io/), men den här kursen använder ett verktyg för öppen källkod som heter [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Om du inte redan gjort installera Swashbuckle Nuget-paketet i Visual Studio-projekt.

   1. I Visual Studio väljer **verktyg** > **NuGet Package Manager** > 
    **Pakethanterarkonsolen**.

   2. I den **Pakethanterarkonsolen**, gå till din app projektkatalogen om du inte redan det (kör `Set-Location "project-path"`), och köra detta PowerShell-cmdlet: 
   
      `Install-Package Swashbuckle`

      Exempel:

      ![Package Manager-konsolen, installera Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Om du kör din app när du har installerat Swashbuckle genererar Swashbuckle en OpenAPI fil på denna URL: 
   >
   > http://*{your-web-api-app-root-URL}*/swagger/docs/v1
   > 
   > Swashbuckle skapar också ett användargränssnitt på denna URL: 
   > 
   > http://*{your-web-api-app-root-URL}*  /swagger

3. När du är klar kan du publicera Web API-appen till Azure. Om du vill publicera från Visual Studio, högerklicka på ditt webbprojekt i Solution Explorer, Välj **publicera...** , och följ anvisningarna.

   > [!IMPORTANT]
   > Dubbla åtgärds-ID göra ett OpenAPI dokument som är ogiltigt. Om du har använt exempel C# mallen mallen *upprepas ID för den här åtgärden två gånger*:`Values_Get` 
   > 
   > Åtgärda problemet genom att ändra en instans till `Value_Get` och publicera på nytt.

4. Hämta OpenAPI dokumentet genom att bläddra till den här platsen: 

   http://*{your-web-api-app-root-URL}*/swagger/docs/v1

   Du kan också hämta en [exempel OpenAPI dokument](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) från den här kursen. 
   Kontrollera att du tar bort kommentarer som börjar med ”/ /”, innan du använder dokumentet.

5. Spara innehållet som en JSON-fil. Baserat på din webbläsare kan behöva du kopiera och klistra in den i en tom textfil.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera autentisering för anpassade kopplingar](../logic-apps/custom-connector-azure-active-directory-authentication.md)











