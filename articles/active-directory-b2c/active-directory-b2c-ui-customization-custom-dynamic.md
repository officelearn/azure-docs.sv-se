---
title: Anpassa Azure Active Directory B2C-användargränssnittet (UI) dynamiskt med hjälp av anpassade principer | Microsoft Docs
description: Stöd för flera företagsanpassning upplevelser med HTML5/CSS-innehåll som ändras dynamiskt vid körning.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11a14bc8b593e5e7d81e9bdbd4ac4ee3b2bbecaa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582895"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurera Användargränssnittet med dynamiskt innehåll med hjälp av anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med hjälp av Azure Active Directory B2C (Azure AD B2C) anpassade principer, kan du skicka en parameter i en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln handlar om hur du anpassar användargränssnittet för Azure AD B2C med *dynamiskt innehåll* genom att använda anpassade principer. Kom igång genom att se [anpassning av Användargränssnittet i en anpassad princip](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Azure AD B2C-artikeln [konfigurera anpassningar i en anpassad princip](active-directory-b2c-ui-customization-custom.md), beskriver följande grunderna:
> * Sidan användare användargränssnitt (UI) anpassning av funktionen.
> * Viktiga verktyg för att testa funktionen sida Användargränssnittet anpassning med hjälp av vårt exempel innehåll.
> * Gränssnittselement för kärnor för varje typ av sidan.
> * Metodtips för att tillämpa den här funktionen.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Lägg till en länk till HTML5/CSS-mallar till din användarresa

I en anpassad princip definierar en innehållsdefinition sidan HTML5 URI som används för ett specifikt UI-steg (till exempel logga in eller registrera dig sidor). Basprincipen definierar standard utseendet och känslan genom att peka till en URI för HTML5-filer (i CSS). Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen i tillägget-principen. Innehållsdefinitioner innehålla URL: er till externa innehåll som har definierats genom att utforma HTML5/CSS-filer, vid behov. 

Den `ContentDefinitions` avsnittet innehåller en serie `ContentDefinition` XML-element. Attributet ID för den `ContentDefinition` elementet anger vilken typ av sida som relaterar till innehållsdefinitionen. Det vill säga definierar elementet den kontext som en anpassad mall för HTML5/CSS ska tillämpa. I följande tabell beskriver uppsättningen innehållsdefinition ID: N som känns igen av IEF-motorn och vilka typer av sidan som är relaterade till dem.

