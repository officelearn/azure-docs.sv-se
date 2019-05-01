---
title: 'Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp | Microsoft Docs'
description: Lär dig hur du skapar en enkelsidig webbapp som frågar och återger data från en TSI-miljö.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 18f5c14a9427f4d7e34a802b2bcc0612a51a804a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573290"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Självstudie: Skapa en enkelsidig Azure Time Series Insights-webbapp

Den här självstudien vägleder dig genom processen med att skapa egna enkelsidiga webbprogram åtkomst till Time Series Insights-data. Mer specifikt får du lära dig om:

> [!div class="checklist"]
> * Programdesignen
> * Registrera ditt program med Azure Active Directory (AD)
> * Hur du skapar, publicerar och testar ditt webbprogram

> [!NOTE]
> * Källkoden för den här självstudien finns på [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Time Series Insights [klienten exempelapp](https://insights.timeseries.azure.com/clientsample) är värd för att visa den färdiga appen som används i den här självstudien.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

* Du måste också en kostnadsfri version av Visual Studio. Ladda ned den [2017 eller 2019 Community-versionen](https://www.visualstudio.com/downloads/) att komma igång.

* Du måste också den **IIS Express**, **webbdistribution**, och **Azure Cloud Services viktiga verktyg** komponenter för Visual Studio. Lägger till dem genom att ändra Visual Studio-installationen.

## <a name="application-design-overview"></a>Översikt över programdesignen

Time Series Insights enkelsidiga exempelappen utgör grunden för design och kod som används i den här självstudien. Koden omfattar användning av JavaScript-bibliotek för TSI-klient. TSI-klientbiblioteket tillhandahåller en abstraktion för två huvudkategorier av API:er:

- **Omslutningsmetoder för att anropa fråge-API:er för TSI**: REST-API:er som gör det möjligt att fråga efter TSI-data med hjälp av JSON-baserade uttryck. Metoderna organiseras under `TsiClient.server`-namnområdet för biblioteket.

- **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som används för att visualisera TSI-data på en webbsida. Metoderna organiseras under `TsiClient.ux`-namnområdet för biblioteket.

Den här självstudiekursen kommer också att använda data från det exempelprogrammets TSI-miljö. Mer information om strukturen för TSI-exempelprogrammet och dess användning av TSI-klientbiblioteket finns i kursen [Utforska klient-JavaScript-biblioteket för Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrera ett program med Azure AD

Innan du skapar programmet måste du registrera det med Azure AD. Registreringen ger identitetskonfiguration för ett program så att det kan använda OAuth-stöd för enkel inloggning. OAuth kräver att SPA att använda ”underförstått” tillstånd, vilket ska du uppdatera i programmanifestet. Ett programmanifest är en JSON-representation av programmets identitetskonfiguration.

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerationskonto.  
1. Välj den **Azure Active Directory** resurs i den vänstra rutan, sedan **appregistreringar**, sedan **+ ny programregistrering**.

   [![Azure-portalen Azure AD-programregistrering](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. På den **skapa** fyller du i de obligatoriska parametrarna.

   Parameter|Beskrivning
   ---|---
   **Namn** | Ange ett meningsfullt registreringsnamn.  
   **Programtyp** | Eftersom du sammanställer en SPA-webbapp, lämna den som ”Web app/API”.
   **Inloggnings-URL** | Ange en URL för sidan programmets hem-/inloggningssida. Eftersom programmet kommer att finnas i Azure App Service (senare), måste du använda en URL inom den ”https:\//azurewebsites.net” domän. I det här exemplet baseras namnet på registreringsnamnet.

   När du är klar klickar du på **Skapa** att skapa den nya programregistreringen.

   [![Azure-portalen Azure AD-programregistrering – skapa](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Resursprogram innehåller REST API:er för användning av andra program och också är registrerade med Azure AD. API:er ger detaljerad/skyddad åtkomst till klientprogram, genom att exponera ”omfång”. Eftersom ditt program anropar API:et ”Azure Time Series Insights” API, måste du ange API och omfång för behörigheten som ska beviljas/förfrågas vid körning. Välj **inställningar**, sedan **nödvändiga behörigheter**, sedan **+ Lägg till**.

   [![Azure-portalen Azure AD lägger du till behörigheter](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Från sidan **Lägg till API-åtkomst** klickar du på **1 Välj ett API** och ange TSD-API. På sidan **Välj ett API** anger du ”azure time” i sökfältet. Välj ”Azure Time Series Insights” API i resultatlistan och klicka sedan **Välj**.

   [![Azure-portalen Azure AD lägger du till behörigheter - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Nu kan du ange ett omfång för API:et. Gå tillbaka till sidan **Lägg till API-åtkomst** och klicka på **2 Välj behörigheter**. På sidan **Aktivera åtkomst** markerar du omfånget ”Åtkomst till tjänsten Azure Time Series Insights”. Klicka på **Välj**, som återför dig till den **Lägg till API-åtkomst** sidan där du klickar på **klar**.

   [![Azure-portalen Azure AD lägger du till behörigheter - omfång](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. När du kommer tillbaka till sidan **Nödvändiga behörigheter**, lägg märke till att API:et ”Azure Time Series Insights” nu visas. Du måste också ge tillstånd i förväg till att programmet får åtkomst till API:et och omfånget för alla användare. Klicka på den **bevilja** högst upp och välj **Ja**.

   [![Azure-portalen Azure AD nödvändiga behörigheter - medgivande](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Som tidigare nämnts kan du också behöva uppdatera programmanifestet. Klicka på namnet på programmet i den dynamiska länken om du vill gå tillbaka till sidan **Registrerad app**. Välj **Manifest**, ändra den `oauth2AllowImplicitFlow` egenskap `true`, klicka sedan på **spara**.

   [![Azure-portalen Azure AD update manifest](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Klicka på den dynamiska länken för att gå tillbaka till sidan **Registrerad app** igen och kopiera egenskaperna URL för **Startsidan** och **Program-ID** för programmet. Du använder de här egenskaperna i ett senare steg.

   [![Azure-portalen Azure AD-egenskaper](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Bygg och publicera webbappen

1. Skapa en katalog för att lagra din apps projektfiler. Bläddra till var och en av följande webbadresser, högerklicka på länken ”Raw” i den övre högra delen av sidan och ”spara som” i projektkatalogen.

   > [!NOTE]
   > Beroende på webbläsaren kan behöva du korrigera filnamnstillägget (till HTML eller CSS) innan du sparar filen.

   - [**index.html**](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML och JavaScript för sidan.
   - [**sampleStyles.css**]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): CSS-formatmall.

1. Kontrollera att Visual Studio har komponenter som krävs.

    [![VS - ändra installerade komponenter](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    * Du behöver den **IIS Express**, **webbdistribution**, och **Azure Cloud Services viktiga verktyg** komponenter för Visual Studio.

    > [!NOTE]
    > Visual Studio-upplevelsen kan variera något från depicted exemplen beroende på version och konfigurationsinställningar.

1. Starta och logga in på Visual Studio och skapa ett projekt för webbappen. I menyn **Arkiv** väljer du alternativet **öppna**, **Webbplats**.

    [![VS - Skapa ny lösning](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. På den **öppna webbplatsen** dialogrutan arbetskatalogen där du sparade HTML och CSS-filer och sedan klicka på **öppna**.

   [![VS - öppna webbplatsen för fil](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. I Visual Studio väljer du **Solution Explorer** på **Visa**-menyn. Du bör se din nya lösning som innehåller en webbplats-projektet (Globikon), som innehåller HTML och CSS-filer.

   [![VS - ny lösning för solution explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Innan du publicerar din app, måste du ändra konfigurationsinställningarna i **index.html**.

   a. Växla beroenden från utveckling till produktion av uncommenting de tre raderna under kommentaren `"PROD RESOURCE LINKS"`. Kommentera ut de tre raderna under kommentaren `"DEV RESOURCE LINKS"`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Vara bör kommenterade dina beroenden i enlighet med detta:

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

   b. Konfigurera appen att använda din Azure Active Directory app registrerings-ID. Ändra den `clientID` och `postLogoutRedirectUri` fält som ska användas i **program-ID** och **URL-Adressen** du kopierade i **steg 9** av [registrera den program med Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Exempel:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   c. Spara **index.html** när du är klar att göra dessa ändringar.

1. Nu, publicera webbprogrammet på Azure-prenumerationen som en Azure App Service:  

   > [!NOTE]
   > Flera av fälten o följande dialogrutor fylls med data från din Azure-prenumeration. Därför kan det ta några sekunder för varje dialogruta att fullständigt läsa in innan det går att fortsätta.  

   a. Högerklicka på projektnoden webbplats i **Solution Explorer**, och välj **publicera Webbapp**.  

      [![VS - solution explorer publicera webbapp](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   b. Välj **starta** att börja publicera din app.

      [![VS - publiceringsprofil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   c. Välj den prenumeration som du vill använda för att publicera programmet. Välj den **TsiSpaApp** projekt. Sedan **OK**:

      [![VS - publiceringsprofil - apptjänst](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   d. Klicka på **publicera** att distribuera webbprogrammet.

      [![VS - publicera webbapp – publicera loggutdata](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   e. Du bör se en logg för lyckad publicering i Visual Studios **Utdatafönster**. När distributionen är klar öppnar Visual Studio även webbprogrammet på en webbläsarflik och uppmanar användaren att logga in. Efter inloggningen lyckas ser du alla TSI-kontroller som fyllts med data.

## <a name="troubleshooting"></a>Felsökning  

Felkod/-villkor | Beskrivning
---------------------| -----------
*AADSTS50011: Ingen svarsadress har registrerats för appen.* | Azure AD-registrering saknas den **svars-URL** egenskapen. Gå till sidan **Inställningar** / **Svars-URL: er** för din Azure AD-appregistrering. Kontrollera att den **inloggning** URL som anges i **steg 3** av [registrera programmet med Azure AD](#register-the-application-with-azure-ad) finns.
*AADSTS50011: Svaret från den url som anges i begäran matchar inte svars-URL som konfigurerats för programmet: '\<GUID för program-ID >'.* | `postLogoutRedirectUri` som angavs i steg 4.b av [Bygga och publicera webbappen](#build-and-publish-the-web-application) måste överensstämma med värdet som anges under egenskapen **Inställningar** / **Svars-URL** i Azure AD-appregistrering. Se till att även ändra **mål-URL** att använda `https`, per **steg 5** av [skapa och publicera webbapplikationen](#build-and-publish-the-web-application).
Webbappen har lästs in, men inloggningssidan har endast oformaterad text med en vit bakgrund. | Kontrollera att sökvägarna som beskrivs i **steg 4** av [skapa och publicera webbapplikationen](#build-and-publish-the-web-application) är korrekta. Om webbappen inte kan hitta CSS-filer kommer sidan inte att formateras korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudiekursen skapar flera körningar av Azure-tjänster. Om du inte vill slutföra den här serien av självstudiekurser rekommenderar vi att du tar bort alla resurser för att undvika onödiga kostnader.

Klicka på den vänstra menyn i Azure Portal:

1. Välj **resursgrupper**, välj sedan den resursgrupp du skapade för TSI-miljö. Överst på sidan, klickar du på **Ta bort resursgrupp**, anger namnet på resursgruppen och klickar sedan på **Ta bort**.
1. Välj **resursgrupper**, Välj den resursgrupp som har skapats av lösningsaccelerator för enheten simulering. Överst på sidan, klickar du på **Ta bort resursgrupp**, anger namnet på resursgruppen och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Programdesignen
> * Registrera ditt program med Azure Active Directory (AD)
> * Hur du skapar, publicerar och testar ditt webbprogram

Den här kursen kan integreras med Azure AD, med den inloggade användarens identitet för att få en åtkomsttoken. Information om hur du kommer åt TSI API med hjälp av identiteten för ett program för service-daemon finns i:

> [!div class="nextstepaction"]
> [Autentisering och auktorisering för Azure Time Series Insights API](time-series-insights-authentication-and-authorization.md)