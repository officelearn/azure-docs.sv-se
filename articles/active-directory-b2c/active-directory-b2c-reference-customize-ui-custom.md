---
title: Anpassa användar gränssnittet för en användar resa med anpassade principer | Microsoft Docs
description: Lär dig mer om att Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147529"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Anpassa användar gränssnittet för en användar resa med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Den här artikeln är en avancerad Beskrivning av hur UI-anpassning fungerar och hur du aktiverar med Azure AD B2C anpassade principer med hjälp av identitets miljö ramverket.


En smidig användar upplevelse är nyckeln till all företags lösning. En smidig användar upplevelse är en upplevelse, oavsett om enheten eller webbläsaren, där en användares resa via tjänsten inte kan särskiljas från den kund tjänst som de använder.

## <a name="understand-the-cors-way-for-ui-customization"></a>Förstå CORS-metoden för UI-anpassning

Med Azure AD B2C kan du anpassa utseendet på användar upplevelsen (UX) på de olika sidor som hanteras och visas av Azure AD B2C med hjälp av dina anpassade principer.

I detta syfte kör Azure AD B2C kod i din konsument webbläsare och använder modern och standard metoden [resurs delning mellan ursprung (CORS)](https://www.w3.org/TR/cors/) för att läsa in anpassat innehåll från en viss URL som du anger i en anpassad princip för att peka på din HTML5/CSS onlinemallar. CORS är en mekanism som tillåter att begränsade resurser, t. ex. teckensnitt, på en webb sida begärs från en annan domän utanför domänen som resursen kommer från.

Jämfört med det tidigare traditionella sättet, där mallarna ägs av lösningen där du angav begränsad text och bilder, där begränsad kontroll av layout och känsla erbjöds leda till mer än svårigheter för att uppnå en sömlös upplevelse, är CORS-metoden stöder HTML5 och CSS och gör att du kan:

- Värd för innehållet och lösningen matar in sina kontroller med skript på klient sidan.
- Ha fullständig kontroll över varje pixel av layout och känsla.

Du kan ange så många innehålls sidor som du vill genom att utforma HTML5-/CSS-filer efter behov.

> [!NOTE]
> Av säkerhets skäl är användningen av Java Script för närvarande blockerad för anpassning. 

I var och en av dina HTML5-/CSS-mallar anger du ett *Fäst punkts* element, som motsvarar det obligatoriska `<div id="api">`-elementet i HTML-filen eller innehålls sidan, som illustreras nedan. Azure AD B2C kräver att alla innehålls sidor har denna speciella div.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Azure AD B2C-relaterat innehåll för sidan matas in i div-filen, medan resten av sidan är din att styra. Den Azure AD B2C JavaScript-koden hämtar innehåll och infogar HTML i detta speciella div-element. Azure AD B2C infogar följande kontroller efter behov: konto väljar kontroll, inloggnings kontroller, Multi-Factor (för närvarande telefonbaserade) kontroller och attribut insamlings kontroller. Azure AD B2C säkerställer att alla kontroller är HTML5-kompatibla och tillgängliga, att alla kontroller kan skrivas helt och att en kontroll version inte regress.

Det sammanfogade innehållet visas slutligen som det dynamiska dokumentet till din konsument.

För att se till att allt fungerar som förväntat måste du:

- Se till att innehållet är HTML5 kompatibelt och tillgängligt
- Se till att innehålls servern är aktive rad för CORS.
- Hantera innehåll via HTTPS.
- Använd absoluta URL: er som `https://yourdomain/content` för alla länkar och CSS-innehåll.

> [!TIP]
> För att kontrol lera att platsen som du är värd för ditt innehåll på har CORS aktiverat och test-CORS-begäranden kan du använda plats https://test-cors.org/. Tack vare den här webbplatsen kan du antingen skicka CORS-begäran till en fjärrserver (om du vill testa om CORS stöds) eller skicka CORS-begäran till en test Server (för att utforska vissa funktioner i CORS).

> [!TIP]
> Plats https://enable-cors.org/ utgör också en mer användbar resurs på CORS.

Tack vare den här CORS-baserade metoden har slutanvändarna konsekventa upplevelser mellan ditt program och de sidor som hanteras av Azure AD B2C.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Som en förutsättning måste du skapa ett lagrings konto. Du behöver en Azure-prenumeration för att skapa ett Azure Blob Storage-konto. Du kan registrera dig för en kostnads fri utvärderings version på [Azure-webbplatsen](https://azure.microsoft.com/pricing/free-trial/).

1. Öppna en webbläsarsession och navigera till [Azure Portal](https://portal.azure.com).
2. Logga in med dina administrativa autentiseringsuppgifter.
3. Klicka på **skapa en resurs** > **lagrings** > **lagrings konto**.  Ett fönster för att **skapa lagrings konton** öppnas.
4. I **namn**anger du ett namn för lagrings kontot, till exempel *contoso369b2c*. Det här värdet anges senare som *storageAccountName*.
5. Välj lämpliga val för pris nivån, resurs gruppen och prenumerationen. Kontrol lera att alternativet **Fäst på Start sidan** är markerat. Klicka på **Skapa**.
6. Gå tillbaka till start sidan och klicka på det lagrings konto som du skapade.
7. I avsnittet **tjänster** klickar du på **blobbar**. En **BLOB service fönstret** öppnas.
8. Klicka på **+ container**.
9. I **namn**anger du ett namn för behållaren, till exempel *B2C*. Det här värdet kallas senare för *containerName*.
9. Välj **BLOB** som **åtkomst typ**. Klicka på **Skapa**.
10. Den behållare som du har skapat visas i listan i rutan **BLOB service**.
11. Stäng fönstret **blobbar** .
12. I **fönstret lagrings konto**klickar du på **nyckel** ikonen. **Fönstret åtkomst nycklar** öppnas.  
13. Skriv ned värdet för **KEY1**. Det här värdet är senare kallat *KEY1*.

## <a name="downloading-the-helper-tool"></a>Hjälp verktyget hämtas

1.  Hämta hjälp verktyget från [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Spara filen *B2C-AzureBlobStorage-client-Master. zip* på den lokala datorn.
3.  Extrahera innehållet i filen B2C-AzureBlobStorage-Client-master. zip på din lokala disk, till exempel under mappen **UI-Customization-Pack** , som skapar en *B2C-AzureBlobStorage-client-Master-* mapp under.
4.  Öppna mappen och extrahera innehållet i Arkiv filen *B2CAzureStorageClient. zip* i den.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Överför exempel filen UI-anpassnings paket

1.  Använd Utforskaren i Windows och navigera till mappen *B2C-AzureBlobStorage-client-Master* som finns under mappen *UI-Customization-Pack* som skapades i föregående avsnitt.
2.  Kör filen *B2CAzureStorageClient. exe* . Det här programmet överför alla filer i den katalog som du anger till ditt lagrings konto och aktiverar CORS-åtkomst för dessa filer.
3.  När du uppmanas anger du: a.  Namnet på ditt lagrings konto, *storageAccountName*, till exempel *contoso369b2c*.
    b.  Den primära åtkomst nyckeln för Azure Blob Storage, *KEY1*, till exempel *contoso369b2c*.
    c.  Namnet på lagrings-Blob storage-behållaren, *containerName*, till exempel *B2C*.
    d.  Sökvägen till exempel filen *startpack* , till exempel *. \B2CTemplates\wingtiptoys*.

Om du följde föregående steg, pekar HTML5-och CSS-filerna i *UI-anpassnings paketet* för den fiktiva företags **wingtiptoys** nu till ditt lagrings konto.  Du kan kontrol lera att innehållet har laddats upp korrekt genom att öppna fönstret relaterad behållare i Azure Portal. Du kan också kontrol lera att innehållet har laddats upp korrekt genom att öppna sidan från en webbläsare. Mer information finns i [Azure Active Directory B2C: ett hjälp verktyg som används för att demonstrera anpassnings funktionen för sid användar gränssnitt (UI)](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Se till att lagrings kontot har CORS aktiverat

CORS (resurs delning mellan ursprung) måste vara aktiverat på slut punkten för att Azure AD B2C ska kunna läsa in ditt innehåll. Detta beror på att ditt innehåll finns i en annan domän än den domän Azure AD B2C kommer att betjäna sidan från.

Fortsätt med följande steg för att kontrol lera att lagrings utrymmet som du är värd för innehållet på har CORS aktiverat:

1. Öppna en webbläsarsession och navigera till sidan *Unified. html* med den fullständiga URL: en för platsen i ditt lagrings konto `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Till exempel https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navigera till https://test-cors.org. På den här platsen kan du kontrol lera att CORS är aktiverat på sidan som du använder.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. I **fjärr-URL**anger du den fullständiga URL: en för det enhetliga HTML-innehållet och klickar på **skicka begäran**.
4. Kontrol lera att utdata i avsnittet **Results** innehåller *XHR status: 200*, vilket anger att CORS är aktiverat.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Lagrings kontot bör nu innehålla en BLOB-behållare med namnet *B2C* i bilden som innehåller följande wingtiptoys-mallar från *Start paketet*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

I följande tabell beskrivs syftet med föregående HTML5-sidor.

| HTML5-mall | Beskrivning |
|----------------|-------------|
| *phonefactor. html* | Den här sidan kan användas som mall för en Multi-Factor Authentication-sida. |
| *ResetPassword. html* | Den här sidan kan användas som mall för en glömt lösen ords sida. |
| *selfasserted. html* | Den här sidan kan användas som mall för registrering av ett socialt konto, ett lokalt konto registrerings sida eller en inloggnings sida för lokalt konto. |
| *Unified. html* | Den här sidan kan användas som mall för en enhetlig registrering eller inloggnings sida. |
| *updateprofile. html* | Den här sidan kan användas som mall för en profil uppdaterings sida. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Lägg till en länk till HTML5-/CSS-mallarna till din användar resa

Du kan lägga till en länk i HTML5-/CSS-mallarna till din användar resa genom att redigera en anpassad princip direkt.

De anpassade HTML5-/CSS-mallarna som ska användas i din användar resa måste anges i en lista över innehålls definitioner som kan användas i användar resan. För detta ändamål måste ett valfritt *\<ContentDefinitions >* XML-element deklareras under *\<BuildingBlocks >* -avsnittet i din anpassade princip-XML-fil.

I följande tabell beskrivs de innehålls Definitions-ID: n som identifieras av Azure AD B2C Identity Experience Engine och vilken typ av sidor som relaterar till dem.

| ID för innehålls definition | Beskrivning |
|-----------------------|-------------|
| *API. error* | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. |
| *API. idpselections* | **Sidan Val av identitets leverantör**. Den här sidan innehåller en lista över identitets leverantörer som användaren kan välja från vid inloggning. Dessa leverantörer är antingen företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton (baserat på e-postadress eller användar namn). |
| *API. idpselections. signup* | **Val av identitets leverantör för registrering**. Den här sidan innehåller en lista över identitets leverantörer som användaren kan välja från vid registrering. Dessa leverantörer är antingen företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton (baserat på e-postadress eller användar namn). |
| *API. localaccountpasswordreset* | **Sidan glömt lösen ord**. Den här sidan innehåller ett formulär som användaren måste fylla i för att initiera lösen ords återställning.  |
| *API. localaccountsignin* | **Inloggnings sida för lokalt konto**. Den här sidan innehåller ett inloggnings formulär som användaren måste fylla i när de loggar in med ett lokalt konto som är baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla text rutorna text rutor och lösen ord. |
| *API. localaccountsignup* | **Registrerings sida för lokalt konto**. Den här sidan innehåller ett registrerings formulär som användaren måste fylla i när de registrerar sig för ett lokalt konto som är baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, till exempel text inmatnings ruta, rutan lösen ords post, alternativ knapp, list rutor med flera val och kryss rutor med flera val. |
| *API. phonefactor* | **Sidan Multi-Factor Authentication**. På den här sidan kan användarna verifiera sina telefonnummer (med text eller röst) under registreringen eller inloggningen. |
| *API. selfasserted* | **Registrerings sida för socialt konto**. Den här sidan innehåller ett registrerings formulär som användaren måste fylla i när de registrerar sig med ett befintligt konto från en social identitetsprovider som Facebook eller Google +. Den här sidan påminner om den föregående registrerings sidan för sociala konton med undantag för fälten för lösen ords inmatning. |
| *API. selfasserted. profileupdate* | **Sidan profil uppdatering**. Den här sidan innehåller ett formulär som användaren kan använda för att uppdatera sin profil. Den här sidan påminner om den föregående registrerings sidan för sociala konton med undantag för fälten för lösen ords inmatning. |
| *API. signuporsignin* | **Enhetlig registrerings-eller inloggnings sida**.  Den här sidan hanterar både registrering & inloggnings användare, som kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton.

## <a name="next-steps"></a>Nästa steg
[Referens: förstå hur anpassade principer fungerar med identitets miljö ramverket i B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
