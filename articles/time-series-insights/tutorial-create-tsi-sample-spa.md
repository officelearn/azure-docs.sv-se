---
title: 'Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp | Microsoft Docs'
description: Lär dig hur du skapar ett enkelsidigt program som frågar och återger data från en Azure Time Series Insights-miljö.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 2f25267b95e9ed5f7d5f6e6373fb9e3807927a7f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735353"
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

## <a name="prerequisites"></a>Nödvändiga komponenter

* Registrera dig för en [kostnadsfritt Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har ett.

* En kostnadsfri version av Visual Studio. Ladda ned den [2017 eller 2019 Community-versionerna](https://www.visualstudio.com/downloads/) att komma igång.

* IIS Express, Web Deploy och Azure Cloud Services verktyg huvudkomponenterna för Visual Studio. Lägga till komponenter genom att ändra Visual Studio-installationen.

## <a name="application-design"></a>Programdesign

Time Series Insights-exemplet SPA utgör grunden för design och kod som används i den här självstudien. Koden använder Time Series Insights JavaScript-klientbiblioteket. Time Series Insights-klientbiblioteket tillhandahåller en abstraktion för API: et huvudkategorier:

- **Omslutning metoderna för att anropa Time Series Insights fråga API: er**: REST API: er som du kan använda för att fråga efter Time Series Insights data med hjälp av JSON-baserade uttryck. Metoderna som är ordnade under TsiClient.server namnområdet för biblioteket.

- **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som du kan använda för att visualisera Time Series Insights-data på en webbsida. Metoderna som är ordnade under TsiClient.ux namnområdet för biblioteket.

Den här självstudien använder också data från exempelprogrammets Time Series Insights-miljö. Mer information om strukturen i Time Series Insights-exempelprogrammet och hur den används av Time Series Insights-klientbiblioteket finns i självstudierna [utforska Azure Time Series Insights JavaScript-klientbiblioteket](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrera ett program med Azure AD

Innan du skapar programmet måste du registrera den med Azure AD. Registreringen ger identity-konfigurationen så att programmet kan använda OAuth-stöd för enkel inloggning. OAuth kräver SPA att använda implicita beviljandetyp. Du kan uppdatera auktoriseringen i manifestet. Ett programmanifest är en JSON-representation av programmets identitetskonfiguration.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ditt Azure-prenumeration.  
1. Välj **Azure Active Directory** > **Appregistreringar** > **Ny programregistrering**.

   [![Azure portal – börja med Azure AD-programregistrering](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. I den **skapa** rutan, fyller du i de obligatoriska parametrarna.

   Parameter|Beskrivning
   ---|---
   **Namn** | Ange ett beskrivande registrering namn.  
   **Programtyp** | Lämna som **Web app/API**.
   **Inloggnings-URL** | Ange URL: en för den inloggning (hemsida) av programmet. Eftersom programmet kommer senare att finnas i Azure App Service, måste du använda en URL i https:\//azurewebsites.net domän. I det här exemplet baseras namnet på registreringsnamnet.

   Välj **skapa** att skapa ny programregistrering.

   [![Azure portal – sidan Skapa alternativ i fönstret Azure AD application registrering](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Resursprogram ger REST API: er som kan använda för andra program. API: erna också är registrerade med Azure AD. API: er ger detaljerade och säker åtkomst till program genom att exponera *scope*. Eftersom programmet anropar Azure Time Series Insights API, måste du ange API och omfång. Tillstånd beviljas för API och scope vid körning. Välj **inställningar** > **nödvändiga behörigheter** > **Lägg till**.

   [![Azure portal – Lägg till alternativet för att lägga till Azure AD-behörigheter](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. I den **Lägg till API-åtkomst** väljer **1 Välj en API** att ange Azure Time Series Insights API. I den **Välj en API** fönstret i sökrutan anger **azure tid**. Välj **Azure Time Series Insights** i resultatlistan. Välj **Välj**.

   [![Azure portal – sökfunktionen för att lägga till Azure AD-behörigheter](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Markera ett omfång för API i den **Lägg till API-åtkomst** väljer **2 Select-behörigheter**. I den **Aktivera åtkomst** väljer den **åtkomst Azure Time Series Insights-tjänsten** omfång. Välj **Välj**. Du kommer tillbaka till den **Lägg till API-åtkomst** fönstret. Välj **Done** (Klar).

   [![Azure portal – ange en omfattning för att lägga till Azure AD-behörigheter](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. I den **nödvändiga behörigheter** fönstret Azure Time Series Insights API visas nu. Du måste också ange före medgivande behörighet för programmet åtkomst till API och omfång för alla användare. Välj **bevilja**, och välj sedan **Ja**.

   [![Behörigheter som krävs för Azure portal – The bevilja behörigheter alternativet för att lägga till Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Som vi beskrivs tidigare kan uppdatera du även applikationsmanifestet. I menyn högst upp i fönstret (”dynamiska”) till vågrät väljer du namnet på programmet för att återgå till den **registrerad app** fönstret. Välj **Manifest**, ändra den `oauth2AllowImplicitFlow` egenskap `true`, och välj sedan **spara**.

   [![Azure portal – uppdatering med Azure AD-manifest](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. I sökvägen, väljer du namnet på programmet för att återgå till den **registrerad app** fönstret. Kopiera värdena för **startsida** och **program-ID** för ditt program. Du kan använda de här egenskaperna senare under kursen.

   [![Azure portal – Kopiera URL-Adressen och program-ID-värden för ditt program](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Bygg och publicera webbappen

1. Skapa en katalog för att lagra din apps projektfiler. Gå sedan till var och en av följande webbadresser. Högerklicka på den **Raw** länken i det övre högra hörnet på sidan och välj sedan **Spara som** att spara filerna i projektkatalogen.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML och JavaScript för sidan
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

   1. För att konfigurera appen att använda Azure AD app registrerings-ID, ändra den `clientID` och `postLogoutRedirectUri` värden som ska användas av värdena för **program-ID** och **startsida** som du kopierade i steg 9 i [ Registrera programmet med Azure AD](#register-the-application-with-azure-ad).

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
*AADSTS50011: Ingen svarsadress har registrerats för appen.* | Azure AD-registrering saknas den **svars-URL** egenskapen. Gå till **inställningar** > **Svarswebbadresser** för din Azure AD-programregistrering. Kontrollera att den **inloggning** URL som anges i steg 3 i [registrera programmet med Azure AD](#register-the-application-with-azure-ad) finns.
*AADSTS50011: Svaret från den url som anges i begäran matchar inte svars-URL som konfigurerats för programmet: '\<GUID för program-ID >'.* | Den `postLogoutRedirectUri` angav i steg 6 i [skapa och publicera webbapplikationen](#build-and-publish-the-web-application) måste överensstämma med värdet som anges under **inställningar** > **Svarswebbadresser** i registrering för din Azure AD-program. Se till att även ändra värdet för **mål-URL** att använda *https* per steg 5 i [skapa och publicera webbapplikationen](#build-and-publish-the-web-application).
Webbprogrammet har lästs in, men den har en formatmängden, endast text på inloggningssidan, med en vit bakgrund. | Kontrollera att de sökvägar som beskrivs i steg 4 [skapa och publicera webbapplikationen](#build-and-publish-the-web-application) är korrekta. Om webbappen inte kan hitta CSS-filer kommer sidan inte att formateras korrekt.

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
