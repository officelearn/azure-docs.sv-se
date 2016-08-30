<properties
    pageTitle="Hantera ditt första API i Azure API Management | Microsoft Azure"
    description="Lär dig hur du skapar API:er, lägger till åtgärder och kommer igång med API Management."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/25/2016"
    ms.author="sdanie"/>

# Hantera ditt första API i Azure API Management

## <a name="overview"> </a>Översikt

Den här guiden hjälper dig att snabbt komma igång med Azure API Management och att göra ditt första API-anrop.

## <a name="concepts"> </a>Vad är Azure API Management?

Med Azure API Management kan du välja valfri serverdel och köra ett fullvärdigt API-program baserat på den.

Här följer exempel på några vanliga scenarier:

* **Skydda den mobila infrastrukturen** genom att hantera åtkomsten med API-nycklar, förhindra DOS-attacker med hjälp av begränsningar och använda avancerade säkerhetsprinciper som JWT-tokenvalidering.
* **Aktivera ISV-partnerekosystem** genom att erbjuda snabb partnerintegrering via utvecklarportalen och genom att bygga en API-fasad som är åtskild från interna implementeringar som inte är redo för partneranvändning än.
* **Kör ett internt API-program** genom att erbjuda en central plats för organisationen där man kan kommunicera kring tillgänglighet och de senaste ändringarna i API:er samt hantera åtkomsten baserat på organisationskonton – allt via en säker kanal mellan API-gatewayen och serverdelen.


Systemet består av följande komponenter:

* **API-gatewayen** är slutpunkten som:
  * Accepterar API-anrop och dirigerar dem till serverdelen.
  * Verifierar API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter.
  * Tillämpar användningskvoter och hastighetsbegränsningar.
  * Transformerar ditt API direkt utan kodändringar.
  * Cachelagrar backend-svar om detta konfigurerats.
  * Loggar metadata i anrop för analysändamål.

* **Utgivarportalen** är det administrativa gränssnittet där du konfigurerar ditt API-program. Använd portalen om du vill:
    * Definiera eller importera API-schemat.
    * Paketera API:er till produkter.
    * Konfigurera principer som kvoter eller transformationer i API:erna.
    * Få insikter från analyser.
    * Hantera användare.

* **Utvecklarportalen** är en fundamental webbportal för utvecklare, där de kan:
    * Få tillgång till API-dokumentation.
    * Testa ett API via den interaktiva konsolen.
    * Skapa ett konto och börja prenumerera på API-nycklar.
    * Komma åt analyser om deras egen användning.


## <a name="create-service-instance"> </a>Skapa en API Management-instans

>[AZURE.NOTE] Du behöver ett Azure-konto för att slutföra den här självstudien. Om du inte har något konto kan du skapa ett kostnadsfritt konto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure][].

Det första steget när du arbetar med API Management är att skapa en tjänstinstans. Logga in på [Klassisk Azure-portal][] och klicka på **Nytt**, **Apptjänster**, **API Management**, **Skapa**.

![Ny API Management-instans][api-management-create-instance-menu]

För **URL** anger du ett unikt underdomännamn  som ska användas för tjänst-URL:en.

Välj önskad **prenumeration** och **region** för din tjänstinstans. När du har gjort dina val klickar du på knappen **Nästa**.

![Ny API Management-tjänst][api-management-create-instance-step1]

Ange **Contoso Ltd.** som **organisationsnamn** och ange din e-postadress i fältet **Administratörens e-postadress**.

>[AZURE.NOTE] Den här e-postadressen används för meddelanden från API Management-systemet. Mer information finns i [Konfigurera meddelanden och e-postmallar i Azure API Management][].

![Ny API Management-tjänst][api-management-create-instance-step2]

API Management-tjänstinstanser är tillgängliga på tre nivåer: Developer, Standard och Premium. Som standard skapas nya API Management-tjänstinstanser på Developer-nivån. Om du vill välja Standard- eller Premium-nivån markerar du kryssrutan **Avancerade inställningar** och väljer önskad nivå på skärmen som visas.

