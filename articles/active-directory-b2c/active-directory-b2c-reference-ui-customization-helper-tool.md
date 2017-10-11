---
title: 'Azure Active Directory B2C: Page UI anpassning helper tool | Microsoft Docs'
description: "Ett helper-verktyg som används för att visa sidan anpassning gränssnittsfunktionen i Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ae935d52-3520-4a94-b66e-b35bb40e7514
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: swkrish
ms.openlocfilehash: e0c2d827553567ddbc7d006192dc35574e66f1cd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: En helper verktyg som används för att demonstrera funktionen sidan för anpassning av användaren-användargränssnittet (UI)
Den här artikeln är en medlem i den [Huvudartikel för UI-anpassning](active-directory-b2c-reference-ui-customization.md) i Azure Active Directory (AD Azure) B2C. Följande steg beskriver hur utöva sidan anpassning gränssnittsfunktionen med hjälp av HTML- och CSS exemplen som vi tillhandahåller.

## <a name="get-an-azure-ad-b2c-tenant"></a>Skaffa en Azure AD B2C-klient
Innan du kan anpassa allt du behöver [skaffa en Azure AD B2C-klient](active-directory-b2c-get-started.md) om du inte redan har ett.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Skapa en princip för registrering eller inloggning
Exemplen som vi tillhandahåller kan användas för att customze två sidor i en [princip för registrering eller inloggning](active-directory-b2c-reference-policies.md): den [enhetlig inloggningssidan](active-directory-b2c-reference-ui-customization.md) och [sidan själva uppgavs attribut](active-directory-b2c-reference-ui-customization.md). När [skapar din princip för registrering eller inloggning](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), lägga till lokalt konto (e-postadress), Facebook, Google och Microsoft som **identitetsleverantörer**. Det här är de enda IDPs som tar emot i vårt exempel HTML-innehåll.  Du kan också lägga till en delmängd av dessa IDPs om du vill.

## <a name="register-an-application"></a>Registrera ett program
Du behöver [registrera ett program](active-directory-b2c-app-registration.md) i din B2C-klient som kan användas för att köra din princip. När du har registrerat ditt program har du några alternativ som du kan använda för att faktiskt kör principen för registrering:

