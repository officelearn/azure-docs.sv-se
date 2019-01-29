---
title: Anpassa Användargränssnittet för en användarresa med anpassade principer | Microsoft Docs
description: Läs mer om Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d222e398653487cb87525ac13ebe682e9c8a26ee
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195953"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Anpassa Användargränssnittet för en användarresa med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Den här artikeln är en avancerad beskrivning av hur anpassningar fungerar och hur du aktiverar med Azure AD B2C anpassade principer med hjälp av den Identitetsramverk.


En smidig användarupplevelse är nyckeln för alla företag till konsument – lösningar. En smidig användarupplevelse är en upplevelse på enheten eller webbläsaren, där en användarresa genom tjänsten går att skilja från som den kundtjänst som de använder.

## <a name="understand-the-cors-way-for-ui-customization"></a>Förstå hur CORS för anpassning av Användargränssnittet

Azure AD B2C kan du anpassa den utseende och känslan av användarupplevelsen (UX) på olika sidor som hanteras och visas i Azure AD B2C med hjälp av dina anpassade principer.

För detta ändamål, Azure AD B2C körs koden i din konsument webbläsare och använder den moderna och standard-metoden [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) att läsa in anpassat innehåll från en specifik URL som du anger i en anpassad princip för att peka mot dina HTML5/CSS-mallar. CORS är en mekanism som gör att begränsade resurser, t.ex. teckensnitt, på en webbsida begäras från en annan domän utanför domänen som resursen har sitt ursprung.

Jämfört med den gamla traditionell, där mallen sidor ägs av den information du hittar där du begränsad text och bilder, där det är begränsad kontroll över layout och känslan fanns leder till mer än problem med att uppnå en sömlös upplevelse CORS-sätt har stöd för HTML5 och CSS och gör att du kan:

- Vara värd för innehåll och lösningen lägger in dess kontroller med hjälp av skript på klientsidan.
- Har fullständig kontroll över varje bildpunkt layout och utseende.

Du kan ange så många innehållssidor som du vill genom att utforma HTML5/CSS-filer vid behov.

> [!NOTE]
> Användning av JavaScript är för närvarande blockerad för anpassning av säkerhetsskäl. 

I var och en av dina HTML5/CSS-mallar, anger du ett *förtroendeankare* element som motsvarar de nödvändiga `<div id=”api”>` element i HTML eller sidan innehåll som illustrerar nedan. Azure AD B2C kräver att alla innehållssidor har den här specifika div.

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

Azure AD B2C-relaterat innehåll för sidan är införs i den här div, medan resten av sidan är ditt att styra. Azure AD B2C JavaScript-koden hämtar i ditt innehåll och lägger in HTML i det här specifika div-elementet. Azure AD B2C lägger in följande kontroller efter behov: Väljaren kontroll, logga in kontroller, Multi-Factor Authentication (för närvarande telefon) kontroller och attributet samling kontroller. Azure AD B2C garanterar att alla kontroller är HTML5 kompatibla och kan nås, alla kontroller kan vara fullständigt formaterad och som en kontroll-version inte affärsmöjlighetens.

Det sammanlagda innehållet visas så småningom som dynamiskt dokument till ditt konsument.

För att säkerställa att allt fungerar som förväntat, måste du:

- Se till att ditt innehåll är HTML5 kompatibla och kan nås
- Se till att innehållsservern har aktiverats för CORS.
- Leverera innehåll över HTTPS.
- Använda absoluta URL-adresser som https://yourdomain/content för alla länkar och CSS-innehåll.

> [!TIP]
> Du kan använda webbplatsen för att kontrollera att du är värd för ditt innehåll på platsen har CORS aktiverat och testa CORS-förfrågningar, https://test-cors.org/. Tack vare den här platsen kan antingen skicka CORS-begäran till en fjärransluten server (för att testa om det finns stöd för CORS) eller skicka CORS-begäran till en testserver (för att utforska vissa funktioner i CORS).

> [!TIP]
> Webbplatsen https://enable-cors.org/ också utgör en mer än användbara resurser på CORS.

