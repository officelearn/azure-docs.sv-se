---
title: Anpassa Azure Active Directory B2C användar gränssnitt (UI) dynamiskt genom att använda anpassade principer | Microsoft Docs
description: Stöd för flera anpassnings upplevelser med HTML5/CSS-innehåll som ändras dynamiskt vid körning.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 43c0da3ca8fa4b2f74d48b0e202cc56bc8b9406c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227211"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurera användar gränssnittet med dynamiskt innehåll med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Genom att använda Azure Active Directory B2C (Azure AD B2C) anpassade principer kan du skicka en parameter i en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln fokuserar på hur du anpassar Azure AD B2C användar gränssnitt med *dynamiskt innehåll* med hjälp av anpassade principer. För att komma igång, se [UI-anpassning i en anpassad princip](active-directory-b2c-ui-customization-custom.md).

>[!NOTE]
>Azure AD B2C artikeln, [Konfigurera UI-anpassning i en anpassad princip](active-directory-b2c-ui-customization-custom.md), diskuterar följande grundläggande:
> * Anpassnings funktionen för sid användar gränssnitt (UI).
> * Viktiga verktyg för att testa anpassnings funktionen för sid gränssnittet med hjälp av vårt exempel innehåll.
> * Huvud GRÄNSSNITTs elementen för varje typ av sida.
> * Metod tips för att använda den här funktionen.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Lägg till en länk till HTML5/CSS-mallar till din användar resa

I en anpassad princip definierar en innehålls definition den HTML5-sid-URI som används för ett angivet användar gränssnitts steg (till exempel inloggnings-eller registrerings sidor). Bas principen definierar standard utseendet och känslan genom att peka på en URI med HTML5-filer (i CSS). I tilläggs principen kan du ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen. Innehålls definitioner innehåller URL: er till externt innehåll som definieras av hantverkande HTML5/CSS-filer, efter behov.

Avsnittet innehåller en serie med `ContentDefinition` XML-element. `ContentDefinitions` Attributet ID för `ContentDefinition` elementet anger vilken typ av sida som relaterar till innehålls definitionen. Det innebär att elementet definierar den kontext som en anpassad HTML5/CSS-mall kommer att använda. I följande tabell beskrivs de innehålls Definitions-ID: n som identifieras av IEF-motorn och vilka sidtyper som är relaterade till dem.

