---
title: "Anpassning av användargränssnitt (UI) - Azure AD B2C | Microsoft Docs"
description: "Ett avsnitt om användaren användargränssnittet (UI) anpassningsfunktionerna i Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 99f5a391-5328-471d-a15c-a2fafafe233d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: be3fe7199308606aaab002290319df9c82149433
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: Anpassa Azure AD B2C-användargränssnittet (UI)

Användarupplevelsen är ytterst viktigt i en kundinriktade program.  Utöka kunden grundläggande genom att utforma användarupplevelser med utseendet och känslan av ditt varumärke. Azure Active Directory B2C (Azure AD B2C) kan du anpassa profil för registrering, inloggning, redigering och sidor med pixel perfekt kontroll för återställning av lösenord.

> [!NOTE]
> Sidan anpassning gränssnittsfunktionen beskrivs i den här artikeln gäller inte för inloggning endast principen, dess tillhörande sidan för återställning av lösenord och verifiering e-postmeddelanden.  Använda funktionerna i [funktionen för företagsanpassning](../active-directory/customize-branding.md) i stället.
>
> På liknande sätt, om en användare intiates en redigera profilprincip *innan* logga in användaren omdirigeras till en sida som kan anpassas med hjälp av den [funktionen för företagsanpassning](../active-directory/customize-branding.md).

Den här artikeln innehåller följande avsnitt:

* Sidan anpassning gränssnittsfunktionen.
* Ett verktyg för överföring av HTML-innehåll till Azure Blob Storage för användning med sidan anpassning gränssnittsfunktionen.
* De UI-element som används av Azure AD B2C som du kan anpassa med sammanhängande formatmallar (CSS).
* Bästa metoder när du använder den här funktionen.

## <a name="the-page-ui-customization-feature"></a>Sidan anpassning gränssnittsfunktionen

Du kan anpassa utseendet och känslan av lösenord för kunden registrering, inloggning, återställning och redigering av profilen sidor (genom att konfigurera [principer](active-directory-b2c-reference-policies.md)). Kunderna får en integrerad upplevelse när navigera mellan programmet och sidor som hanteras av Azure AD B2C.

Till skillnad från andra tjänster där gränssnittsalternativ, Azure AD B2C använder en enkel och moderna metod för anpassning av Användargränssnittet.

Här är hur det fungerar: Azure AD B2C Kör koden i kundens webbläsare och använder en modern metod som kallas [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/).  Vid körning, innehållet har lästs in från en URL som du anger i en princip. Du kan ange olika URL: er för olika sidor. När innehåll lästes in från URL: en är sammanfogat med ett HTML-avsnitt från Azure AD B2C, visas sidan till kunden. Allt du behöver göra är att:

1. Skapa korrekt HTML5 innehåll med en tom `<div id="api"></div>` elementet finns någonstans i den `<body>`. Det här elementet märken där Azure AD B2C innehållet infogas.
1. Värd för ditt innehåll på en HTTPS-slutpunkt (med CORS tillåtna). Observera både hämta och alternativ metodbegäranden måste vara aktiverat när du konfigurerar CORS.
1. Använd CSS för att style-UI-element som infogas av Azure AD B2C.

### <a name="a-basic-example-of-customized-html"></a>Ett grundläggande exempel på anpassade HTML

I följande exempel är den mest grundläggande HTML-innehåll som du kan använda för att testa den här funktionen. Använd den [helper tool](active-directory-b2c-reference-ui-customization-helper-tool.md) överför och konfigurerar det här innehållet i Azure Blob storage. Du kan kontrollera den grundläggande, icke-snygg knappar & formulärfält på varje sida som visas och fungerar.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>Testa funktionen för anpassning av Användargränssnittet

Om du vill testa funktionen för anpassning av Användargränssnittet med hjälp av våra exempel HTML och CSS innehåll?  Vi har lagt [helper-verktyget](active-directory-b2c-reference-ui-customization-helper-tool.md) som överför och konfigurerar exemplen på Azure Blob storage.

> [!NOTE]
> Du kan vara värd för ditt användargränssnitt innehåll var som helst: på webbservrar, CDN-nät, AWS S3, dela filsystem, osv. Så länge innehållet finns på en offentligt tillgängliga HTTPS-slutpunkt med CORS aktiverat, är du redo att börja. Vi använder Azure Blob storage som endast illustration.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>UI-fragment inbäddade av Azure AD B2C

Följande avsnitt listar HTML5-fragment som Azure AD B2C sammanfogar till den `<div id="api"></div>` element finns i ditt innehåll. **Infoga inte dessa fragment i HTML 5-innehåll.** Tjänsten Azure AD B2C infogas i vid körning. Använd dessa fragment som referens när du skapar egna sammanhängande formatmallar (CSS).

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Fragment infogas i ”identitet providern sidan”

Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja bland under registrering eller inloggning. Knapparna omfattar sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton (baserat på e-adress eller användare).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Fragment infogas i ”lokala konton registreringssidan”

Den här sidan innehåller ett formulär för lokalt konto baserat på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika inkommande kontroller, till exempel textrutan inmatningsfält för lösenord, knappen, enkelval listrutorna och välja flera kryssrutor.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Fragment infogas i ”” sociala konto registreringssidan ”

Den här sidan visas när du loggar med ett befintligt konto från en sociala identitetsleverantören, till exempel Facebook eller Google +.  Den används när ytterligare information måste samlas in från slutanvändaren genom att använda en registreringsformuläret. Den här sidan liknar lokalt konto registreringssidan (visas i föregående avsnitt) med undantag för transaktionen lösenordsfält.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Fragment infogas i ”Unified registrering eller inloggning sidan”

Den här sidan hanterar både registrering och inloggning av användare kan använda sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Fragment infogas i ”Multi-Factor authentication-page”

Användare kan verifiera sina telefonnummer (med text eller röst) under registrering eller inloggning på den här sidan.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Fragment infogas i ”” felsidan ”

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>Lokalisera HTML-innehåll

Du kan lokalisera HTML-innehåll genom att aktivera ['Språk anpassning'](active-directory-b2c-reference-language-customization.md).  Den här funktionen aktiveras kan Azure AD B2C att vidarebefordra parametern öppna ID Connect `ui-locales`, till slutpunkten.  Innehållsservern kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som är specifika för språket.

## <a name="things-to-remember-when-building-your-own-content"></a>Kom ihåg följande när du skapar ditt eget innehåll

Om du planerar att använda sidan anpassning gränssnittsfunktionen granska följande metodtips:

* Inte kopiera innehållet i Azure AD B2C standard och försök att ändra den. Det är bäst att skapa ditt HTML5-innehåll från början och Använd standardinnehåll som referens.
* Av säkerhetsskäl att inte vi du kan använda alla JavaScript i ditt innehåll. De flesta av vad du behöver ska vara tillgänglig direkt. Om inte, Använd [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) att begära nya funktioner.
* Versioner av webbläsare som stöds:
  * Internet Explorer 11, 10, kant
  * Begränsat stöd för Internet Explorer 9, 8
  * Google Chrome 42.0 och senare
  * Mozilla Firefox 38.0 och senare
