---
title: "Anpassa ett gränssnitt med hjälp av anpassade principer - Azure AD B2C | Microsoft Docs"
description: "Lär dig mer om hur du anpassar ett användargränssnitt (UI) samtidigt som du använder anpassade principer i Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: c430b488016f038ed1d7a67a8d52c057df1ea40e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: Konfigurera anpassningar i en anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du har slutfört den här artikeln har du en anpassad princip för registrering och inloggning med märke och utseende. Med Azure Active Directory B2C (Azure AD B2C), du får nästan full kontroll över HTML- och CSS-innehåll som visas för användarna. När du använder en anpassad princip kan konfigurera du anpassning av Användargränssnittet i XML istället för att använda kontroller i Azure-portalen. 

## <a name="prerequisites"></a>Krav

Innan du kan slutföra [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

## <a name="page-ui-customization"></a>Anpassning av Page UI

Du kan anpassa utseendet och känslan av en anpassad princip med hjälp av sidan anpassning gränssnittsfunktionen. Du kan också upprätthålla märke och visual konsekvensen mellan dina program och Azure AD B2C.

Här är hur det fungerar: Azure AD B2C Kör koden i kundens webbläsare och använder en modern metod som kallas [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/). Först måste ange du en URL i en anpassad princip med anpassade HTML-innehåll. Azure AD B2C sammanfogas UI-element med HTML-innehåll som har lästs in från URL: en och visar sidan till kunden.

## <a name="create-your-html5-content"></a>Skapa din HTML5 innehåll

Skapa HTML innehåll med varumärken produktnamnet i rubriken.

1. Kopiera följande HTML-fragment. Det är korrekt HTML5 med ett tomt element kallas  *\<div id = ”api”\>\</div\>*  i den  *\<brödtext\>*  taggar. Det här elementet anger där Azure AD B2C-innehåll som ska infogas.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

   >[!NOTE]
   >Av säkerhetsskäl bör blockerat användningen av JavaScript för närvarande för anpassning.

2. Klistra in den kopierade fragment i en textredigerare och spara filen som *anpassa ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob storage-konto

>[!NOTE]
> I den här artikeln används Azure Blob storage som värd för innehållet. Du kan välja att vara värd för ditt innehåll på en webbserver, men du måste [aktivera CORS på din webbserver](https://enable-cors.org/server.html).

Om du vill vara värd för den här HTML-innehåll i Blob storage, gör du följande:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På den **hubb** väljer du **ny** > **lagring** > **lagringskonto**.
3. Ange ett unikt **namn** för ditt lagringskonto.
4. **Distributionsmodell** kan vara **Resource Manager**.
5. Ändra **konto typ** till **Blob storage**.
6. **Prestanda** kan vara **Standard**.
7. **Replikering** kan vara **RA-GRS**.
8. **Åtkomstnivå** kan vara **frekvent**.
9. **Lagringstjänstens kryptering** kan vara **inaktiverade**.
10. Välj en **prenumeration** för ditt lagringskonto.
11. Skapa en **resursgruppen** eller välj en befintlig.
12. Välj den **geografisk plats** för ditt lagringskonto.
13. Skapa lagringskontot genom att klicka på **Skapa**.  
    När distributionen är klar i **lagringskonto** blad öppnas automatiskt.

## <a name="create-a-container"></a>Skapa en behållare

Om du vill skapa en offentlig behållare i Blob storage, gör du följande:

1. Klicka på den **översikt** fliken.
2. Klicka på **behållare**.
3. För **namn**, typen **$root**.
4. Ange **komma åt typen** till **Blob**.
5. Klicka på **$root** att öppna den nya behållaren.
6. Klicka på **Överför**.
7. Klicka på mappikonen bredvid **Välj en fil**.
8. Gå till **anpassa ui.html**, som du skapade tidigare i den [Page UI anpassning](#the-page-ui-customization-feature) avsnitt.
9. Klicka på **Överför**.
10. Välj Anpassa ui.html blob som du överfört.
11. Bredvid **URL**, klickar du på **kopiera**.
12. Klistra in den kopierade Webbadressen i en webbläsare och gå till webbplatsen. Om platsen är tillgänglig, kontrollera åtkomst behållartypen anges till **blob**.

## <a name="configure-cors"></a>Konfigurera CORS

Konfigurera Blob storage för Cross-Origin Resource Sharing genom att göra följande:

>[!NOTE]
>Om du vill testa funktionen för anpassning av Användargränssnittet med hjälp av våra exempel HTML och CSS innehåll? Vi har samlat [ett enkelt kommandoradsverktyg](active-directory-b2c-reference-ui-customization-helper-tool.md) som överför och konfigurerar våra exemplen på Blob storage-konto. Om du använder verktyget kan du gå vidare till [ändra en anpassad princip för registrering eller inloggning](#modify-your-sign-up-or-sign-in-custom-policy).

1. På den **lagring** bladet under **inställningar**öppnar **CORS**.
2. Klicka på **Lägg till**.
3. För **tillåtna ursprung**, skriver du en asterisk (\*).
4. I den **tillåtna verb** listrutan, Välj **hämta** och **alternativ**.
5. För **tillåtna huvuden**, skriver du en asterisk (\*).
6. För **exponeras huvuden**, skriver du en asterisk (\*).
7. För **högsta ålder (sekunder)**, typen **200**.
8. Klicka på **Lägg till**.

## <a name="test-cors"></a>Testa CORS

Verifiera att du är redo genom att göra följande:

1. Gå till den [test cors.org](http://test-cors.org/) webbplats, och klistra in Webbadressen i den **fjärr-URL** rutan.
2. Klicka på **skicka begäran**.  
    Om du får ett felmeddelande, kontrollerar du att din [CORS-inställningarna](#configure-cors) är korrekta. Du kan också behöva rensa webbläsarens cacheminne eller öppna ett privat sessionen genom att trycka på Ctrl + Skift + P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Ändra en anpassad princip för registrering eller inloggning

Under den översta  *\<TrustFrameworkPolicy\>*  tagg, bör du hitta  *\<BuildingBlocks\>*  tagg. I den  *\<BuildingBlocks\>*  taggar, lägga till en  *\<ContentDefinitions\>*  taggen genom att kopiera följande exempel. Ersätt *your_storage_account* med namnet på ditt lagringskonto.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Ladda upp din uppdaterade anpassad princip

1. I den [Azure-portalen](https://portal.azure.com), [växla i samband med din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och sedan öppna den **Azure AD B2C** bladet.
2. Klicka på **alla principer**.
3. Klicka på **överföra princip**.
4. Överför `SignUpOrSignin.xml` med den  *\<ContentDefinitions\>*  tagg som du har lagt till tidigare.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av **kör nu**

1. På den **Azure AD B2C** gå till bladet **alla principer**.
2. Välj den anpassade principen som du överfört och klicka på den **kör nu** knappen.
3. Du ska kunna logga med hjälp av en e-postadress.

## <a name="reference"></a>Referens

Du kan hitta exempelmallarna för anpassning av Användargränssnittet här:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Mappen sample_templates/wingtip innehåller följande HTML-filer:

| HTML5-mall | Beskrivning |
|----------------|-------------|
| *phonefactor.HTML* | Använd den här filen som en mall för en multifaktorautentiseringssidan. |
| *ResetPassword.HTML* | Använd den här filen som en mall för en glömt lösenord. |
| *selfasserted.HTML* | Använd den här filen som en mall för en sociala konto registreringssidan, registreringssidan för lokalt konto eller ett lokalt konto-inloggningssida. |
| *Unified.HTML* | Använd den här filen som en mall för ett enhetlig registrering eller inloggning. |
| *updateprofile.HTML* | Använd den här filen som en mall för en uppdatering profilsida. |

I den [ändra anpassad princip för registrering eller inloggning-avsnittet](#modify-your-sign-up-or-sign-in-custom-policy), du har konfigurerat innehållsdefinitionen för `api.idpselections`. En fullständig uppsättning innehåll Definitions-ID som identifieras av Azure AD B2C identitet upplevelse framework och deras beskrivningar finns i följande tabell:

| Innehålls-ID: N | Beskrivning | 
|-----------------------|-------------|
| *API.Error* | **Felsidan**. Den här sidan visas när ett undantagsfel eller ett fel har påträffats. |
| *API.idpselections* | **Identity-providern på sidan**. Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja från under inloggningen. Dessa alternativ är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *API.idpselections.Signup* | **Identitet providern val för registrering**. Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja bland under registreringen. Dessa alternativ är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *API.localaccountpasswordreset* | **Har du glömt lösenordssidan**. Den här sidan innehåller ett formulär som användaren måste slutföra för att initiera en återställning av lösenord.  |
| *API.localaccountsignin* | **Lokalt konto inloggningssidan**. Den här sidan innehåller ett formulär för att logga in med ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Formuläret kan innehålla en textruta och inmatningsfält för lösenord. |
| *API.localaccountsignup* | **Lokalt konto registreringssidan**. Den här sidan innehåller en registreringsformuläret för att registrera dig för ett lokalt konto som baseras på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika inkommande kontroller, till exempel en textruta, inmatningsfält för lösenord, en alternativknapp, enkelval listrutorna och välja flera kryssrutor. |
| *API.phonefactor* | **Multifaktorautentiseringssidan**. På den här sidan verifiera användare sina telefonnummer (med hjälp av text- eller röst) under registrering eller inloggning. |
| *API.selfasserted* | **Sociala konto registreringssidan**. Den här sidan innehåller en registreringsformuläret som användare måste slutföra när de loggar med ett befintligt konto från en sociala identitetsleverantören, till exempel Facebook eller Google +. Den här sidan liknar föregående sociala konto registreringssidan, förutom fälten lösenord post. |
| *API.selfasserted.profileupdate* | **Uppdatera profilsida**. Den här sidan innehåller ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar sociala konto registreringssidan, förutom fälten lösenord post. |
| *API.signuporsignin* | **Enhetlig registrering eller inloggning sidan**. Den här sidan hanterar både registrering och inloggning av användare som kan använda enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton.  |

## <a name="next-steps"></a>Nästa steg

Mer information om UI-element som kan anpassas finns [referenshandbok för anpassning av Användargränssnittet för inbyggda principer](active-directory-b2c-reference-ui-customization.md).
