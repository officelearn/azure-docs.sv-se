---
title: Anpassa ett gränssnitt genom att använda anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om hur du anpassar ett användargränssnitt (UI) samtidigt som du använder anpassade principer i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9908a7cf96c56e414e0a8d7faea0352b60214ea4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446171"
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: Konfigurera anpassning av Användargränssnittet i en anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du har slutfört den här artikeln har du en anpassad princip för registrering och logga in med ditt varumärke och utseende. Med Azure Active Directory B2C (Azure AD B2C), du får nästan fullständig kontroll över HTML och CSS-innehåll som visas för användarna. När du använder en anpassad princip kan konfigurera du anpassning av Användargränssnittet i XML istället för att använda kontroller i Azure-portalen. 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du slutföra [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

## <a name="page-ui-customization"></a>Anpassning av sid-UI

Med hjälp av funktionen sida Användargränssnittet anpassning, kan du anpassa utseendet och känslan av en anpassad princip. Du kan också ha varumärke och visual konsekvens mellan programmet och Azure AD B2C.

Så här fungerar det: Azure AD B2C körs koden i din kunds webbläsare och använder en modern lösning som kallas [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/). Först måste ange du en URL i den anpassade principen med anpassade HTML-innehåll. Azure AD B2C sammanfogar UI-element med HTML-innehåll som har lästs in från din URL och visar sidan för kunden.

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

## <a name="create-a-container"></a>Skapa en behållare

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

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Ändra din anpassade princip för registrering eller inloggning

Under den översta *\<TrustFrameworkPolicy\>* indatatagg, bör du hitta *\<BuildingBlocks\>* tagg. I den *\<BuildingBlocks\>* taggar, lägga till en *\<ContentDefinitions\>* tagg genom att kopiera i följande exempel. Ersätt *your_storage_account* med namnet på ditt lagringskonto.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Ladda upp din uppdaterade anpassad princip

1. I den [Azure-portalen](https://portal.azure.com), [växla till samma kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna sedan den **Azure AD B2C** bladet.
2. Klicka på **alla principer**.
3. Klicka på **överföra princip**.
4. Ladda upp `SignUpOrSignin.xml` med den *\<ContentDefinitions\>* tagg som du lagt till tidigare.

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
