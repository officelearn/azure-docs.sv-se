---
title: Page UI hjälpverktyg för anpassning i Azure Active Directory B2C | Microsoft Docs
description: En hjälpverktyg som används för att visa sidan anpassning gränssnittsfunktionen i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1a37a37dbed3b5ef9733f1105444529b4d255bcf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336789"
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: En hjälpverktyg används för att visa sidan användare användargränssnitt (UI) anpassning av funktionen
Den här artikeln är avsett för den [Huvudartikel för UI-anpassning](active-directory-b2c-reference-ui-customization.md) i Azure Active Directory (Azure AD) B2C. Följande steg beskriver hur du arbeta med sidan anpassning gränssnittsfunktionen med hjälp av exemplet HTML och CSS innehåll som vi tillhandahåller.

## <a name="get-an-azure-ad-b2c-tenant"></a>Skaffa en Azure AD B2C-klient
Innan du kan anpassa vad som helst, behöver du [skaffa en Azure AD B2C-klient](active-directory-b2c-get-started.md) om du inte redan har ett.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Skapa en registrerings- eller inloggningsprincip
Exemplen som vi tillhandahåller kan användas för att anpassa virtuell två sidor i en [princip för registrering eller inloggning](active-directory-b2c-reference-policies.md): den [enhetlig inloggningssidan](active-directory-b2c-reference-ui-customization.md) och [självkontrollerad attribut sidan](active-directory-b2c-reference-ui-customization.md). När [skapar din princip för registrering eller inloggning](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), lägga till lokalt konto (e-postadress), Facebook, Google och Microsoft som **identitetsprovidrar**. Det här är de enda IDP: er som accepterar vår HTML-innehåll.  Du kan också lägga till en delmängd av dessa IDP: er om du vill.

## <a name="register-an-application"></a>Registrera ett program
Behöver du [registrera ett program](active-directory-b2c-app-registration.md) i din B2C-klient som kan användas för att köra din princip. När du har registrerat ditt program, har du några alternativ som du kan använda för att faktiskt kör registreringsprincipen:

