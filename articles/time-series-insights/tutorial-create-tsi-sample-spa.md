---
title: 'Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp | Microsoft Docs'
description: Lär dig hur du skapar ett webb program på en enskild sida som frågar och återger data från en Azure Time Series Insights-miljö.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194207"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp

Den här självstudien vägleder dig genom processen att skapa ett eget webb program (SPA) för en enskild sida för att få åtkomst till Azure Time Series Insights data.

I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> * Programdesignen
> * Registrera ditt program med Azure Active Directory (Azure AD)
> * Hur du skapar, publicerar och testar ditt webbprogram

> [!NOTE]
> * Käll koden för den här själv studie kursen finns på [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Den Time Series Insights [klient exempel appen](https://insights.timeseries.azure.com/clientsample) är värd för att visa den färdiga appen som används i den här självstudien.

Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Förutsättningar

* En kostnads fri kopia av Visual Studio. Hämta [Community-versionerna 2017 eller 2019](https://www.visualstudio.com/downloads/) för att komma igång.

* Komponenterna IIS Express, webb distribution och Azure Cloud Services Core Tools för Visual Studio. Lägg till komponenterna genom att ändra Visual Studio-installationen.

## <a name="understand-application-design"></a>Förstå program design

Time Series Insights SPA är grunden för den design och kod som används i den här självstudien. Koden använder klient biblioteket för Time Series Insights Java Script. Time Series Insights klient biblioteket tillhandahåller en abstraktion för två huvudsakliga API-kategorier:

- **Omslutnings metoder för att anropa API: er för Time Series Insights frågor**: REST API: er som du kan använda för att fråga efter Time Series Insights data med hjälp av JSON-baserade uttryck. Metoderna är ordnade under TsiClient. Server namespace i biblioteket.

- **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som du kan använda för att visualisera Time Series Insights data på en webb sida. Metoderna är ordnade under TsiClient. UX-namnrymden i biblioteket.

I den här självstudien används även data från exempel programmets Time Series Insightss miljö. Mer information om strukturen i Time Series Insights exempel program och hur den använder Time Series Insights klient bibliotek finns i självstudien [utforska Azure Time Series Insights Java Script-klient biblioteket](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>Registrera dig för Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Bygg och publicera

1. Skapa en katalog för att lagra din apps projektfiler. Gå sedan till var och en av följande URL: er. Högerklicka på den **råa** länken i det övre högra hörnet på sidan och välj **Spara som** för att spara filerna i projekt katalogen.

   - [*index. html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML och Java Script för sidan
   - [*sampleStyles. CSS*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): CSS-formatmall

   > [!NOTE]
   > Beroende på webbläsare kan du behöva ändra fil namns tilläggen till. html eller. CSS innan du sparar filen.

1. Kontrol lera att de nödvändiga komponenterna är installerade i Visual Studio. Komponenterna IIS Express, webb distribution och Azure Cloud Services Core Tools för Visual Studio måste vara installerade.

    [![Visual Studio – ändra de installerade komponenterna](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Visual Studio-upplevelsen kan skilja sig något från de exempel som visas beroende på din version och dina konfigurations inställningar.

1. Öppna Visual Studio och logga in. Om du vill skapa ett projekt för webb programmet väljer du **Öppna** > **webbplats**på **Arkiv** -menyn.

    [![Visual Studio – skapa en ny lösning](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. I fönstret **Öppna** webbplats väljer du den arbets katalog där du sparade HTML-och CSS-filerna och väljer sedan **Öppna**.

   [![Visual Studio – menyn Arkiv med alternativen öppna och webbplats](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. I Visual Studio **View** -menyn väljer du **Solution Explorer**. Den nya lösningen öppnas. Det innehåller ett webbplats projekt (jordglob) som innehåller HTML-och CSS-filer.

   [![Visual Studio – den nya lösningen i Solution Explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Innan du publicerar din app måste du ändra konfigurations inställningarna i *index. html*.

   1. Ta bort kommentarer till de tre raderna under `"PROD RESOURCE LINKS"` kommentaren för att växla beroenden från utveckling till produktion. Kommentera de tre raderna under kommentaren `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Dina beroenden ska vara kommenterade som i följande exempel:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Om du vill konfigurera appen så att den använder ditt ID för Azure AD- `clientID` app-registrering ändrar du värdet så att det använder det **program-ID** som du kopierade i **steg 3** när du [registrerade programmet för att använda Azure AD](#register-with-azure-ad). Om du har skapat en utloggnings- **URL** i Azure AD anger du det `postLogoutRedirectUri` värdet som värde.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Exempel:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Spara *index. html*när du är klar med ändringarna.

1. Publicera webb programmet i din Azure-prenumeration som en Azure App Service.  

   > [!NOTE]
   > Flera alternativ i skärm bilderna som visas i följande steg fylls i automatiskt med data från din Azure-prenumeration. Det kan ta några sekunder för varje fönster att läsas in helt.  

   1. I Solution Explorer högerklickar du på noden webbplats projekt och väljer sedan **publicera webbapp**.  

      [![Visual Studio – Välj alternativet Solution Explorer publicera webb program](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Välj **Starta** för att börja publicera din app.

      [![Visual Studio – fönstret publicera profil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Välj att publicera en ny Azure App Service instans eller Använd en befintlig.

      [![Välj en Azure App Service instans](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Välj den prenumeration som du vill använda för att publicera programmet. Välj **TsiSpaApp** -projektet. Välj **OK**.

      [![Visual Studio – fönstret publicera profil App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Välj **publicera** för att distribuera webb programmet.

      [![Visual Studio – alternativet Publicera och publicera logg data](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. En lyckad publicerings logg visas i fönstret **utdata** i Visual Studio. När distributionen är färdig öppnar Visual Studio webb programmet i en webbläsare-flik och du uppmanas att logga in. När inloggningen är klar fylls Time Series Insights kontroller med data.

   1. Navigera till din webbapp och logga in för att visa de renderade Time Series Insights visuella data.

      [![Granska den värdbaserade webb programmet](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Felsöka  

Felkod/-villkor | Beskrivning
---------------------| -----------
*AADSTS50011: Ingen svarsadress har registrerats för appen.* | Azure AD-registreringen saknar en omdirigerings- **URI** -egenskap. Gå till **autentisering** > omdirigerings-**URI: er** för din Azure AD-programregistrering. Kontrol lera att omdirigerings- **URI: n** du hade möjlighet att ange i **steg 2** eller **steg 4** när du [registrerade programmet för att använda Azure AD](#register-with-azure-ad) finns.
*AADSTS50011: Svars-URL: en som anges i begäran matchar inte svars-URL: er som har kon figurer ATS för programmet: '\<Program-ID-GUID > '.* | Den `postLogoutRedirectUri` som anges > i **steg 6. b** i [build och publicera webb programmet](#build-and-publish) måste matcha det värde som anges under omdirigering av**URI: er** i din Azure AD-programregistrering. |
Webb programmet läses in, men det har en ej formaterad, text inloggnings sida med en vit bakgrund. | Kontrol lera att Sök vägarna som beskrivs i **steg 6** i [build och publicera webb programmet](#build-and-publish) är korrekta. Om webbappen inte kan hitta CSS-filer kommer sidan inte att formateras korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudiekursen skapar flera körningar av Azure-tjänster. Om du inte planerar att slutföra den här själv studie serien rekommenderar vi att du tar bort alla resurser för att undvika onödiga kostnader.

På Azure Portal vänstra menyn:

1. Välj **resurs grupper**och välj sedan den resurs grupp som du skapade för Time Series Insightss miljön. Välj **ta bort resurs grupp**längst upp på sidan, ange namnet på resurs gruppen och välj sedan **ta bort**.
1. Välj **resurs grupper**och välj sedan den resurs grupp som skapades av enhets simulerings lösnings Accelerator. Välj **ta bort resurs grupp**längst upp på sidan, ange namnet på resurs gruppen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Programdesignen
> * Registrera ditt program med Azure AD
> * Hur du skapar, publicerar och testar ditt webbprogram

Den här självstudien integreras med Azure AD och använder identiteten för den användare som är inloggad för att hämta en åtkomsttoken. Information om hur du kommer åt Time Series Insights-API med hjälp av identiteten för en tjänst eller ett daemon-program finns i den här artikeln:

> [!div class="nextstepaction"]
> [Autentisering och auktorisering för Azure Time Series Insights API](time-series-insights-authentication-and-authorization.md)
