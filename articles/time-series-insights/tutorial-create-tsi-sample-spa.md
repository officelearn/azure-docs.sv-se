---
title: 'Självstudier: Skapa en enkelsidig Azure Time Series Insights-webbapp | Microsoft Docs'
description: Lär dig hur du skapar en enkelsidig webbapp som frågar och återger data från en TSI-miljö.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: fe8b6113646589e30ff839c8bd47968138d98b03
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521445"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Självstudie: Skapa en enkelsidig Azure Time Series Insights-webbapp

Den här självstudien vägleder dig genom processen att skapa din egen ensidiga webbapp (SPA) för att komma åt TSD-data som bygger på exempelprogrammet [Time Series Insights (Insikter från tidsserier;TSI) ](https://insights.timeseries.azure.com/clientsample). I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> * Programdesignen
> * Registrera ditt program med Azure Active Directory (AD)
> * Hur du skapar, publicerar och testar ditt webbprogram 

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/). 

Installera Visual Studio, om du inte redan gjort det. För den här kursen kan du [ladda ned och installera den kostnadsfria Community-versionen eller en kostnadsfri utvärderingsversion](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Översikt över programdesignen

Som tidigare nämnts utgör TSI-exempelprogrammet grunden för den design och kod som används i den här självstudiekursen. Koden omfattar användning av JavaScript-bibliotek för TSI-klient. TSI-klientbiblioteket tillhandahåller en abstraktion för två huvudkategorier av API:er:

- **Omslutningsmetoder för att anropa fråge-API:er för TSI**: REST-API:er som gör det möjligt att fråga efter TSI-data med hjälp av JSON-baserade uttryck. Metoderna organiseras under `TsiClient.server`-namnområdet för biblioteket.
- **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som används för att visualisera TSI-data på en webbsida. Metoderna organiseras under `TsiClient.ux`-namnområdet för biblioteket.

Den här självstudiekursen kommer också att använda data från det exempelprogrammets TSI-miljö. Mer information om strukturen för TSI-exempelprogrammet och dess användning av TSI-klientbiblioteket finns i kursen [Utforska klient-JavaScript-biblioteket för Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrera ett program med Azure AD 

Innan du skapar programmet måste du registrera det med Azure AD. Registreringen ger identitetskonfiguration för ett program så att det kan använda OAuth-stöd för enkel inloggning. OAuth kräver att SPA att använda ”underförstått” tillstånd, vilket ska du uppdatera i programmanifestet. Ett programmanifest är en JSON-representation av programmets identitetskonfiguration. 

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerationskonto.  
1. Välj resursen **Azure Active Directory** i den vänstra rutan, sedan **App-registreringar**, sedan **+ Ny appregistrering**:  
   
   ![Azure AD-appregistrering i Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. På sidan **Skapa** fyller du i de nödvändiga parametrarna:
   
   Parameter|Beskrivning
   ---|---
   **Namn** | Ange ett meningsfullt registreringsnamn.  
   **Programtyp** | Eftersom du sammanställer en SPA-webbapp, lämna den som ”Web app/API”.
   **Inloggnings-URL** | Ange en URL för sidan programmets hem-/inloggningssida. Eftersom programmet kommer att finnas i Azure App Service (senare), måste du använda en URL inom den ”https:\//azurewebsites.net” domän. I det här exemplet baseras namnet på registreringsnamnet.

   När du är klar klickar du på **Skapa** att skapa den nya programregistreringen.

   ![Azure AD-appregistrering i Azure Portal - skapa](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. Resursprogram innehåller REST API:er för användning av andra program och också är registrerade med Azure AD. API:er ger detaljerad/skyddad åtkomst till klientprogram, genom att exponera ”omfång”. Eftersom ditt program anropar API:et ”Azure Time Series Insights” API, måste du ange API och omfång för behörigheten som ska beviljas/förfrågas vid körning. Välj **Inställningar**, sedan **Nödvändiga behörigheter**, sedan **+ Lägg till**:

   ![Azure Portal Azure AD lägga till behörigheter](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. Från sidan **Lägg till API-åtkomst** klickar du på **1 Välj ett API** och ange TSD-API. På sidan **Välj ett API** anger du ”azure time” i sökfältet. Välj sedan API:et ”Azure Time Series Insights” i resultatlistan och klicka sedan på **Välj**: 

   ![Azure-portalen Azure AD lägga till behörigheter - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. Nu kan du ange ett omfång för API:et. Gå tillbaka till sidan **Lägg till API-åtkomst** och klicka på **2 Välj behörigheter**. På sidan **Aktivera åtkomst** markerar du omfånget ”Åtkomst till tjänsten Azure Time Series Insights”. Klickar på **Välj**, vilket återför dig till sidan **Lägg till API-åtkomst** där du klickar på **Klar**:

   ![Azure-portalen Azure AD lägga till behörigheter - omfång](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. När du kommer tillbaka till sidan **Nödvändiga behörigheter**, lägg märke till att API:et ”Azure Time Series Insights” nu visas. Du måste också ge tillstånd i förväg till att programmet får åtkomst till API:et och omfånget för alla användare. Klicka på knappen **Bevilja behörigheter** överst och välj **Ja**:

   ![Azure-portalen Azure AD nödvändiga behörigheter - medgivande](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Som tidigare nämnts kan du också behöva uppdatera programmanifestet. Klicka på namnet på programmet i den dynamiska länken om du vill gå tillbaka till sidan **Registrerad app**. Välj **Manifest**, ändra egenskapen `oauth2AllowImplicitFlow` till `true` och klicka på **Spara**:

   ![Uppdatera manifest för Azure AD i Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Klicka på den dynamiska länken för att gå tillbaka till sidan **Registrerad app** igen och kopiera egenskaperna URL för **Startsidan** och **Program-ID** för programmet. Du kommer att använda dessa egenskaper i ett senare steg:

   ![Egenskaper för Azure AD med Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Bygg och publicera webbappen

1. Skapa en katalog för att lagra din apps projektfiler. Bläddra till var och en av följande webbadresser, högerklicka på länken ”Raw” i den övre högra delen av sidan och ”Spara som” i projektkatalogen:

   > [!NOTE]
   > Beroende på webbläsaren kan behöva du korrigera filnamnstillägget (till HTML eller CSS) innan du sparar filen.

   - **index.HTML** HTML och JavaScript för sidan https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** CSS-formatmall: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Starta och logga in på Visual Studio och skapa ett projekt för webbappen. I menyn **Arkiv** väljer du alternativet **öppna**, **Webbplats**. I dialogrutan **Öppna webbplats** väljer du arbetskatalogen där du sparade HTML- och CSS-filerna och klickar sedan på **Öppna**:

   ![VS - Arkiv öppna webbplats](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. I Visual Studio väljer du **Solution Explorer** på **Visa**-menyn. Du bör se den nya lösningen som innehåller ett webbplats-projekt (globikon) som innehåller HTML- och CSS-filer:

   ![VS - solution explorer ny lösning](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Innan du kan publicera programmet, måste du uppdatera delar av JavaScript-koden i **index.html**: 

   a. Först, ändra sökvägarna för JavaScript och formatbladfilreferenser i elementet `<head>`. Öppna filen **index.html** i Visual Studio-lösningeb och sök efter följande rader med JavaScript-kod. Ta bort kommentarerna från de tre raderna under ”PROD RESURSLÄNKAR” och kommentera ut de tre raderna under ”DEV RESURSLÄNKAR”:
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Din ändrade kod bör se ut som i följande exempel:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Nu ska du ändra logiken för åtkomsttoken för att använda din nya appregistrering för Azure AD. Ändra variablerna `clientID` och `postLogoutRedirectUri` med hjälp av det program-ID och den URL du kopierade i steg 9 i avsnittet [Registrera appen med Azure AD](#register-the-application-with-azure-ad):

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Din ändrade kod bör se ut som i följande exempel:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Spara **index.html** när du har redigerat klart.

1. Nu kan du publicera webbappen på din Azure-prenumeration som Azure App Service:  

   > [!NOTE]
   > Flera av fälten o följande dialogrutor fylls med data från din Azure-prenumeration. Därför kan det ta några sekunder för varje dialogruta att fullständigt läsa in innan det går att fortsätta.  

   a. I **Solution Explorer** högerklickar du på projektnoden för webbplatsen och väljer **Publicera Webbapp**:  

      ![VS - solution explorer publicera webbapp](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Välj **Microsoft Azure App Service** för att skapa ett publiceringsmål:  

      ![Visa - publiceringsprofil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Välj den prenumeration du vill använda för att publicera programmet och klicka på ”Nytt”: 

      ![VS - publiceringsprofil - apptjänst](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Ge dialogrutan **Skapa App Service** några sekunder för att läsa in alla fält och sedan ändra följande fält:
   
      Fält | Beskrivning
      ---|---
      **Appnamn** | Ändra till Azure AD-programnamnet du använde i steg 3 i [Registrera appen med Azure AD](#register-the-application-with-azure-ad). 
      **Resursgrupp** | Använd knappen **Nytt...**  och ändra så att den matchar fältet **Appnamn**.
      **App Service-plan** | Använd knappen **Nytt...**  och ändra så att den matchar fältet **Appnamn**.

      Klicka på **Skapa** när du är klar. Knappen **Exportera** längst ned till vänster ersätts med ”Distribuerar:” i några sekunder medan Azure-resurser skapas. När resurserna skapas kommer du tillbaka till föregående dialogruta. 

      ![VS - publiceringsprofil - lägg till ny apptjänst](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. När du kommer tillbaka till dialogrutan **Publicera** bör **Publiceringsmetod** vara inställd på ”Webbdistribution”. Ändra **mål-URL:en** så att du använder `https` i stället för `http`, så att den matchar appregistreringen i Azure AD. Klicka sedan på ”Publicera” för att distribuera webbappen:  

      ![VS - publicera webbapp – publicera app service](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Du bör se en logg för lyckad publicering i Visual Studios **Utdatafönster**. När distributionen är klar öppnar Visual Studio även webbprogrammet på en webbläsarflik och uppmanar användaren att logga in. Efter lyckad inloggning visas alla TSI-kontroller som fylls i med data:  

      [![VS - publicera webbapp – publicera loggutdata](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![TSI SPA app - inloggning](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>Felsökning  

Felkod/-villkor | Beskrivning
---------------------| -----------
*AADSTS50011: Ingen svarsadress har registrerats för appen.* | Azure AD-registreringen saknar egenskapen ”Svars-URL”. Gå till sidan **Inställningar** / **Svars-URL: er** för din Azure AD-appregistrering. Kontrollera att den **inloggning**-URL som anges i steg 3 av [Registrera apen med Azure AD](#register-the-application-with-azure-ad) finns. 
*AADSTS50011: Svaret från den url som anges i begäran matchar inte svars-URL som konfigurerats för programmet: '\<GUID för program-ID >'.* | `postLogoutRedirectUri` som angavs i steg 4.b av [Bygga och publicera webbappen](#build-and-publish-the-web-application) måste överensstämma med värdet som anges under egenskapen **Inställningar** / **Svars-URL** i Azure AD-appregistrering. Se till att även ändra din **mål-URL** så att du använder `https`, per steg 5.e i [Bygg och publicera webbappen](#build-and-publish-the-web-application).
Webbappen har lästs in, men inloggningssidan har endast oformaterad text med en vit bakgrund. | Kontrollera att de sökvägar som beskrivs i steg 4.a i [Bygg och publicera webbappen](#build-and-publish-the-web-application) är korrekta. Om webbappen inte kan hitta CSS-filer kommer sidan inte att formateras korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudiekursen skapar flera körningar av Azure-tjänster. Om du inte vill slutföra den här serien av självstudiekurser rekommenderar vi att du tar bort alla resurser för att undvika onödiga kostnader. 

Klicka på den vänstra menyn i Azure Portal:

1. Klicka på ikonen **Resursgrupper** välj sedan den resursgrupp som du skapade för TSI-miljön. Överst på sidan, klickar du på **Ta bort resursgrupp**, anger namnet på resursgruppen och klickar sedan på **Ta bort**. 
1. Klicka på ikonen **Resursgrupper** välj sedan den resursgrupp som har skapats av enhetssimuleringens lösningsaccelerator. Överst på sidan, klickar du på **Ta bort resursgrupp**, anger namnet på resursgruppen och klickar sedan på **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Programdesignen
> * Registrera ditt program med Azure Active Directory (AD)
> * Hur du skapar, publicerar och testar ditt webbprogram 

Den här kursen kan integreras med Azure AD, med den inloggade användarens identitet för att få en åtkomsttoken. Information om hur du kommer åt TSI API med hjälp av identiteten för ett program för service-daemon finns i:

> [!div class="nextstepaction"]
> [Autentisering och auktorisering för Azure Time Series Insights API](time-series-insights-authentication-and-authorization.md)