| ID för innehålls definition | Standard HTML5-mall| Beskrivning |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. |
| *API. idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sidan Val av identitets leverantör**. Den här sidan listar identitets leverantörer som användarna kan välja bland under inloggningen. Alternativen är vanligt vis företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Val av identitets leverantör för registrering**. Den här sidan listar identitets leverantörer som användarna kan välja bland under registreringen. Alternativen är antingen företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sidan glömt lösen ord**. Den här sidan innehåller ett formulär som användarna måste utföra för att initiera en lösen ords återställning.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Inloggnings sida för lokalt konto**. Den här sidan innehåller ett formulär för att logga in med ett lokalt konto som baseras på en e-postadress eller ett användar namn. Formuläret kan innehålla text rutorna text rutor och lösen ord. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registrerings sidan för lokalt konto**. Den här sidan innehåller ett formulär för att registrera ett lokalt konto som baseras på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, t. ex. en text inmatnings ruta, en ruta för lösen ords inmatning, en alternativ knapp, en listruta med flera val och kryss rutor med flera val. |
| *API. phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan Multi-Factor Authentication**. På den här sidan kan användarna verifiera sina telefonnummer (med hjälp av text eller röst) under registreringen eller inloggningen. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registrerings sida för socialt konto**. Den här sidan innehåller ett formulär som användarna måste utföra när de registrerar sig genom att använda ett befintligt konto från en social identitetsprovider. Den här sidan påminner om den föregående registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan profil uppdatering**. Den här sidan innehåller ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. |
| *api.signuporsignin* | [Unified. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrerings-eller inloggnings sida**. Den här sidan hanterar användarens registrerings-och inloggnings process. Användare kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton.  |

## <a name="serving-dynamic-content"></a>Betjäna dynamiskt innehåll
I artikeln [Konfigurera UI-anpassning i en anpassad princip](active-directory-b2c-ui-customization-custom.md) , laddar du upp HTML5-filer till Azure Blob Storage. Dessa HTML5-filer är statiska och återger samma HTML-innehåll för varje begäran.

I den här artikeln använder du en ASP.NET-webbapp som kan acceptera parametrar för frågesträng och svara på detta.

I den här genom gången ska du:
* Skapa ett ASP.NET Core webb program som är värd för HTML5-mallarna.
* Lägg till en anpassad HTML5-mall, _Unified. cshtml_.
* Publicera din webbapp till Azure App Service.
* Ange resurs delning mellan ursprung (CORS) för din webbapp.
* `LoadUri` Åsidosätt elementen så att de pekar på din HTML5-fil.

## <a name="step-1-create-an-aspnet-web-app"></a>Steg 1: Skapa en ASP.NET-webbapp

1. Skapa ett projekt i Visual Studio genom att välja **Arkiv** > **nytt** > **projekt**.

2. I fönstret **nytt projekt**  > väljer du **Visual C#**  **Web** > **ASP.net Core Web Application (.net Core)** .

3. Ge programmet namnet (till exempel *contoso. AADB2C. UI*) och välj sedan **OK**.

    ![Skapa nytt Visual Studio-projekt](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Välj mallen för **webb program** .

5. Ange autentiseringen till **Ingen autentisering**.

    ![Välj mall för webb program](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Klicka på **OK** för att skapa projektet.

## <a name="step-2-create-mvc-view"></a>Steg 2: Skapa MVC-vy
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Steg 2.1: Ladda ned den inbyggda HTML5-mallen B2C
Din anpassade HTML5-mall baseras på Azure AD B2C inbyggd HTML5-mall. Du kan hämta den [enhetliga HTML-filen](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) eller hämta mallen från [Start paket](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Du använder den här HTML5-filen för att skapa en enhetlig registrerings-eller inloggnings sida.

### <a name="step-22-add-the-mvc-view"></a>Steg 2.2: Lägga till MVC-vyn
1. Högerklicka på mappen vyer/hem och **Lägg sedan till** > ett**nytt objekt**.

    ![Meny alternativet Lägg till nytt objekt i Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. I fönstret **Lägg till nytt objekt – contoso. AADB2C. UI** väljer du **Webb > ASP.net**.

3. Välj **sidan MVC-vy**.

4. Ändra namnet till **Unified. cshtml**i rutan **namn** .

5. Välj **Lägg till**.

    ![Dialog rutan Lägg till nytt objekt i Visual Studio med MVC View-sidan markerad](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Om den *enhetliga. cshtml* -filen inte redan är öppen, dubbelklickar du på filen för att öppna den och rensar sedan fil innehållet.

7. I den här genom gången tar vi bort referensen till layout-sidan. Lägg till följande kodfragment till _Unified. cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Öppna den _enhetliga. cshtml_ -fil som du laddade ned från Azure AD B2C inbyggd HTML5-mall.

9. Ersätt enkelt vid tecken (@) med dubbla snabel-tecken (@ @).

10. Kopiera innehållet i filen och klistra in det under layout-definitionen. Koden bör se ut så här:

    ![enhetlig. cshtml-fil efter tillägg av HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Steg 2,3: Ändra bakgrunds bilden

Leta upp `<img>` det element som `ID` innehåller `src` värdet *background_background_image*och ersätt sedan värdet med **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** eller någon annan bakgrunds bild som du vill använda.

![img-element med anpassat background_background_image src-värde](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Steg 2,4: Lägg till vyn i MVC-kontrollanten

1. Öppna **Controllers\HomeController.cs**och Lägg till följande metod:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Den här koden anger att metoden bör använda en *visningsmall för* att rendera ett svar på webbläsaren. Eftersom vi inte uttryckligen anger namnet på *View* -mallfilen, använder MVC standardvärdet för den _enhetliga. cshtml_ -visnings filen i mappen */views/Home* .

    När du har lagt till den _enhetliga_ metoden bör koden se ut så här:

    ![Ändra kontroll enheten så att den återger vyn](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Felsök din webbapp och se till att den _enhetliga_ sidan är tillgänglig (till exempel `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Steg 2,5: Publicera till Azure
1. I **Solution Explorer**högerklickar du på projektet **contoso. AADB2C. UI** och väljer sedan **publicera**.

    ![Publicera till Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Välj panelen **Microsoft Azure App Service** och välj sedan **publicera**.

    ![Skapa nytt Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Fönstret **skapa App Service** öppnas. I det kan du börja skapa alla nödvändiga Azure-resurser för att köra ASP.NET-webbappen i Azure.

    > [!NOTE]
    > Mer information om hur du publicerar finns i [skapa en ASP.NET-webbapp i Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. I rutan **namn på webbapp** skriver du ett unikt namn på appen (giltiga tecken är a-z, a-z, 0-9 och bindestreck (-). Webbadressen till webbappen är `http://<app_name>.azurewebsites.NET`, där `<app_name>` är webbappens namn. Du kan godkänna namnet som genereras automatiskt och som är unikt.

4. Välj **Skapa** för att börja skapa Azure-resurser.

    ![Ange App Service egenskaper](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    När processen har skapats publicerar guiden ASP.NET-webbappen till Azure och startar sedan appen i standard webbläsaren.

5. Kopiera URL: en för den _enhetliga_ sidan (till exempel _https://< APP_NAME >. net/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Steg 3: Konfigurera CORS i Azure App Service
1. I [Azure Portal](https://portal.azure.com/)väljer du **app Services**och väljer sedan namnet på din API-app.

    ![Välj API-app i Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. I avsnittet **Inställningar** under **API** -avsnittet väljer du **CORS**.

    ![Meny alternativet CORS markerat i App Service menyn i Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Gör något av följande i rutan CORS ( **tillåtna ursprung** ) i **CORS** -fönstret:

    * Ange den URL eller de URL: er som du vill tillåta att JavaScript-anrop kommer från. Du måste använda alla små bokstäver i de URL: er som du anger.
    * Ange en asterisk (*) för att ange att alla ursprungs domäner ska accepteras.

4. Välj **Spara**.

    ![Sidan CORS-inställningar med asterisk markerat i tillåtna ursprung](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    När du har valt **Spara**accepterar API-appen JavaScript-anrop från de angivna URL: erna.

## <a name="step-4-html5-template-validation"></a>Steg 4: Validering av HTML5-mall
Din HTML5-mall är klar att använda. Det är dock inte tillgängligt i `ContentDefinition` koden. Innan du kan lägga `ContentDefinition` till den anpassade principen måste du se till att:
* Ditt innehåll är HTML5 kompatibelt och tillgängligt.
* Innehålls servern är aktive rad för CORS.

    >[!NOTE]
    >För att kontrol lera att den plats där du är värd för ditt innehåll har aktiverat CORS och kan testa CORS-begäranden, går du till [test-CORS.org](https://test-cors.org/) -webbplatsen.

* Ditt hanterade innehåll är säkert över **https**.
* Du använder *absoluta URL: er*, till `https://yourdomain/content`exempel för alla länkar, CSS-innehåll och bilder.

## <a name="step-5-configure-your-content-definition"></a>Steg 5: Konfigurera din innehålls definition
Gör följande `ContentDefinition`för att konfigurera:
1. Öppna bas filen för principen (till exempel *TrustFrameworkBase. XML*).

2. Sök efter `<ContentDefinitions>` elementet och kopiera sedan hela innehållet `<ContentDefinitions>` i noden.

3. Öppna tilläggs filen (till exempel *TrustFrameworkExtensions. XML*) och Sök sedan efter `<BuildingBlocks>` elementet. Om elementet inte finns lägger du till det.

4. Klistra in hela innehållet i `<ContentDefinitions>` noden som du kopierade som underordnat `<BuildingBlocks>` elementet.

5. Sök efter `<ContentDefinition>` noden som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.

6. Ändra värdet för `LoadUri` från _~/Tenant/default/Unified_ till _https://< APP_NAME >. azurewebsites. net/Home/Unified_.
    Den anpassade principen bör se ut så här:

    ![Exempel på XML-kodfragment med LoadUri-element markerat](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Steg 6: Överför principen till klienten
1. I [Azure Portal](https://portal.azure.com)växlar du till kontexten [för din Azure AD B2C klient](active-directory-b2c-navigate-to-b2c-context.md)och väljer sedan **Azure AD B2C**.

2. Välj **ramverk för identitets upplevelse**.

3. Välj **alla principer**.

4. Välj **Ladda upp princip**.

5. Markera kryss rutan **Skriv över principen om den finns** .

6. Ladda upp filen *TrustFrameworkExtensions. XML* och kontrol lera att den klarar verifieringen.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Steg 7: Testa den anpassade principen med hjälp av kör nu
1. Välj **Azure AD B2C inställningar**och välj sedan **Identity Experience Framework**.

    >[!NOTE]
    >Körning kräver nu att minst ett program är förregistrerade på klienten. Information om hur du registrerar program finns i artikeln Azure AD B2C [komma igång](active-directory-b2c-get-started.md) eller i [program registrering](active-directory-b2c-app-registration.md) .

2. Öppna **B2C_1A_signup_signin**, den förlitande parten (RP) anpassad princip som du överförde och välj sedan **Kör nu**.
    Du bör kunna se din anpassade HTML5 med bakgrunden som du skapade tidigare.

    ![Din registrerings-eller inloggnings princip](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Steg 8: Lägg till dynamiskt innehåll
Ändra bakgrunden baserat på frågesträngparametern med namnet _campaignId_. Ditt RP-program (webb-och mobilappar) skickar parametern till Azure AD B2C. Din princip läser parametern och skickar dess värde till din HTML5-mall.

### <a name="step-81-add-a-content-definition-parameter"></a>Steg 8,1: Lägg till en innehålls definitions parameter

Lägg till `ContentDefinitionParameters` elementet genom att göra följande:
1. Öppna *SignUpOrSignin* -filen för principen (till exempel *SignUpOrSignin. XML*).

2. Under noden lägger du till noden `UserJourneyBehaviors` `<DefaultUserJourney>` :

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Steg 8,2: Ändra koden så att den accepterar en frågesträngparametern och ersätt bakgrunds bilden
Ändra HomeController `unified` -metoden för att godkänna parametern campaignId. Metoden kontrollerar sedan parameterns värde och ställer in `ViewData["background"]` variabeln enligt detta.

1. Öppna filen *Controllers\HomeController.cs* och ändra `unified` sedan metoden genom att lägga till följande kodfragment:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Leta upp `background_background_image` `src` elementet med ID och Ersätt värdet med `@ViewData["background"]`. `<img>`

    ![img-element med src-värde markerat ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: Överför ändringarna och publicera principen
1. Publicera ditt Visual Studio-projekt i Azure App Service.

2. Ladda upp *SignUpOrSignin. XML-* principen till Azure AD B2C.

3. Öppna **B2C_1A_signup_signin**, den RP-anpassade principen som du laddade upp och välj sedan **Kör nu**.
    Du bör se samma bakgrunds bild som visades tidigare.

4. Kopiera URL: en från webbläsarens Adress fält.

5. Lägg till frågesträngparametern _campaignId_ i URI: n. Till exempel Lägg till `&campaignId=hawaii`, som visas i följande bild:

    ![URI med en campaignId-parameter för frågesträng markerad](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Välj **RETUR** för att Visa Hawaii-bakgrunds bilden.

    ![Registrera inloggnings sidan med en egen Hawaii-bild bakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Ändra värdet till *Tokyo*och välj sedan **RETUR**.
    Webbläsaren visar Tokyo-bakgrunden.

    ![Registrerings sidan för registrering med anpassad bild bakgrund för Tokyo-bilder](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Steg 9: Ändra resten av användar resan
Om du väljer länken **Registrera dig nu** på inloggnings sidan, visar webbläsaren standard bakgrunds bilden, inte den bild som du har definierat. Detta beror på att du har ändrat bara registrerings-eller inloggnings sidan. Ändra resten av de självkontrollerande innehålls definitionerna:
1. Gå tillbaka till "steg 2" och gör följande:

    a. Hämta *selfasserted* -filen.

    b. Kopiera fil innehållet.

    c. Skapa en ny vy, *selfasserted*.

    d. Lägg till *selfasserted* på **Start** styrenheten.

2. Gå tillbaka till "steg 4" och gör följande:

    a. Leta upp `<ContentDefinition>` noden som innehåller `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, och `Id="api.localaccountpasswordreset"`i tilläggs principen.

    b. Ange attributet till din selfasserted-URI.  `LoadUri`

3. Gå tillbaka till "steg 8,2" och ändra koden för att godkänna parametrar för frågesträng, men den här gången till funktionen *selfasserted* .

4. Ladda upp *TrustFrameworkExtensions. XML-* principen och kontrol lera att den klarar verifieringen.

5. Kör princip testet och välj sedan **Registrera dig nu** för att se resultatet.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Valfritt Ladda ned fullständiga principfiler och kod
* När du har slutfört guiden [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) rekommenderar vi att du skapar ditt scenario genom att använda dina egna anpassade principfiler. Vi har angett [exempel på principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)för din referens.
* Du kan ladda ned den fullständiga koden från [exempel Visual Studio-lösningen för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




