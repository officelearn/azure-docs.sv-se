---
title: 'Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp | Microsoft Docs'
description: Lär dig hur du skapar ett enkelsidigt program som frågar och återger data från en Azure Time Series Insights-miljö.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: e415c681ae5a35de6e8ff76e09cfef8cc8cc98f8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544068"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp

Den här självstudien vägleder dig genom processen med att skapa egna enkelsidiga webbprogram (SPA) att komma åt Azure Time Series Insights-data.

I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> * Programdesignen
> * Hur du registrerar ditt program med Azure Active Directory (AD Azure)
> * Hur du skapar, publicerar och testar ditt webbprogram

> [!NOTE]
> * Källkoden för den här självstudien finns på [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Time Series Insights [klienten exempelapp](https://insights.timeseries.azure.com/clientsample) är värd för att visa den färdiga appen som används i den här självstudien.

## <a name="prerequisites"></a>Förutsättningar

* Registrera dig för en [kostnadsfritt Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har ett.

* En kostnadsfri version av Visual Studio. Ladda ned den [2017 eller 2019 Community-versionerna](https://www.visualstudio.com/downloads/) att komma igång.

* IIS Express, Web Deploy och Azure Cloud Services verktyg huvudkomponenterna för Visual Studio. Lägga till komponenter genom att ändra Visual Studio-installationen.

## <a name="application-design"></a>Programdesign

Time Series Insights-exemplet SPA utgör grunden för design och kod som används i den här självstudien. Koden använder Time Series Insights JavaScript-klientbiblioteket. Time Series Insights-klientbiblioteket tillhandahåller en abstraktion för API: et huvudkategorier:

- **Omslutning metoderna för att anropa Time Series Insights fråga API: er**: REST API: er som du kan använda för att fråga efter Time Series Insights data med hjälp av JSON-baserade uttryck. Metoderna som är ordnade under TsiClient.server namnområdet för biblioteket.

- **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som du kan använda för att visualisera Time Series Insights-data på en webbsida. Metoderna som är ordnade under TsiClient.ux namnområdet för biblioteket.

Den här självstudien använder också data från exempelprogrammets Time Series Insights-miljö. Mer information om strukturen i Time Series Insights-exempelprogrammet och hur den används av Time Series Insights-klientbiblioteket finns i självstudierna [utforska Azure Time Series Insights JavaScript-klientbiblioteket](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrera ett program med Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish-the-web-application"></a>Bygg och publicera webbappen

1. Skapa en katalog för att lagra din apps projektfiler. Gå sedan till var och en av följande webbadresser. Högerklicka på den **Raw** länken i det övre högra hörnet på sidan och välj sedan **Spara som** att spara filerna i projektkatalogen.

   - [*index.HTML*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML och JavaScript för sidan
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): CSS-formatmall

   > [!NOTE]
   > Beroende på vilken webbläsare kan du behöva ändra filnamnstilläggen .html eller CSS innan du sparar filen.

1. Kontrollera att de nödvändiga komponenterna har installerats i Visual Studio. Kärnkomponenter för verktyg för Visual Studio i IIS Express, Web Deploy och Azure Cloud Services måste installeras.

    [![Visual Studio – ändra installerade komponenter](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Din Visual Studio-upplevelse kan skilja sig något från depicted exemplen beroende på din version och vilka konfigurationsinställningar.

1. Öppna Visual Studio och logga in. Att skapa ett projekt för webbprogrammet på den **filen** menyn och välj **öppna** > **webbplats**.

    [![Visual Studio – skapa en ny lösning](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. I den **öppna webbplatsen** fönstret Välj arbetskatalogen där du sparade HTML och CSS-filer och därefter **öppna**.

   [![Visual Studio - menyn Arkiv med alternativ för öppen och -webbplats](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. I Visual Studio **visa** menyn och välj **Solution Explorer**. Den nya lösningen öppnas. Den innehåller en webbplats-projektet (Globikon), som innehåller HTML och CSS-filer.

   [![Visual Studio – den nya lösningen i Solution Explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Innan du publicerar din app måste du ändra konfigurationsinställningarna i *index.html*.

   1. Ta bort kommentarerna för de tre raderna under kommentaren `"PROD RESOURCE LINKS"` växla beroenden från utveckling till produktion. Kommentera ut de tre raderna under kommentaren `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Vara bör kommenterade dina beroenden som i följande exempel:

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

   1. För att konfigurera appen att använda Azure AD app registrerings-ID, ändra den `clientID` värde som ska användas i den **program-ID** du kopierade i **steg 3** när du [registrerade programmet till Använd Azure AD](#register-the-application-with-azure-ad). Om du har skapat en **URL för utloggning** i Azure AD, ange värdet som den `postLogoutRedirectUri` värde.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Exempel:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. När du är klar att göra ändringar kan du spara *index.html*.

1. Publicera webbappen i Azure-prenumerationen som en Azure App Service.  

   > [!NOTE]
   > Flera alternativ i skärmbilderna som visas i följande steg fylls i automatiskt med data från din Azure-prenumeration. Det kan ta några sekunder för respektive fönster för att läsa in helt.  

   1. Högerklicka på webbplatsen projektnoden i Solution Explorer och välj sedan **publicera Webbapp**.  

      [![Visual Studio – Välj alternativet Publicera Webbappen i Solution Explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Välj **starta** att börja publicera din app.

      [![Visual Studio - fönstret Publicera profil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Välj den prenumeration som du vill använda för att publicera programmet. Välj den **TsiSpaApp** projekt. Välj **OK**.

      [![Visual Studio – publiceringsprofil App Service-fönstret](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Välj **publicera** att distribuera webbprogrammet.

      [![Visual Studio - alternativet Publicera och loggutdata publicera](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. En lyckad publicera loggen visas i Visual Studio **utdata** fönstret. När distributionen är klar, Visual Studio öppnas webbprogrammet i en ny flik i webbläsaren och frågar efter inloggning. Efter inloggningen lyckas ifyllda Time Series Insights-kontroller med data.

## <a name="troubleshoot"></a>Felsöka  

Felkod/-villkor | Beskrivning
---------------------| -----------
*AADSTS50011: Ingen svarsadress har registrerats för appen.* | Azure AD-registrering saknas den **svars-URL** egenskapen. Gå till **inställningar** > **Svarswebbadresser** för din Azure AD-programregistrering. Kontrollera att den **omdirigerings-URI** du hade kan ange i **steg 2** när du [registrerade programmet till att använda Azure AD](#register-the-application-with-azure-ad) finns.
*AADSTS50011: Svaret från den url som anges i begäran matchar inte svars-URL som konfigurerats för programmet: '\<GUID för program-ID >'.* | Den `postLogoutRedirectUri` anges i **steg 6** i [skapa och publicera webbapplikationen](#build-and-publish-the-web-application) måste överensstämma med värdet som anges under **inställningar**  >  **Svarswebbadresser** i din Azure AD-programregistrering. Se till att även ändra värdet för **mål-URL** att använda *https* per **steg 5** i [skapa och publicera webbapplikationen](#build-and-publish-the-web-application).
Webbprogrammet har lästs in, men den har en formatmängden, endast text på inloggningssidan, med en vit bakgrund. | Kontrollera att sökvägarna som beskrivs i **steg 4** i [skapa och publicera webbapplikationen](#build-and-publish-the-web-application) är korrekta. Om webbappen inte kan hitta CSS-filer kommer sidan inte att formateras korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudiekursen skapar flera körningar av Azure-tjänster. Om du inte planerar att slutföra den här självstudieserien, rekommenderar vi att du tar bort alla resurser för att undvika onödiga kostnader.

Azure portal menyn till vänster:

1. Välj **resursgrupper**, och välj sedan den resursgrupp du skapade för Time Series Insights-miljö. Längst ned på sidan Välj **ta bort resursgrupp**, ange namnet på resursgruppen och välj sedan **ta bort**.
1. Välj **resursgrupper**, och välj sedan den resursgrupp som har skapats av lösningsaccelerator för enheten simulering. Längst ned på sidan Välj **ta bort resursgrupp**, ange namnet på resursgruppen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du:

> [!div class="checklist"]
> * Programdesignen
> * Så här registrerar du ditt program med Azure AD
> * Hur du skapar, publicerar och testar ditt webbprogram

Den här självstudien kan integreras med Azure AD och använder identiteten för den användare som är inloggad för att hämta en åtkomsttoken. Läs hur du kommer åt API: T för Time Series Insights med hjälp av en tjänst eller daemon programmets identitet med i den här artikeln:

> [!div class="nextstepaction"]
> [Autentisering och auktorisering för Azure Time Series Insights API](time-series-insights-authentication-and-authorization.md)