Tack vare den här CORS-baserade metoden har slutanvändarna konsekventa erfarenheter mellan programmet och de sidor som hanteras av Azure AD B2C.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Som ett krav måste du skapa ett lagringskonto. Du behöver en Azure-prenumeration att skapa ett Azure Blob Storage-konto. Du kan registrera dig gratis på den [Azure-webbplatsen](https://azure.microsoft.com/pricing/free-trial/).

1. Öppna en webbläsarsession och navigera till den [Azure-portalen](https://portal.azure.com).
2. Logga in med dina administratörsautentiseringsuppgifter.
3. Klicka på **skapa en resurs** > **Storage** > **lagringskonto**.  En **skapa lagringskonto** fönstret som öppnas.
4. I **namn**, ange ett namn för lagringskontot, till exempel *contoso369b2c*. Det här värdet senare som anges på *storageAccountName*.
5. Välj lämpliga val för prisnivå, resursgruppen och prenumerationen. Se till att du har den **fäst på startsidan** alternativet är markerat. Klicka på **Skapa**.
6. Gå tillbaka till startsidan och klicka på det lagringskonto som du skapade.
7. I den **Services** klickar du på **Blobar**. En **Blob service-fönstret** öppnas.
8. Klicka på **+ behållare**.
9. I **namn**, ange ett namn för behållaren, till exempel *b2c*. Det här värdet senare kallas *containerName*.
9. Välj **Blob** som den **åtkomsttyp**. Klicka på **Skapa**.
10. Den behållare som du har skapat visas i listan på den **Blob service-fönstret**.
11. Stäng den **Blobar** fönstret.
12. På den **storage-konto fönstret**, klickar du på den **nyckel** ikon. En **åtkomst nycklar fönstret** öppnas.  
13. Anteckna värdet för **key1**. Det här värdet kallas senare *key1*.

## <a name="downloading-the-helper-tool"></a>Hämta helper-verktyget

1.  Ladda ned hjälpverktyg från [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Spara den *B2C-AzureBlobStorage-klient-master.zip* filen på den lokala datorn.
3.  Extrahera innehållet i filen B2C-AzureBlobStorage-klient-master.zip på den lokala hårddisken, till exempel den **UI-anpassning-Pack** -mappen, som skapar en *B2C-AzureBlobStorage-klient-master*mapp under.
4.  Öppna mappen och extrahera innehållet i arkivfilen *B2CAzureStorageClient.zip* i den.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Ladda upp exempelfilerna UI-anpassning-Pack

1.  Använd Windows Explorer, navigera till mappen *B2C-AzureBlobStorage-klient-master* finns under den *UI-anpassning-Pack* mapp som skapades i föregående avsnitt.
2.  Kör den *B2CAzureStorageClient.exe* fil. Det här programmet överför alla filer i den katalog du anger till ditt lagringskonto och aktivera CORS-åtkomst för dessa filer.
3.  När du uppmanas, ange: en.  Namnet på ditt lagringskonto *storageAccountName*, till exempel *contoso369b2c*.
    b.  Den primära åtkomstnyckeln för ditt azure blob storage *key1*, till exempel *contoso369b2c*.
    c.  Namnet på din storage blob storage-behållare, *containerName*, till exempel *b2c*.
    d.  Sökvägen till den *-startpaket* exempelfiler, till exempel *... \B2CTemplates\wingtiptoys*.

Om du har följt föregående steg, HTML5 och CSS-filer av den *UI-anpassning-Pack* för det fiktiva företaget **VingspetsLeksaker** nu pekar på ditt lagringskonto.  Du kan kontrollera att innehållet har laddats upp korrekt genom att öppna fönstret relaterade behållare i Azure-portalen. Du kan också kontrollera att innehållet har laddats upp korrekt genom att gå till sidan från en webbläsare. Mer information finns i [Azure Active Directory B2C: En hjälpverktyg som används för att visa sidan användare användargränssnitt (UI) anpassning av funktionen](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Se till att lagringskontot har CORS aktiverat

CORS (Cross-Origin Resource Sharing) måste aktiveras på din slutpunkt för Azure AD B2C att läsa in ditt innehåll. Det beror på att innehållet finns på en annan domän än den Azure AD B2C kommer betjänar sidan från.

Om du vill kontrollera att den lagring som du är värd för ditt innehåll på har CORS aktiverat, fortsätter du med följande steg:

1. Öppna en webbläsarsession och gå till sidan *unified.html* med hjälp av den fullständiga URL: en för dess plats i ditt storage-konto `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Till exempel https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navigera till https://test-cors.org. Den här platsen kan du kontrollera att den sidan som du använder har CORS aktiverat.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. I **Remote URL**, ange den fullständiga URL: en för ditt unified.html innehåll och på **skicka förfrågan**.
4. Kontrollera att utdata i den **resultat** innehåller *XHR status: 200*, vilket betyder att CORS är aktiverat.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Lagringskontot ska nu innehålla en blobbehållare med namnet *b2c* bilden innehåller följande VingspetsLeksaker mallar från den *-startpaket*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

I följande tabell beskrivs syftet med de föregående HTML5-sidorna.

| HTML5-mall | Beskrivning |
|----------------|-------------|
| *phonefactor.HTML* | Den här sidan kan användas som en mall för en sida för multifaktorautentisering. |
| *resetpassword.html* | Den här sidan kan användas som en mall för en sida för glömt lösenord. |
| *selfasserted.html* | Den här sidan kan användas som en mall för ett socialt konto registrera sida, en registreringssida för lokalt konto eller ett lokalt konto på inloggningssidan. |
| *unified.html* | Den här sidan kan användas som en mall för en enhetlig registrering eller på inloggningssidan. |
| *updateprofile.html* | Den här sidan kan användas som en mall för en uppdatering profilsida. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Lägg till en länk till dina HTML5/CSS-mallar till din användarresa

Du kan lägga till en länk till dina HTML5/CSS-mallar till din användarresa genom att redigera en anpassad princip direkt.

Anpassade HTML5/CSS-mallarna för att använda i din användarresan måste anges i en lista över innehållsdefinitioner som kan användas i dessa användare-utbildning. För detta ändamål, en valfri *<ContentDefinitions>* XML-element måste deklareras den *<BuildingBlocks>* i din anpassad princip för XML-fil.

I följande tabell beskriver uppsättningen innehåll definition-ID: N som identifieras av Azure AD B2C-identitet uppleva-motorn och vilken typ av sidor som relaterar till dem.

| Innehållsdefinition-ID | Beskrivning |
|-----------------------|-------------|
| *api.error* | **Felsida**. Den här sidan visas när ett undantag eller ett fel har påträffats. |
| *api.idpselections* | **Sida för val av identitet**. Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja mellan under inloggning. Dessa providers är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton (baserat på e-postadress eller användarnamn namn). |
| *api.idpselections.signup* | **Identitets-provider-markeringen för registrering**. Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja mellan under registreringen. Dessa providers är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton (baserat på e-postadress eller användarnamn namn). |
| *api.localaccountpasswordreset* | **Sida för glömt lösenord**. Den här sidan innehåller ett formulär som användaren har att fylla för att initiera sina återställning av lösenord.  |
| *api.localaccountsignin* | **Lokalt konto på inloggningssidan**. Den här sidan innehåller en inloggningsformuläret som användaren har att fylla i när du loggar in med ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla ett textinmatningsrutan och lösenordsruta. |
| *api.localaccountsignup* | **Registreringssida för lokalt konto**. Den här sidan innehåller en fyllt i registreringsformuläret som användaren har att fylla i när du registrerar dig för ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika indatakontroller, till exempel textinmatningsrutan, lösenordsruta, alternativknappen, flervals-listrutorna och välja flera kryssrutor. |
| *api.phonefactor* | **Multifaktorautentiseringssidan**. Användare kan verifiera sina telefonnummer (med text eller röst) under registrering eller inloggning på den här sidan. |
| *api.selfasserted* | **Registreringssida för socialt konto**. Den här sidan innehåller en fyllt i registreringsformuläret som användaren har att fylla i när du registrerar dig med ett befintligt konto från en social identitetsprovider, till exempel Facebook eller Google +. Den här sidan liknar föregående socialt konto registreringssidan med undantag för inmatningsfält för lösenord. |
| *api.selfasserted.profileupdate* | **Uppdatera profilsida**. Den här sidan innehåller ett formulär som användaren kan använda för att uppdatera sina profiler. Den här sidan liknar föregående socialt konto registreringssidan med undantag för inmatningsfält för lösenord. |
| *api.signuporsignin* | **Sida för enhetlig registrering eller inloggning**.  Den här sidan hanterar både registrering och inloggning av användare som kan använda enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton.

## <a name="next-steps"></a>Nästa steg
[Referens: Förstå hur anpassade principer fungerar med Identitetsramverk i B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