* Skapa en Azure AD B2C som Snabbstart, starta program som anges i avsnittet ”Kom igång” i [registrera och logga in användare i dina program](active-directory-b2c-overview.md#get-started).
* Använd det fördefinierade [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) program. Om du väljer att använda playground måste du registrera ett program i B2C-klienten med hjälp av den **omdirigerings-URI** `https://aadb2cplayground.azurewebsites.net/`.
* Använd den **kör nu** knappen principen i den [Azure-portalen](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Anpassa din princip
Om du vill anpassa utseendet och känslan av principen måste du först skapa HTML- och CSS-filer med hjälp av särskilda konventioner för Azure AD B2C. Du kan sedan överföra dina statiskt innehåll till en tillgänglig plats så att Azure AD B2C kan komma åt den. Detta kan vara dina egna dedikerade webbservern, Azure Blob Storage, Azure Content Delivery Network eller någon annan statisk värd för resursen provider. De enda kraven är att innehållet är tillgängligt via HTTPS och kan nås med hjälp av CORS. När du har exponerad din statiskt innehåll på webben kan redigera du din princip för att peka på den här platsen och presentera innehållet för dina kunder. Den [Huvudartikel för UI-anpassning](active-directory-b2c-reference-ui-customization.md) beskrivs i detalj hur funktionen Azure AD B2C anpassning fungerar.

Vid tillämpningen av den här kursen vi redan skapat några exemplen och finns på Azure Blob Storage. Exemplen är en grundläggande anpassning i temat för vårt fiktiva företag, ”Wingtip Toys”. Om du vill testa den i din egen policy, gör du följande:

1. Logga in på din klient i den [Azure-portalen](https://portal.azure.com/) och gå till B2C-funktionsbladet.
2. Klicka på **principer för registrering eller inloggning** och klicka sedan på din princip (till exempel ”b2c\_1\_logga\_in\_logga\_i”).
3. Klicka på **Page UI anpassning** och sedan **Unified registrering eller inloggning sidan**.
4. Växla den **Använd anpassad sida** växla till **Ja**. I den **anpassad sidan URI** anger `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Klicka på **OK**.
5. Klicka på **registreringssidan för lokalt konto**. Växla den **Använd anpassad mall** växla till **Ja**. I den **anpassad sidan URI** anger `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Upprepa samma steg för den **sociala konto registreringssidan**.
   Klicka på **OK** två gånger för att stänga Användargränssnittet anpassning blad.
7. Klicka på **Spara**.

Nu kan du testa den anpassade principen. Du kan använda ditt eget program eller Azure AD B2C-playground om du vill, men du kan också klicka bara på den **kör nu** i principbladet. Markera programmet i den nedrullningsbara listrutan och välj lämplig omdirigerings-URI. Klicka på den **kör nu** knappen. En ny webbläsarflik öppnas och du kan köra via användarupplevelsen registrerar dig för ditt program med det nya innehållet på plats!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Överför exemplen till Azure Blob Storage
Om du vill använda Azure Blob Storage som värd för ditt innehåll kan du skapa egna lagringskonto och använda vår B2C för att överföra filer.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **+ ny** > **Data + lagring** > **lagringskonto**. Du behöver en Azure-prenumeration att skapa ett Azure Blob Storage-konto. Du kan logga in en kostnadsfri utvärderingsversion på den [Azure-webbplatsen](https://azure.microsoft.com/pricing/free-trial/).
3. Ange en **namn** för lagringen kontot (till exempel ”contoso”) och välj lämpliga val för **prisnivå**, **resursgruppen** och  **Prenumerationen**. Se till att du har den **fäst på startsidan** alternativet som är markerat. Klicka på **Skapa**.
4. Gå tillbaka till startsidan och klicka på det lagringskonto som du nyss skapade.
5. I den **sammanfattning** klickar du på **behållare**, och klicka sedan på **+ Lägg till**.
6. Ange en **namn** för behållare (till exempel ”b2c”) och välj **Blob** som den **komma åt typen**. Klicka på **OK**.
7. Den behållare som du har skapat visas i listan på den **Blobbar** bladet. Skriv ner Webbadressen till behållaren. till exempel den bör likna `https://contoso.blob.core.windows.net/b2c`. Stäng den **Blobbar** bladet.
8. Klicka på bladet storage-konto **nycklar** och anteckna värdena för den **Lagringskontonamnet** och **primära åtkomstnyckeln** fält.

> [!NOTE]
> **Primära åtkomstnyckeln** är en viktig säkerhetsuppgift för autentisering.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Hämta verktyg och exempel hjälpfiler
Du kan hämta den [Azure Blob Storage helper-verktyget och exempel filer som en .zip-fil](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) eller klona från GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Den här databasen innehåller en `sample_templates\wingtip` directory, som innehåller exempel HTML, CSS och bilder. För dessa mallar att referera till din egen Azure Blob Storage-konto, behöver du redigera HTML-filer. Öppna `unified.html` och `selfasserted.html` och Ersätt alla förekomster av `https://localhost` med URL-Adressen för din egen behållare som du skrev ned i föregående steg. Du måste använda den absoluta sökvägen till HTML-filer eftersom i det här fallet HTML hanteras av Azure AD under domänen `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Ladda upp exempeldata
I samma databas packa `B2CAzureStorageClient.zip` och kör den `B2CAzureStorageClient.exe` filen i. Det här programmet kommer bara ladda upp alla filer i katalogen som du anger till ditt lagringskonto och aktiverar CORS-åtkomst för dessa filer. Om du följde stegen ovan att HTML och CSS-filer nu peka till ditt lagringskonto. Observera att namnet på ditt lagringskonto är den del som föregår `blob.core.windows.net`, till exempel `contoso`. Du kan kontrollera att innehållet har överförts korrekt genom att försöka komma åt `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` i en webbläsare. Också använda [http://test-cors.org/](http://test-cors.org/) att se till att innehållet är nu CORS är aktiverat. (Sök efter ”XHR status: 200” i resultatet.)

### <a name="customize-your-policy-again"></a>Anpassa din princip igen
Nu när du har överfört exemplen till ditt eget lagringskonto, måste du redigera principen för registrering för att referera till den. Upprepa steg från den [”anpassa principen”](#customize-your-policy) avsnittet ovan är nu använda ditt eget lagringskonto URL-adresser. Till exempel platsen för din `unified.html` filen skulle vara `<url-of-your-container>/wingtip/unified.html`.

Nu kan du använda den **kör nu** knapp eller programmet ska köras principen igen. Resultatet bör se ut nästan exakt samma--som du använde samma prov HTML- och CSS i båda fallen. Men dina principer nu refererar till en egen instans av Azure Blob Storage och du kan redigera och överför filer igen som du. Mer information om hur du anpassar HTML- och CSS avser den [Huvudartikel för UI-anpassning](active-directory-b2c-reference-ui-customization.md).

