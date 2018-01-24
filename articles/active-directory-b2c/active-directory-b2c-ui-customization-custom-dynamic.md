---
title: "Azure Active Directory B2C: Anpassa Azure AD B2C-användargränssnittet (UI) dynamiskt med hjälp av anpassade principer"
description: "Stöd för flera företagsanpassning upplevelser med HTML5/CSS-innehåll som ändras dynamiskt vid körning."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: 3a2310ae6266709df6677c55f11b15239c0425a2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurera Användargränssnittet med dynamiskt innehåll med hjälp av anpassade principer
Med hjälp av Azure Active Directory B2C (Azure AD B2C) anpassade principer, kan du skicka en parameter i en frågesträng. Genom att ange parametern till din HTML-slutpunkt, kan du ändra sidinnehållet dynamiskt. Du kan till exempel ändra bakgrundsbilden på Azure AD B2C registrering eller inloggning sidan baserat på en parameter som överförs från dina webb- eller mobila program. 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln fokuserar på hur du anpassar Azure AD B2C-användargränssnittet med *dynamiskt innehåll* genom att använda anpassade principer. Om du vill komma igång finns [anpassningar i en anpassad princip](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Azure AD B2C-artikel [konfigurera anpassningar i en anpassad princip](active-directory-b2c-ui-customization-custom.md), beskriver grunderna för följande:
> * Användaren användargränssnittet (UI) anpassning funktionssidan.
> * Viktiga verktyg för att testa anpassning gränssnittsfunktionen sidan med våra exemplen.
> * Gränssnittselement för kärnor på varje sida skriver.
> * Metodtips för att tillämpa den här funktionen.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Lägga till en länk till HTML5/CSS-mallar till dina användare resa

HTML5-sida URI som används för ett specifikt gränssnitt steg (till exempel logga in eller registrera sidor) definieras i en definition av innehåll i en anpassad princip. Grundläggande principen definierar standard utseendet och känslan genom att peka till en URI för HTML5-filer (i CSS). Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen i principen för tillägget. Innehåll definitioner innehålla URL: er till externt innehåll som definieras av utforma HTML5/CSS-filer vid behov. 

Den `ContentDefinitions` avsnitt innehåller en serie `ContentDefinition` XML-element. Attributet ID för den `ContentDefinition` elementet anger vilken typ av sida som relaterar till innehåll definition. Det vill säga definierar elementet kontext som en anpassad HTML5/CSS-mall som ska tillämpas. Följande tabell beskriver uppsättningen innehållsdefinitionen ID: N som identifieras av IEF-motorn och vilka typer av sidan som relaterar till dem.

| Innehålls-ID: N | HTML5 standardmall| Beskrivning | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsidan**. Den här sidan visas när ett undantagsfel eller ett fel har påträffats. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identity-providern på sidan**. Den här sidan visar en lista över identitetsleverantörer som användarna kan välja från under inloggningen. Alternativen är vanligtvis enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitet providern val för registrering**. Den här sidan visar en lista över identitetsleverantörer som användaren kan välja bland under registreringen. Alternativen är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Har du glömt lösenordssidan**. Den här sidan innehåller ett formulär som användarna måste slutföra för att initiera en återställning av lösenord.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Lokalt konto inloggningssidan**. Den här sidan innehåller ett formulär för att logga in med ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Formuläret kan innehålla en textruta och inmatningsfält för lösenord. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Lokalt konto registrering**. Den här sidan innehåller ett formulär för att registrera dig för ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika inkommande kontroller, exempelvis: indata för en text ruta, inmatningsfält för lösenord, en alternativknapp, enkelval listrutorna och markera kryssrutorna. |
| *api.phonefactor* | [multifaktoråtkomstkontroll 1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multifaktorautentiseringssidan**. På den här sidan verifiera användare sina telefonnummer (med hjälp av text- eller röst) under registrering eller inloggning. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sociala konto registreringssidan**. Den här sidan innehåller ett formulär som användarna måste slutföra när de loggar med ett befintligt konto från sociala identitetsleverantören. Den här sidan liknar föregående sociala konto registreringssidan, förutom fälten lösenord post. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Uppdatera profilsida**. Den här sidan innehåller ett formulär som kan användas för att uppdatera sin profil. Den här sidan liknar sociala konto registreringssidan, förutom fälten lösenord post. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrering eller inloggning sidan**. Den här sidan hanterar användarprocess för registrering och inloggning. Användarna kan använda enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton.  |

## <a name="serving-dynamic-content"></a>Betjänar dynamiskt innehåll
I den [konfigurera anpassningar i en anpassad princip](active-directory-b2c-ui-customization-custom.md) artikeln får du ladda upp HTML5-filer till Azure Blob storage. Dessa HTML5-filer är statiska och återge samma HTML innehåll för varje begäran. 

I den här artikeln använder du en ASP.NET webbapp, vilket kan ta emot frågesträngparametrar och svara därefter. 

I den här genomgången ska du:
* Skapa en ASP.NET Core webbprogram som är värd för dina HTML5-mallar. 
* Lägg till en anpassad mall i HTML5 _unified.cshtml_. 
* Publicera webbappen i Azure App Service. 
* Ange cross-origin-resursdelning (CORS) för din webbapp.
* Åsidosätta den `LoadUri` element att peka på HTML5-fil.

## <a name="step-1-create-an-aspnet-web-app"></a>Steg 1: Skapa en ASP.NET-webbprogram

1. Skapa ett projekt i Visual Studio genom att välja **filen** > **ny** > **projekt**.

2. I den **nytt projekt** väljer **Visual C#** > **Web** > **kärnor för ASP.NET-webbprogram (.NET Core)**.

3. Ett namn för programmet (till exempel *Contoso.AADB2C.UI*), och välj sedan **OK**.

    ![Skapa nytt Visual Studio-projekt](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Välj den **webbprogram** mall.

5. Ange autentiseringen till **ingen autentisering**.

    ![Välj mall för webbprogram](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Välj **OK** att skapa projektet.

## <a name="step-2-create-mvc-view"></a>Steg 2: Skapa MVC-vy
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Steg 2.1: Ladda ned den inbyggda HTML5-mallen för B2C
Den egna HTML5-mallen är baserad på den inbyggda HTML5-mallen för Azure AD B2C. Du kan hämta den [unified.html filen](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) eller hämta mallen från [startpaket](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Du kan använda den här HTML5-filen för att skapa en enhetlig registrering eller inloggning sida.

### <a name="step-22-add-the-mvc-view"></a>Steg 2.2: Lägga till vyn MVC
1. Högerklicka på mappen vyer hem och sedan **Lägg till** > **nytt objekt**.

    ![Lägg till nytt objekt i MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. I den **Lägg till nytt objekt – Contoso.AADB2C.UI** väljer **webb > ASP.NET**.

3. Välj **MVC visa sidan**.

4. I den **namn** ändrar namnet **unified.cshtml**.

5. Välj **Lägg till**.

    ![Lägg till MVC-vy](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Om den *unified.cshtml* filen inte är öppen redan, dubbelklicka på filen för att öppna den och avmarkerar sedan filens innehåll.

7. För den här genomgången ska vi ta bort referensen till layout-sidan. Lägg till följande kodfragment till _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Öppna den _unified.cshtml_ filen som du hämtade från Azure AD B2C inbyggda HTML5-mallen.

9. Ersätt enskilt tecken (@) med ett dubbelt tecken (@@).

10. Kopiera innehållet i filen och under Layout-definition. Koden bör se ut som:

    ![Unified.cshtml fil när du lägger till i HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Steg 2.3: Ändra bakgrundsbilden

Leta upp den `<img>` element som innehåller den `ID` värdet *background_background_image*, och Ersätt den `src` värdet med **https://kbdevstorage1.blob.core.windows.net/ 19889_en_1-tillgångsinformation-blobbar** eller andra bakgrundsbild som du vill använda.

![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Steg 2.4: Lägga till vyn MVC-enhet

1. Öppna **Controllers\HomeController.cs**, och Lägg till följande metod: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Den här koden anger att metoden ska använda en *visa* mallfilen att återge ett svar till webbläsaren. Eftersom vi inte uttryckligen anger namnet på den *visa* mallfil MVC som standard använder den _unified.cshtml_ visa filen i den */vyer hem* mapp.
    
    När du lägger till den _enhetlig_ metoden koden bör se ut som:
    
    ![Ändra domänkontrollant för återgivning av vyn](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Felsöka ditt webbprogram och se till att den _enhetlig_ är tillgänglig (till exempel `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Steg 2.5: Publicera till Azure
1. I **Solution Explorer**, högerklicka på den **Contoso.AADB2C.UI** projektet och välj sedan **publicera**.

    ![Publicera till Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Välj den **Microsoft Azure App Service** panelen och välj sedan **publicera**.

    ![Skapa ny Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Den **skapa App Service** öppnas. I den kan du börja skapa alla nödvändiga Azure-resurser för att köra ASP.NET-webbapp i Azure.

    > [!NOTE]
    > Mer information om hur du publicerar finns [skapa en ASP.NET-webbapp i Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. I den **Webbprogramnamnet** Skriv ett unikt appnamn (giltiga tecken är a-z, A-Z, 0-9 och bindestreck (-). Webbadressen till webbappen är `http://<app_name>.azurewebsites.NET`, där `<app_name>` är webbappens namn. Du kan godkänna namnet som genereras automatiskt och som är unikt.

4. Välj **Skapa** för att börja skapa Azure-resurser.

    ![Ange egenskaper för Apptjänst](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    När processen är klar, guiden publicerar ASP.NET-webbapp till Azure och sedan startar appen i standardwebbläsaren.

5. Kopiera Webbadressen till den _enhetlig_ sida (till exempel _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Steg 3: Konfigurera CORS i Azure App Service
1. I den [Azure-portalen](https://portal.azure.com/)väljer **Apptjänster**, och välj sedan namnet på din API-app.

    ![Välj API-app i Azure-portalen](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. I den **inställningar** under avsnittet **API** väljer **CORS**.

    ![Välj CORS-inställningarna](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. I den **CORS** fönster i den **tillåtna ursprung** gör något av följande:

    * Ange URL-Adressen eller URL: er som du vill tillåta JavaScript-anrop från.
    * Ange en asterisk (*) om du vill ange att alla ursprungsdomäner accepteras.

4. Välj **Spara**.

    ![Fönstret CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    När du har valt **spara**, API-appen tar emot JavaScript-anrop från de angivna URL: er. 

## <a name="step-4-html5-template-validation"></a>Steg 4: HTML5 mallen verifiering
HTML5-mallen är redo att användas. Det är dock inte tillgängliga i den `ContentDefinition` kod. Innan du kan lägga till `ContentDefinition` till den anpassade principen, se till att:
* Innehållet är HTML5 kompatibla och tillgänglig.
* Innehållsservern har aktiverats för CORS.

    >[!NOTE]
    >Kontrollera att den plats där du är värd för ditt innehåll har aktiverat CORS och testa CORS begäranden, gå till den [test cors.org](http://test-cors.org/) webbplats. 

* Hanteras innehållet är säkert över **HTTPS**.
* Du använder *absoluta URL: er*, som *https://yourdomain/content*, för alla länkar, CSS-innehåll och bilder.

## <a name="step-5-configure-your-content-definition"></a>Steg 5: Konfigurera din innehåll definition
Så här konfigurerar du `ContentDefinition`, gör du följande:
1. Öppna filen grundläggande av principen (till exempel *TrustFrameworkBase.xml*).

2. Sök efter den `<ContentDefinitions>` element, och sedan kopiera hela innehållet i den `<ContentDefinitions>` nod.

3. Öppna tilläggsfilen (till exempel *TrustFrameworkExtensions.xml*) och sök sedan efter den `<BuildingBlocks>` element. Lägg till om elementet inte finns.

4. Klistra in hela innehållet i den `<ContentDefinitions>` nod som du kopierade som underordnad till den `<BuildingBlocks>` element. 

5. Sök efter den `<ContentDefinition>` nod som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.

6. Ändra värdet för `LoadUri` från _~/tenant/default/unified_ till _https://<app_name>.azurewebsites.net/home/unified_.  
    En anpassad princip bör se ut ungefär så här:
    
    ![Definition av innehåll](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Steg 6: Överför principen till din klient
1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.

2. Välj **identitet upplevelse Framework**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **skriva över principen om den finns** kryssrutan.

6. Överför den *TrustFrameworkExtensions.xml* filen och se till att den har klarat valideringen.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Steg 7: Testa den anpassade principen genom att använda Kör nu
1. Välj **Azure AD B2C inställningar**, och välj sedan **identitet upplevelse Framework**.

    >[!NOTE]
    >Kör nu kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.

2. Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du överfört och välj sedan **kör nu**.  
    Du ska kunna se din anpassade HTML5 med bakgrund som du skapade tidigare.

    ![Din princip för registrering eller inloggning](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Steg 8: Lägga till dynamiskt innehåll
Ändra rapportens bakgrund baserat på frågesträngparametern med namnet _campaignId_. Tillämpningsprogrammet RP (webb- och mobilappar) skickar parametern till Azure AD B2C. Din princip läser parametern och skickar dess värde i HTML5-mallen. 

### <a name="step-81-add-a-content-definition-parameter"></a>Steg 8.1: Lägg till en parameter för definition av innehåll

Lägg till den `ContentDefinitionParameters` element genom att göra följande:
1. Öppna den *SignUpOrSignin* -filen för principen (till exempel *SignUpOrSignin.xml*).

2. Sök efter den `<DefaultUserJourney>` nod. 

3. I den `<DefaultUserJourney>` nod, Lägg till följande XML-kodstycke:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Steg 8.2: Ändra koden för att godkänna en frågesträngsparameter och Ersätt bakgrundsbilden
Ändra HomeController `unified` metod för att acceptera campaignId-parametern. Metoden söker parametern har värdet och anger den `ViewData["background"]` variabeln därefter.

1. Öppna den *Controllers\HomeController.cs* filen och sedan ändra den `unified` metoden genom att lägga till följande kodfragment:

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

2. Leta upp den `<img>` element med ID `background_background_image`, och Ersätt den `src` värdet med `@ViewData["background"]`.

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: överföra ändringarna och publicera din princip
1. Publicera din Visual Studio-projekt i Azure App Service.

2. Överför den *SignUpOrSignin.xml* principen till Azure AD B2C.

3. Öppna **B2C_1A_signup_signin**, RP anpassade principer som du överfört och välj sedan **kör nu**.  
    Du bör se samma bakgrundsbilden som visades tidigare.

4. Kopiera URL-Adressen från adressfältet i webbläsaren.

5. Lägg till den _campaignId_ frågesträngparametern till URI: N. Lägg exempelvis till `&campaignId=hawaii`, enligt följande bild:

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Välj **RETUR** att visa Hawaii bakgrundsbild.

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Ändra värdet till *Tokyo*, och välj sedan **RETUR**.  
    Webbläsaren visar Tokyo bakgrund.

    ![Ändra sidbakgrund](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Steg 9: Ändra resten av användaren resa
Om du väljer den **registrera nu** länk på sidan logga in webbläsaren visar standardbakgrundsbilden, inte bilden som du har definierat. Det här problemet uppstår eftersom du har ändrat endast sidan registrering eller inloggning. Ändra resten av själva Assert innehåll definitioner:
1. Gå tillbaka till ”steg 2” och gör följande:

    a. Hämta den *selfasserted* fil.

    b. Kopiera innehållet för filen.

    c. Skapa en ny vy *selfasserted*.

    d. Lägg till *selfasserted* till den **Start** domänkontrollant.

2. Gå tillbaka till ”steg 4” och gör följande: 

    a. I princip för tillägg finns i `<ContentDefinition>` nod som innehåller `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, och `Id="api.localaccountpasswordreset"`.

    b. Ange den `LoadUri` attribut till din *selfasserted* URI.

3. Gå tillbaka till ”steg 8.2” och ändra koden för att godkänna frågan string-parametrar, men att den *selfasserted* funktion. 

4. Överför den *TrustFrameworkExtensions.xml* principen och se till att den har klarat valideringen.

5. Kör testet princip och välj sedan **registrera nu** att se resultatet.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Hämta fullständig principfiler och kod
* När du har slutfört den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




