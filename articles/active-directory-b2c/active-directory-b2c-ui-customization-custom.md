---
title: Anpassa användargränssnittet i ditt program med en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om hur du anpassar ett användargränssnitt som använder en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f36d08a397836f17ec25a61e77cb1db5ce10b9d4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945068"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Anpassa användargränssnittet i ditt program med en anpassad princip i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du har slutfört den här artikeln har du en anpassad princip för registrering och logga in med ditt varumärke och utseende. Med Azure Active Directory B2C (Azure AD B2C), du får nästan fullständig kontroll över HTML och CSS-innehåll som visas för användarna. När du använder en anpassad princip kan konfigurera du anpassning av Användargränssnittet i XML istället för att använda kontroller i Azure-portalen. 

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

## <a name="page-ui-customization"></a>Anpassning av sid-UI

Med hjälp av funktionen sida Användargränssnittet anpassning, kan du anpassa utseendet och känslan av en anpassad princip. Du kan även hålla varumärke och grafik konsekventa mellan programmet och Azure AD B2C.

Så här fungerar det: Azure AD B2C körs koden i din kunds webbläsare och använder en modern lösning som kallas [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/). Först måste ange du en URL i den anpassade principen med anpassade HTML-innehåll. Azure AD B2C sammanfogar UI-element med HTML-innehåll som läses in från din URL och visar sedan sidan för kunden.

## <a name="create-your-html5-content"></a>Skapa din HTML5 innehåll

Skapa HTML innehåll med varumärke Produktnamn i rubriken.

1. Kopiera följande HTML-kodfragment. Det är rätt HTML5 med ett tomt element kallas *\<div id = ”-api”\>\</div\>* inom den *\<brödtext\>* taggar. Det här elementet anger där Azure AD B2C-innehåll som ska infogas.

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
   >Användning av JavaScript är för närvarande blockerad för anpassning av säkerhetsskäl.

2. Klistra in det kopierade kodfragmentet i en textredigerare och spara filen som *anpassa ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob storage-konto