>[AZURE.NOTE] Developer-nivån är avsedd för utveckling, testning och pilotprojekt av API-program där hög tillgänglighet inte är något problem. På Standard- och Premium-nivåerna kan du skala det reserverade antalet enheter och hantera mer trafik. API Management-tjänsten har mest processorkraft och prestanda på Standard- och Premium-nivåerna. Du kan genomföra den här självstudiekursen med valfri nivå. Mer information om API Management-nivåer finns i avsnittet om [API Management-priser][].

Markera kryssrutan om du vill skapa en tjänstinstans.

![Ny API Management-tjänst][api-management-instance-created]

När tjänstinstansen har skapats är nästa steg att skapa eller importera ett API.

## <a name="create-api"> </a>Importera ett API

Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster.

API:er kan skapas (och åtgärder kan läggas till) manuellt eller importeras. I den här självstudien ska vi importera API:et för ett exempel på en webbaserad kalkylatortjänst som tillhandahålls av Microsoft och som finns i Azure.

>[AZURE.NOTE] Anvisningar för hur du skapar ett API och lägger till åtgärder manuellt finns i [Skapa API:er](api-management-howto-create-apis.md) och [Lägga till åtgärder till ett API](api-management-howto-add-operations.md).

API:er konfigureras från utgivarportalen, som du kommer åt via den klassiska Azure-portalen. Du kommer åt utgivarportalen genom att klicka på **Hantera** på den klassiska Azure-portalen för API Management-tjänsten.

![Utgivarportalen][api-management-management-console]

Du importerar kalkylator-API:et genom att klicka på **API:er** på **API Management**-menyn till vänster och sedan på **Importera API**.

![Knappen Importera API][api-management-import-api]

Konfigurera kalkylator-API:et genom att följa stegen nedan.

1. Klicka på **Från URL**, ange **http://calcapi.cloudapp.net/calcapi.json** i textrutan **URL till specifikationsdokument** och klicka på alternativknappen för **Swagger**.
2. Skriv **calc** i textrutan **URL-suffix för webb-API**.
3. Klicka i rutan **Produkter (valfritt)** och välj **Starter**.
4. Klicka på **Spara** för att importera API:et.

![Lägga till ett nytt API][api-management-import-new-api]