* Skapa en Azure AD-B2C Snabbstart program som finns angivna i avsnittet ”Kom igång” i [logga och logga in konsumenter i dina program](active-directory-b2c-overview.md).
* Använd den färdiga [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) program. Om du väljer att använda playground, måste du registrera ett program i din B2C-klient med den **omdirigerings-URI** `https://aadb2cplayground.azurewebsites.net/`.
* Använd den **kör nu** knappen era principer i den [Azure-portalen](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Anpassa din princip
Om du vill anpassa utseendet och känslan av din princip, måste du först skapa HTML och CSS-filer med specifika konventioner för Azure AD B2C. Du kan sedan överföra dina statiskt innehåll till en plats som är allmänt tillgängliga så att Azure AD B2C kan komma åt den. Det kan en egen dedikerad webbserver, Azure Blob Storage, Azure Content Delivery Network eller någon annan statisk värd för resursen leverantör. De enda kraven är att innehållet är tillgängligt över HTTPS och kan nås med hjälp av CORS. När du har visas din statiskt innehåll på webben, kan du redigera din princip för att peka på den här platsen och presentera innehållet för dina kunder. Den [Huvudartikel för UI-anpassning](active-directory-b2c-reference-ui-customization.md) beskriver i detalj hur funktionen Azure AD B2C-anpassning fungerar.

För den här självstudien, har vi redan skapat vissa exemplen och finns på Azure Blob Storage. Exemplen är en väldigt grundläggande anpassning i temat på vår fiktiva företag, ”Wingtip Toys”. Om du vill testa den i din egen policy, gör du följande:

1. Logga in till din klient på den [Azure-portalen](https://portal.azure.com/) och gå till B2C-funktionsbladet.
2. Klicka på **registrerings-eller logga in**, klickar du på principen och klicka på Redigera (till exempel ”b2c\_1\_logga\_upp\_logga\_i”).
3. Klicka på **Page UI anpassning** och sedan **enhetliga sidan för registrering eller inloggning**.
4. Visa/Dölj de **Använd anpassad sida** växla till **Ja**. I den **anpassad sida URI** anger `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Klicka på **OK**.
5. Klicka på **registreringssida för lokalt konto**. Visa/Dölj de **Använd anpassad mall** växla till **Ja**. I den **anpassad sida URI** anger `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Upprepa samma steg för den **registreringssida för socialt konto**.
   Klicka på **OK** två gånger för att stänga bladen för UI-anpassning.
7. Klicka på **Spara**.

Nu kan du prova att använda en anpassad princip. Du kan använda ditt eget program eller Azure AD B2C-playground om du vill, men du kan också bara klicka på **kör nu** i principbladet. Markera programmet i den nedrullningsbara listrutan och välj lämplig omdirigerings-URI. Klicka på den **kör nu** knappen. En ny webbläsarflik öppnas och du kan gå igenom användarupplevelsen av registrering för ditt program med det nya innehållet på plats!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Ladda upp exempel-innehåll till Azure Blob Storage
Om du vill använda Azure Blob Storage som värd för ditt innehåll kan du skapa ditt eget lagringskonto och använda vår B2C hjälpverktyg för att överföra dina filer.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **+ ny** > **Data + lagring** > **lagringskonto**. Du behöver en Azure-prenumeration att skapa ett Azure Blob Storage-konto. Du kan registrera dig gratis på den [Azure-webbplatsen](https://azure.microsoft.com/pricing/free-trial/).
3. Ange en **namn** för lagrings-konto (till exempel ”contoso”) och välj lämpliga val för **prisnivå**, **resursgrupp** och  **Prenumeration**. Se till att du har den **fäst på startsidan** alternativet är markerat. Klicka på **Skapa**.
4. Gå tillbaka till startsidan och klicka på det lagringskonto som du nyss skapade.
5. I den **sammanfattning** klickar du på **behållare**, och klicka sedan på **+ Lägg till**.
6. Ange en **namn** för behållare (till exempel ”b2c”) och välj **Blob** som den **åtkomsttyp**. Klicka på **OK**.
7. Den behållare som du har skapat visas i listan på den **Blobar** bladet. Anteckna URL: en för behållaren. till exempel det bör se ut ungefär så `https://contoso.blob.core.windows.net/b2c`. Stäng den **Blobar** bladet.
8. På bladet storage-konto klickar du på **nycklar** och anteckna värdena för den **Lagringskontonamn** och **primära åtkomstnyckel** fält.

> [!NOTE]
> **Primär åtkomstnyckel** är en viktig säkerhetsuppgift för autentisering.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Ladda ned verktyget och exempel hjälpfiler
Du kan ladda ned den [Azure Blob Storage helper-verktyget och exempel filer som en .zip-fil](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) eller klona från GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Den här lagringsplatsen innehåller en `sample_templates\wingtip` directory som innehåller exempel HTML, CSS och bilder. För dessa mallar att referera till ditt eget Azure Blob Storage-konto, behöver du redigera HTML-filer. Öppna `unified.html` och `selfasserted.html` och Ersätt alla förekomster av `https://localhost` med Webbadressen för en egen behållare som du skrev ned i föregående steg. Du måste använda den absoluta sökvägen till HTML-filer eftersom i det här fallet HTML hanteras av Azure AD under domänen `tenantname.b2clogin.com`.

### <a name="upload-the-sample-files"></a>Ladda upp exempelfilerna
På samma lagringsplats, packa upp `B2CAzureStorageClient.zip` och kör den `B2CAzureStorageClient.exe` filen i. Det här programmet kommer bara överför alla filer i katalogen som du anger till ditt lagringskonto och aktivera CORS-åtkomst för dessa filer. Om du har följt stegen ovan kommer det nu att peka HTML och CSS-filer till ditt lagringskonto. Observera att namnet på ditt lagringskonto är den del som föregår `blob.core.windows.net`, till exempel `contoso`. Du kan kontrollera att innehållet har laddats upp korrekt genom att komma åt `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` i en webbläsare. Också använda [ http://test-cors.org/ ](http://test-cors.org/) att se till att innehållet är nu CORS aktiverat. (Leta efter ”XHR status: 200” i resultatet.)

### <a name="customize-your-policy-again"></a>Anpassa din princip igen
Nu när du har överfört exemplen till ditt eget lagringskonto, måste du redigera principen för registrering om du vill referera till den. Upprepa stegen från den [”anpassa din princip”](#customize-your-policy) avsnittet ovan, med ditt eget lagringskonto URL: er. Exempelvis kan platsen för din `unified.html` filen skulle vara `<url-of-your-container>/wingtip/unified.html`.

Nu kan du använda den **kör nu** knapp eller ditt eget program att köra principen igen. Resultatet bör se nästan exakt samma--du använde samma prov HTML och CSS i båda fallen. Men dina principer nu refererar till en egen instans av Azure Blob Storage och du kan redigera och ladda upp filer igen, som finns. Mer information om hur du anpassar HTML och CSS i den [Huvudartikel för UI-anpassning](active-directory-b2c-reference-ui-customization.md).