>[!NOTE]
> I den här artikeln använder vi Azure Blob storage som värd för vårt innehåll. Du kan välja att vara värd för ditt innehåll på en webbserver, men du måste [aktivera CORS på webbservern](https://enable-cors.org/server.html).

Om du vill vara värd för den här HTML-innehåll i Blob storage, gör du följande:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På den **Hub** menyn och välj **New** > **Storage** > **lagringskonto**.
3. Ange ett unikt **namn** för ditt lagringskonto.
4. **Distributionsmodellen** kan förbli **Resource Manager**.
5. Ändra **typ av konto** till **Blob-lagring**.
6. **Prestanda** kan förbli **Standard**.
7. **Replikering** kan förbli **RA-GRS**.
8. **Åtkomstnivå** kan förbli **frekvent**.
9. **Kryptering av lagringstjänst** kan förbli **inaktiverad**.
10. Välj en **prenumeration** för ditt lagringskonto.
11. Skapa en **resursgrupp** eller välj en befintlig.
12. Välj den **geografisk plats** för ditt lagringskonto.
13. Skapa lagringskontot genom att klicka på **Skapa**.  
    När distributionen är klar kan den **lagringskonto** bladet öppnas automatiskt.

## <a name="create-a-container"></a>Skapa en container

Om du vill skapa en offentlig behållare i Blob storage, gör du följande:

1. Klicka på den **översikt** fliken.
2. Klicka på **behållare**.
3. För **namn**, typ **$root**.
4. Ange **åtkomsttyp** till **Blob**.
5. Klicka på **$root** att öppna den nya behållaren.
6. Klicka på **Överför**.
7. Klicka på mappikonen bredvid **Välj en fil**.
8. Gå till **anpassa ui.html**, som du skapade tidigare i den [Page UI anpassning](#the-page-ui-customization-feature) avsnittet.
9. Klicka på **Överför**.
10. Välj den blob som anpassa ui.html som du överförde.
11. Bredvid **URL**, klickar du på **kopiera**.
12. Klistra in den kopierade Webbadressen i en webbläsare och gå till webbplatsen. Om platsen inte är tillgänglig, kontrollera att behållaren åtkomsttyp anges till **blob**.

## <a name="configure-cors"></a>Konfigurera CORS

Konfigurera Blob-lagring för Cross-Origin Resource Sharing genom att göra följande:

>[!NOTE]
>Vill du prova att använda funktionen för anpassning av Användargränssnittet med hjälp av våra exempel HTML och CSS innehåll? Vi tillhandahåller [en enkel hjälpverktyg](active-directory-b2c-reference-ui-customization-helper-tool.md) som överför och konfigurerar vår exemplen på Blob storage-kontot. Om du använder verktyget kan gå vidare till [ändra din anpassade princip för registrering eller inloggning](#modify-your-sign-up-or-sign-in-custom-policy).

1. På den **Storage** bladet under **inställningar**öppnar **CORS**.
2. Klicka på **Lägg till**.
3. För **tillåtna ursprung**, skriva en asterisk (\*).
4. I den **tillåtna verb** listrutan, Välj **hämta** och **alternativ**.
5. För **tillåtna huvuden**, skriva en asterisk (\*).
6. För **exponerade rubriker**, skriva en asterisk (\*).
7. För **högsta ålder (sekunder)**, typ **200**.
8. Klicka på **Lägg till**.

## <a name="test-cors"></a>Testa CORS

Verifiera att du är redo genom att göra följande:

1. Gå till den [www.test-cors.org](http://www.test-cors.org/) webbplats, och klistra in URL: en i den **Remote URL** box.
2. Klicka på **skicka begäran**.  
    Om du får ett fel, se till att din [CORS-inställningar](#configure-cors) är korrekta. Du kan också behöva rensa webbläsarens cache eller öppna en privat-webbläsarsession genom att trycka på Ctrl + Skift + P.

## <a name="modify-the-extensions-file"></a>Ändra tilläggsfilen

Om du vill konfigurera anpassningar du kopiera den **ContentDefinition** och dess underordnade element från bas-filen till tilläggsfilen.

1. Öppna filen bas i din princip. Till exempel *TrustFrameworkBase.xml*.
2. Sök efter och kopiera hela innehållet i den **ContentDefinitions** element.
3. Öppna tilläggsfilen. Till exempel *TrustFrameworkExtensions.xml*. Sök efter den **BuildingBlocks** element. Om elementet inte finns kan du lägga till den.
4. Klistra in hela innehållet i den **ContentDefinitions** element som du kopierade som underordnad till den **BuildingBlocks** element. 
5. Sök efter den **ContentDefinition** element som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.
6. Ändra värdet för **LoadUri** till Webbadressen för HTML-fil som du laddade upp till lagring. Till exempel ”https://mystore1.azurewebsites.net/b2c/customize-ui.html.
    
    En anpassad princip bör se ut så här:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. Spara tilläggsfilen.

## <a name="upload-your-updated-custom-policy"></a>Ladda upp din uppdaterade anpassad princip

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **Identitetsramverk**.
2. Klicka på **alla principer**.
3. Klicka på **överföra princip**.
4. Ladda upp tilläggsfilen som du tidigare har ändrats.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av **kör nu**

1. På den **Azure AD B2C** gå till bladet **alla principer**.
2. Välj den anpassade principen som du laddat upp och klicka på den **kör nu** knappen.
3. Du ska kunna registrera sig med hjälp av en e-postadress.

## <a name="reference"></a>Referens

Du hittar exempelmallar för anpassning av Användargränssnittet här:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Sample_templates/wingtip-mappen innehåller följande HTML-filer:

| HTML5-mall | Beskrivning |
|----------------|-------------|
| *phonefactor.HTML* | Använd den här filen som en mall för en sida för multifaktorautentisering. |
| *resetpassword.html* | Använd den här filen som en mall för en sida för glömt lösenord. |
| *selfasserted.html* | Använd den här filen som en mall för en registreringssida för socialt konto, en registreringssida för lokalt konto eller ett lokalt konto på inloggningssidan. |
| *Unified.HTML* | Använd den här filen som en mall för en enhetlig sida för registrering eller inloggning. |
| *updateprofile.html* | Använd den här filen som en mall för en uppdatering profilsida. |

I den [ändra anpassad princip för registrering eller inloggning-avsnittet](#modify-your-sign-up-or-sign-in-custom-policy), du har konfigurerat innehållsdefinition för `api.idpselections`. Den fullständiga uppsättningen innehåll Definitions-ID som identifieras av Azure AD B2C-identitetsramverk och deras beskrivningar finns i följande tabell:

| Innehållsdefinition-ID | Beskrivning | 
|-----------------------|-------------|
| *api.error* | **Felsida**. Den här sidan visas när ett undantag eller ett fel har påträffats. |
| *API.idpselections* | **Sida för val av identitet**. Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja mellan under inloggning. Dessa alternativ är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *API.idpselections.Signup* | **Identitets-provider-markeringen för registrering**. Den här sidan innehåller en lista över identitetsleverantörer som användaren kan välja mellan under registreringen. Dessa alternativ är enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook och Google + eller lokala konton. |
| *api.localaccountpasswordreset* | **Sida för glömt lösenord**. Den här sidan innehåller ett formulär som användaren måste slutföra för att initiera en lösenordsåterställning.  |
| *API.localaccountsignin* | **Lokalt konto på inloggningssidan**. Den här sidan innehåller ett formulär för att logga in med ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla ett textinmatningsrutan och lösenordsruta. |
| *API.localaccountsignup* | **Registreringssida för lokalt konto**. Den här sidan innehåller en fyllt i registreringsformuläret för att registrera dig för ett lokalt konto som är baserad på en e-postadress eller ett användarnamn. Formuläret kan innehålla olika indatakontroller, till exempel en textruta, en lösenordsruta, en alternativknapp, flervals-listrutorna och välja flera kryssrutor. |
| *API.phonefactor* | **Multifaktorautentiseringssidan**. På den här sidan verifiera användare sina telefonnummer (med hjälp av text eller röst) under registrering eller inloggning. |
| *api.selfasserted* | **Registreringssida för socialt konto**. Den här sidan innehåller en fyllt i registreringsformuläret som användare måste slutföra när de registrerar sig med hjälp av ett befintligt konto från en social identitetsprovider, till exempel Facebook eller Google +. Den här sidan liknar föregående socialt konto registreringssidan, förutom inmatningsfält för lösenord. |
| *api.selfasserted.profileupdate* | **Uppdatera profilsida**. Den här sidan innehåller ett formulär som användarna kan använda för att uppdatera sina profiler. Den här sidan liknar socialt konto registreringssidan, förutom inmatningsfält för lösenord. |
| *API.signuporsignin* | **Sida för enhetlig registrering eller inloggning**. Den här sidan hanterar både registrering och inloggning av användare som kan använda enterprise identitetsleverantörer, sociala identitetsleverantörer, till exempel Facebook eller Google + eller lokala konton.  |

## <a name="next-steps"></a>Nästa steg

Läs mer om UI-element som kan anpassas i [referensguiden för anpassning av Användargränssnittet för inbyggda principer](active-directory-b2c-reference-ui-customization.md).