>[AZURE.NOTE] **API Management** stöder för närvarande både 1.2- och 2.0-versionen av Swagger-dokument för importändamål. Egenskaperna `host`, `basePath` och `schemes` är valfria enligt [Swagger 2.0-specifikationen](http://swagger.io/specification), men ditt Swagger 2.0-dokument **MÅSTE** innehålla dessa egenskaper för att importeras. 

När du har importerat API:et visas sammanfattningssidan för API:et på utgivarportalen.

![API-sammanfattning][api-management-imported-api-summary]

API-avsnittet innehåller flera flikar. Fliken **Sammanfattning** innehåller grundläggande mätvärden och information om API:et. Fliken [Inställningar](api-management-howto-create-apis.md#configure-api-settings) används för att visa och redigera konfigurationen för ett API. Fliken [Åtgärder](api-management-howto-add-operations.md) används för att hantera API-åtgärderna. Fliken **Säkerhet** kan användas för att konfigurera gateway-autentisering för backend-servern med grundläggande autentisering eller [ömsesidig certifikatautentisering](api-management-howto-mutual-certificates.md), och för att konfigurera [användarauktorisering med hjälp av OAuth 2.0](api-management-howto-oauth2.md).  Fliken **Problem** används för att visa problem som har rapporterats av utvecklare som använder dina API:er. Fliken **Produkter** används för att konfigurera produkterna som innehåller API:et.

Som standard medföljer två exempelprodukter varje API Management-instans:

-   **Starter**
-   **Unlimited**

I den här självstudien lades Basic Calculator-API:et till i Starter-produkten när API:et importerades.

Innan utvecklare kan göra anrop till ett API måste de prenumerera på en produkt som ger dem åtkomst till API:et. Utvecklare kan prenumerera på produkter via utvecklarportalen, eller så kan en administratör registrera utvecklare för produktprenumerationer från utgivarportalen. Du är administratör eftersom du skapade API Management-instansen i föregående steg i den här självstudiekursen, och som administratör prenumererar du redan på alla produkter som standard.

## <a name="call-operation"> </a>Anropa en åtgärd från utvecklarportalen

Du kan anropa åtgärder direkt från utvecklarportalen, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API. I det här steget i självstudiekursen ska du anropa åtgärden **Lägg till två heltal** i Basic Calculator-API:et. Klicka på **Utvecklarportal** på menyn längst upp till höger på utgivarportalen.

![Utvecklarportalen][api-management-developer-portal-menu]

Klicka på **API:er** på den översta menyn och klicka sedan på **Basic Calculator** för att visa de tillgängliga åtgärderna.

![Utvecklarportalen][api-management-developer-portal-calc-api]

Observera exempelbeskrivningarna och exempelparametrarna som importerades tillsammans med API:et och åtgärderna. Beskrivningarna fungerar som en referens för utvecklare som ska använda åtgärden. Dessa beskrivningar kan även läggas till om åtgärder läggs till manuellt.

Anropa åtgärden **Lägg till två heltal** genom att klicka på **Prova**.

![Prova][api-management-developer-portal-calc-api-console]

Ange egna värden för parametrarna eller behåll standardvärdena och klicka sedan på **Skicka**.

![HTTP Get][api-management-invoke-get]

När en åtgärd har anropats visas **svarsstatus**,  **svarshuvuden** och eventuellt **svarsinnehåll** på utvecklarportalen.

![Svar][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Visa analys

Om du vill visa analysinformation för Basic Calculator går du tillbaka till utgivarportalen genom att välja **Hantera** på menyn längst upp till höger på utvecklarportalen.

![Hantera][api-management-manage-menu]

Standardvyn för utgivarportalen är **Instrumentpanel**, som innehåller en översikt över API Management-instansen.

![Instrumentpanel][api-management-dashboard]

Hovra med musen över diagrammet för **Basic Calculator** så ser du de specifika mätvärdena relaterade till användningen av API:et under en viss tidsperiod.

>[AZURE.NOTE] Om du inte ser några rader i diagrammet växlar du tillbaka till utvecklarportalen gör några anrop till API:et, väntar en stund och går sedan tillbaka till instrumentpanelen.

Klicka på **Visa detaljer** om du vill visa sammanfattningssidan för API:et, inklusive en mer omfattande version av mätvärdena som visas.

![Analys][api-management-mouse-over]

![Sammanfattning][api-management-api-summary-metrics]

Om du vill visa detaljerade mätvärden och rapporter klickar du på **Analys** på **API Management**-menyn till vänster.

![Översikt][api-management-analytics-overview]

Avsnittet **Analys** innehåller följande fyra flikar:

-   **Översikt** innehåller mätvärden om användning och hälsotillstånd, samt de vanligaste utvecklarna, produkterna, API:erna och åtgärderna.
-   **Användning** innehåller detaljerad information om API-anrop och bandbredd, inklusive en geografisk representation.
-   **Hälsa** fokuserar på statuskoder, lyckade cachelagringsåtgärder, svarstider och API- och tjänstsvarstider.
-   **Aktivitet** innehåller rapporter som visar information om den specifika aktiviteten efter utvecklare, produkt, API och åtgärd.

## <a name="next-steps"> </a>Nästa steg

-   Titta gärna igenom avsnitten i självstudiekursen [Komma igång med avancerad API-konfiguration][].

[kostnadsfria utvärderingsversionen av Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Skapa en API Management-instans]: #create-service-instance
[Skapa ett API]: #create-api
[Lägga till en åtgärd]: #add-operation
[Lägga till det nya API:et till en produkt]: #add-api-to-product
[Prenumerera på produkten som innehåller API:et]: #subscribe
[Anropa en åtgärd från utvecklarportalen]: #call-operation
[Visa analys]: #view-analytics
[Nästa steg]: #next-steps


[Hantera utvecklarkonton i Azure API Management]: api-management-howto-create-or-invite-developers.md
[Konfigurera API-inställningar]: api-management-howto-create-apis.md#configure-api-settings
[Konfigurera meddelanden och e-postmallar i Azure API Management]: api-management-howto-configure-notifications.md
[Svar]: api-management-howto-add-operations.md#responses
[Skapa och publicera en produkt]: api-management-howto-add-products.md
[Komma igång med avancerad API-konfiguration]: api-management-get-started-advanced.md
[API Management-priser]: http://azure.microsoft.com/pricing/details/api-management/

[Klassisk Azure-portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png



<!--HONumber=jun16_HO2-->