| Innehållsdefinition-ID | Standardmall för HTML5| Beskrivning | 
|-----------------------|--------|-------------|
| *api.error* | [Exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida**. Den här sidan visas när ett undantag eller ett fel har påträffats. |
| *API.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sida för val av identitet**. Den här sidan visar en lista över identitetsleverantörer som användare kan välja bland under inloggning. Alternativen är vanligtvis enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *API.idpselections.Signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitets-provider-markeringen för registrering**. Den här sidan visar en lista över identitetsleverantörer som användare kan välja bland under registreringen. Alternativen är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sida för glömt lösenord**. Den här sidan innehåller ett formulär som användare måste slutföra för att initiera en lösenordsåterställning.  |
| *API.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Lokalt konto på inloggningssidan**. Den här sidan innehåller ett formulär för att logga in med ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla ett textinmatningsrutan och lösenordsruta. |
| *API.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registreringssida för lokalt konto**. Den här sidan innehåller ett formulär för att registrera dig för ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika indatakontroller, till exempel: indata i en text box, en lösenordsruta, en alternativknapp, flervals-listrutorna och markera kryssrutorna. |
| *API.phonefactor* | [multifaktoråtkomstkontroll 1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multifaktorautentiseringssidan**. På den här sidan verifiera användare sina telefonnummer (med hjälp av text eller röst) under registrering eller inloggning. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registreringssida för socialt konto**. Den här sidan innehåller ett formulär som användare måste slutföra när de registrerar sig med hjälp av ett befintligt konto från en social identitetsprovider. Den här sidan liknar föregående socialt konto registreringssidan, förutom inmatningsfält för lösenord. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Uppdatera profilsida**. Den här sidan innehåller ett formulär som användarna kan nå för att uppdatera sina profiler. Den här sidan liknar socialt konto registreringssidan, förutom inmatningsfält för lösenord. |
| *API.signuporsignin* | [Unified.HTML](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Sida för enhetlig registrering eller inloggning**. Den här sidan hanterar processen för användare registrera dig och logga in. Användare kan använda enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton.  |

## <a name="serving-dynamic-content"></a>Betjänar dynamiskt innehåll
I den [konfigurera anpassningar i en anpassad princip](active-directory-b2c-ui-customization-custom.md) artikeln får du överföra HTML5-filer till Azure Blob storage. Filerna HTML5 är statiska och återge samma HTML innehåll för varje begäran. 

I den här artikeln använder du en ASP.NET-webbapp som kan godkänna frågesträngsparametrar och svara därefter. 

I den här genomgången ska du:
* Skapa en ASP.NET Core-webbprogram som är värd för dina HTML5-mallar. 
* Lägg till en anpassad mall i HTML5 _unified.cshtml_. 
* Publicera din webbapp till Azure App Service. 
* Ange cross-origin resource sharing (CORS) för din webbapp.
* Åsidosätta den `LoadUri` element så att den pekar till HTML5-fil.

## <a name="step-1-create-an-aspnet-web-app"></a>Steg 1: Skapa en ASP.NET-webbapp

1. Skapa ett projekt i Visual Studio genom att välja **filen** > **New** > **projekt**.

2. I den **nytt projekt** väljer **Visual C#** > **Web** > **ASP.NET Core-Webbapp (.NET Core)**.

3. Ge programmet namnet (till exempel *Contoso.AADB2C.UI*), och välj sedan **OK**.

    ![Skapa nytt projekt i Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Välj den **webbprogram** mall.

5. Ange autentiseringen **ingen autentisering**.

    ![Välj mall för webbprogram](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Klicka på **OK** för att skapa projektet.

## <a name="step-2-create-mvc-view"></a>Steg 2: Skapa MVC-vy
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Steg 2.1: Ladda ned mallen B2C inbyggda HTML5
Din anpassade HTML5-mall baseras på den inbyggda HTML5-mallen för Azure AD B2C. Du kan ladda ned den [unified.html filen](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) eller hämta en mall från [startpaket](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Du kan använda den här HTML5-filen för att skapa en enhetlig sida för registrering eller inloggning.

### <a name="step-22-add-the-mvc-view"></a>Steg 2.2: Lägg till MVC-vy
1. Högerklicka på mappen vyer/start och sedan **Lägg till** > **nytt objekt**.

    ![Lägg till nytt objekt i MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. I den **Lägg till nytt objekt – Contoso.AADB2C.UI** väljer **webb > ASP.NET**.

3. Välj **MVC visa sidan**.

4. I den **namn** ändrar du namnet på **unified.cshtml**.

5. Välj **Lägg till**.

    ![Lägg till MVC-vy](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Om den *unified.cshtml* filen är inte öppen redan, dubbelklicka på filen för att öppna den och avmarkerar sedan filinnehållet.

7. Den här genomgången ska vi ta bort referensen till layoutsidan. Lägg till följande kodfragment till _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Öppna den _unified.cshtml_ filen du laddade ned från Azure AD B2C inbyggd HTML5-mall.

9. Ersätt enskilt tecken (@) med ett dubbelt loggar (@@).

10. Kopiera innehållet i filen och klistra in den nedan Layout-definition. Koden bör se ut:

    ![Unified.cshtml filen när du lägger till HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Steg 2.3: Ändra bakgrundsbilden

Leta upp den `<img>` element som innehåller den `ID` värdet *background_background_image*, och Ersätt sedan den `src` värde med **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** eller någon annan bakgrundsbild som du vill använda.

![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Steg 2.4: Lägga till vyn i MVC-kontrollant

1. Öppna **Controllers\HomeController.cs**, och Lägg till följande metod: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Den här koden anger att metoden ska använda en *visa* mallfilen att återge ett svar till webbläsaren. Eftersom vi inte uttryckligen anger namnet på den *visa* mallfilen, MVC använder som standard den _unified.cshtml_ Visa fil i den */vyer/Home* mapp.
    
    När du lägger till den _enhetlig_ metoden koden bör se ut:
    
    ![Ändra domänkontrollant för att rendera vyn](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Felsöka din webbapp och se till att den _enhetlig_ är tillgänglig (till exempel `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Steg 2,5: Publicera till Azure
1. I **Solution Explorer**, högerklicka på den **Contoso.AADB2C.UI** projektet och välj sedan **publicera**.

    ![Publicera till Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Välj den **Microsoft Azure App Service** panelen och välj sedan **publicera**.

    ![Skapa nya Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Den **skapa App Service** öppnas. I den kan du börja skapa alla Azure-resurser för att köra ASP.NET-webbapp i Azure.

    > [!NOTE]
    > Mer information om hur du publicerar finns i [skapa en ASP.NET-webbapp i Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. I den **Webbappnamnet** skriver du ett unikt appnamn (giltiga tecken är a – z, A – Z, 0-9 och bindestreck (-). Webbadressen till webbappen är `http://<app_name>.azurewebsites.NET`, där `<app_name>` är webbappens namn. Du kan godkänna namnet som genereras automatiskt och som är unikt.

4. Välj **Skapa** för att börja skapa Azure-resurser.

    ![Ange egenskaper för App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    När processen har slutförts publiceras ASP.NET-webbapp till Azure i guiden och sedan öppnas appen i standardwebbläsaren.

5. Kopiera Webbadressen till den _enhetlig_ sida (till exempel _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Steg 3: Konfigurera CORS i Azure App Service
1. I den [Azure-portalen](https://portal.azure.com/)väljer **Apptjänster**, och välj sedan namnet på din API-app.

    ![Välj API-app i Azure portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. I den **inställningar** under avsnittet **API** väljer **CORS**.

    ![Välj CORS-inställningar](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. I den **CORS** fönstret i den **tillåtna ursprung** gör något av följande:

    * Ange URL eller URL: er som du vill tillåta JavaScript-anrop från. Du måste använda gemener i URL: er som du anger.
    * Ange en asterisk (*) om du vill ange att alla ursprungsdomäner accepteras.

4. Välj **Spara**.

    ![Fönstret CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    När du har valt **spara**, API-appen tar emot JavaScript-anrop från de angivna URL: er. 

## <a name="step-4-html5-template-validation"></a>Steg 4: HTML5 mallverifieringen
HTML5 mallen är redo att använda. Det är dock inte tillgängliga i den `ContentDefinition` kod. Innan du kan lägga till `ContentDefinition` till en egen princip, se till att:
* Innehållet är HTML5 kompatibla och kan nås.
* Innehållsservern har aktiverats för CORS.

    >[!NOTE]
    >Kontrollera att platsen där du är värd för ditt innehåll har aktiverat CORS och testa CORS-förfrågningar, gå till den [test cors.org](http://test-cors.org/) webbplats. 

* Hanteras innehållet är säkert över **HTTPS**.
* Du använder *absoluta URL: er*, till exempel *https://yourdomain/content*, för alla länkar, CSS-innehåll och bilder.

## <a name="step-5-configure-your-content-definition"></a>Steg 5: Konfigurera din innehållsdefinition
Konfigurera `ContentDefinition`, gör du följande:
1. Öppna filen grundläggande av din princip (till exempel *TrustFrameworkBase.xml*).

2. Sök efter den `<ContentDefinitions>` element, och sedan kopiera hela innehållet i den `<ContentDefinitions>` noden.

3. Öppna tilläggsfilen (till exempel *TrustFrameworkExtensions.xml*) och söker sedan efter den `<BuildingBlocks>` element. Om elementet inte finns kan du lägga till den.

4. Klistra in hela innehållet i den `<ContentDefinitions>` nod som du kopierade som underordnad till den `<BuildingBlocks>` element. 

5. Sök efter den `<ContentDefinition>` nod som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.

6. Ändra värdet för `LoadUri` från _~/tenant/default/unified_ till _https://<app_name>.azurewebsites.net/home/unified_.  
    En anpassad princip bör se ut så här:
    
    ![Din innehållsdefinition](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Steg 6: Ladda upp principen till din klient
1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.

2. Välj **Identitetsramverk**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **Skriv över principen om den finns** markerar du kryssrutan.

6. Ladda upp den *TrustFrameworkExtensions.xml* filen och se till att den godkänns vid.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Steg 7: Testa den anpassade principen med hjälp av kör nu
1. Välj **Azure AD B2C-inställningar**, och välj sedan **Identitetsramverk**.

    >[!NOTE]
    >Kör nu kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.

2. Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassa princip som du överförde och väljer sedan **kör nu**.  
    Du ska kunna se din anpassade HTML5 med bakgrunden som du skapade tidigare.

    ![Din princip för registrering eller inloggning](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Steg 8: Lägg till dynamiskt innehåll
Ändra bakgrund som baseras på frågesträngparametern med namnet _campaignId_. Ditt RP-program (webb- och mobilappar) skickar parametern till Azure AD B2C. Din princip läser parametern och skickar värdet i mallen HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Steg 8.1: Lägg till en innehållsdefinition-parameter

Lägg till den `ContentDefinitionParameters` element genom att göra följande:
1. Öppna den *SignUpOrSignin* fil i din princip (till exempel *SignUpOrSignin.xml*).

2. Sök efter den `<DefaultUserJourney>` noden. 

3. I den `<DefaultUserJourney>` nod, Lägg till följande XML-fragment:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Steg 8.2: Ändra koden för att godkänna en frågesträngsparameter och Ersätt bakgrundsbild
Ändra HomeController `unified` metod för att godkänna parametern campaignId. Metoden kontrollerar sedan parametern är värdet och anger den `ViewData["background"]` variabeln därefter.

1. Öppna den *Controllers\HomeController.cs* filen och ändra sedan den `unified` metoden genom att lägga till följande kodavsnitt:

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

2. Leta upp den `<img>` element s ID `background_background_image`, och Ersätt den `src` värde med `@ViewData["background"]`.

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: överföra ändringarna och publicera principen
1. Publicera Visual Studio-projektet till Azure App Service.

2. Ladda upp den *SignUpOrSignin.xml* principen till Azure AD B2C.

3. Öppna **B2C_1A_signup_signin**, den anpassade RP-principen som du överförde och väljer sedan **kör nu**.  
    Du bör se samma bakgrundsbilden som visades tidigare.

4. Kopiera URL: en från adressfältet i webbläsaren.

5. Lägg till den _campaignId_ frågesträngparametern till URI: N. Lägg exempelvis till `&campaignId=hawaii`, enligt följande bild:

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Välj **RETUR** att visa Hawaii bakgrundsbilden.

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Ändra värdet till *Tokyo*, och välj sedan **RETUR**.  
    Webbläsaren visar Tokyo bakgrunden.

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Steg 9: Ändra resten av användarresa
Om du väljer den **registrera dig nu** visar länken på sidan logga in, webbläsaren standardbakgrundsbilden, inte avbildningen som du har definierat. Det här problemet uppstår eftersom du har ändrat endast sidan registrering eller inloggning. Så här ändrar resten av lokal Assert innehållsdefinitioner:
1. Gå tillbaka till ”steg 2” och gör följande:

    a. Ladda ned den *selfasserted* fil.

    b. Kopiera filinnehållet.

    c. Skapa en ny vy *selfasserted*.

    d. Lägg till *selfasserted* till den **Start** controller.

2. Gå tillbaka till ”steg 4” och gör följande: 

    a. I princip för tillägg för att hitta den `<ContentDefinition>` nod som innehåller `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, och `Id="api.localaccountpasswordreset"`.

    b. Ange den `LoadUri` attributet din *selfasserted* URI.

3. Gå tillbaka till ”steg 8.2” och ändringar i koden för att godkänna frågesträngparametrar, men körningen av den *selfasserted* funktion. 

4. Ladda upp den *TrustFrameworkExtensions.xml* principen, och se till att den godkänns vid.

5. Kör testet principen och välj sedan **registrera dig nu** och se resultatet.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Ladda ned fullständig principfiler och kod
* När du har slutfört den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgången ska vi rekommenderar att du skapar ditt scenario genom att använda din egen anpassade principfiler. För referens har vi samlat [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




