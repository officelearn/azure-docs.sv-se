---
title: Språkanpassning i Azure Active Directory B2C
description: Läs mer om hur du anpassar språkupplevelsen i dina användarflöden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b3af812b2b78c276b5345b9b19226e6e1dba80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185768"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Språkanpassning i Azure Active Directory B2C

Med språkanpassning i Azure Active Directory B2C (Azure AD B2C) kan ditt användarflöde anpassas till olika språk som passar dina kunders behov. Microsoft tillhandahåller översättningar för [36 språk,](#supported-languages)men du kan också tillhandahålla egna översättningar för alla språk. Även om din upplevelse endast tillhandahålls för ett enda språk kan du anpassa all text på sidorna.

## <a name="how-language-customization-works"></a>Så här fungerar språkanpassning

Du använder språkanpassning för att välja vilka språk ditt användarflöde är tillgängligt i. När funktionen har aktiverats kan du ange `ui_locales`frågesträngparametern från programmet. När du ringer till Azure AD B2C översätts sidan till det språk som du har angett. Den här typen av konfiguration ger dig fullständig kontroll över språken i användarflödet och ignorerar språkinställningarna i kundens webbläsare.

Du kanske inte behöver den nivån av kontroll över vilka språk kunden ser. Om du inte tillhandahåller `ui_locales` en parameter styrs kundens upplevelse av webbläsarens inställningar. Du kan fortfarande styra vilka språk ditt användarflöde översätts till genom att lägga till det som ett språk som stöds. Om en kunds webbläsare är inställd på att visa ett språk som du inte vill stödja visas det språk som du valde som standard i kulturer som stöds i stället.

* **ui-språk angivna språk:** När du har aktiverat språkanpassning översätts användarflödet till det språk som anges här.
* **Webbläsarbeläst**språk `ui_locales` : Om ingen parameter har angetts översätts ditt användarflöde till det webbläsarbestävnade språket, *om språket stöds*.
* **Standardspråk för principen**: Om webbläsaren inte anger ett språk, eller anger ett språk som inte stöds, översätts användarflödet till standardspråket för användarflödet.

> [!NOTE]
> Om du använder anpassade användarattribut måste du ange dina egna översättningar. Mer information finns i [Anpassa strängarna](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Stöd begärda språk för ui_locales

Principer som skapades före den allmänna tillgängligheten för språkanpassning måste aktivera den här funktionen först. Principer och användarflöden som har skapats efter att språkanpassning aktiverat som standard.

När du aktiverar språkanpassning på ett användarflöde kan du styra `ui_locales` språket i användarflödet genom att lägga till parametern.

1. I din Azure AD B2C-klient väljer du **Användarflöden**.
1. Klicka på det användarflöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj **Aktivera språkanpassning**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Välj vilka språk i användarflödet som är aktiverade

Aktivera en uppsättning språk för ditt användarflöde som ska översättas `ui_locales` till på begäran av webbläsaren utan parametern.

1. Kontrollera att ditt användarflöde har aktiverat språkanpassning från tidigare instruktioner.
1. På sidan **Språk** för användarflödet väljer du ett språk som du vill stödja.
1. Ändra Aktiverad till **Ja**i **egenskapsfönstret** .
1. Välj **Spara** högst upp i egenskapsfönstret.

>[!NOTE]
>Om `ui_locales` en parameter inte anges översätts sidan till kundens webbläsarspråk endast om den är aktiverad.
>

## <a name="customize-your-strings"></a>Anpassa dina strängar

Med språkanpassning kan du anpassa valfri sträng i användarflödet.

1. Kontrollera att ditt användarflöde har aktiverat språkanpassning från föregående instruktioner.
1. På sidan **Språk** för användarflödet väljer du det språk som du vill anpassa.
1. Markera den sida som du vill redigera under **filer på sidnivå**och resurser.
1. Välj **Hämta standardvärden** (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).

De här stegen ger dig en JSON-fil som du kan använda för att börja redigera dina strängar.

### <a name="change-any-string-on-the-page"></a>Ändra en sträng på sidan

1. Öppna JSON-filen som hämtats från tidigare instruktioner i en JSON-redigerare.
1. Leta reda på det element som du vill ändra. Du kan `StringId` hitta för strängen du letar efter `Value` eller leta efter det attribut som du vill ändra.
1. Uppdatera `Value` attributet med det du vill visa.
1. För varje sträng som du `Override` vill `true`ändra ändrar du till .
1. Spara filen och ladda upp ändringarna. (Du hittar uppladdningskontrollen på samma plats som där du hämtade JSON-filen.)

> [!IMPORTANT]
> Om du behöver åsidosätta en sträng `Override` måste `true`du ange värdet till . Om värdet inte ändras ignoreras posten.

### <a name="change-extension-attributes"></a>Ändra tilläggsattribut

Om du vill ändra strängen för ett anpassat användarattribut, eller om du vill lägga till ett i JSON, är den i följande format:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Ersätt `<ExtensionAttribute>` med namnet på ditt anpassade användarattribut.

Ersätt `<ExtensionAttributeValue>` med den nya sträng som ska visas.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Ange en lista med värden med hjälp av LocalizedCollections

Om du vill ange en uppsättning lista med värden `LocalizedCollections` för svar måste du skapa ett attribut. `LocalizedCollections`är en `Name` samling `Value` och par. Ordern för artiklarna blir den ordning de visas. Så `LocalizedCollections`här lägger du till :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`är det användarattribut som det här `LocalizedCollections` attributet är ett svar på.
* `Name`är det värde som visas för användaren.
* `Value`är vad som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Ladda upp dina ändringar

1. När du har slutfört ändringarna i din JSON-fil går du tillbaka till din B2C-klient.
1. Välj **Användarflöden** och klicka på det användarflöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj det språk som du vill översätta till.
1. Välj den sida där du vill tillhandahålla översättningar.
1. Markera mappikonen och välj den JSON-fil som ska laddas upp.

Ändringarna sparas automatiskt i användarflödet.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassa sidgränssnittet med hjälp av språkanpassning

Det finns två sätt att lokalisera HTML-innehållet. Ett sätt är att aktivera [språkanpassning](user-flow-language-customization.md). Om du aktiverar den här funktionen kan Azure AD `ui-locales`B2C vidarebefordra Parametern OpenID Connect till slutpunkten. Innehållsservern kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som är språkspecifika.

Du kan också hämta innehåll från olika platser baserat på det språk som används. I den CORS-aktiverade slutpunkten kan du ställa in en mappstruktur som värd för innehåll för specifika språk. Du ringer den rätta om du använder `{Culture:RFC5646}`jokertecknets värde. Anta till exempel att det här är din anpassade URI:a sida:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Du kan läsa `fr`in sidan i . När sidan hämtar HTML- och CSS-innehåll drar den från:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Lägga till anpassade språk

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för. Du måste ange översättningar för alla strängar i användarflödet. Språk- och språkkoder är begränsade till dem i ISO 639-1-standarden.

1. I din Azure AD B2C-klient väljer du **Användarflöden**.
2. Klicka på användarflödet där du vill lägga till egna språk och klicka sedan på **Språk**.
3. Välj **Lägg till anpassat språk** högst upp på sidan.
4. I kontextfönstret som öppnas identifierar du vilket språk du tillhandahåller översättningar för genom att ange en giltig språkkod.
5. För varje sida kan du hämta en uppsättning åsidosättningar för engelska och arbeta med översättningarna.
6. När du är klar med JSON-filerna kan du ladda upp dem för varje sida.
7. Välj **Aktivera**och ditt användarflöde kan nu visa det här språket för användarna.
8. Spara språket.

>[!IMPORTANT]
>Du måste antingen aktivera de anpassade språken eller ladda upp åsidosättningar för det innan du kan spara.

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-as-overrides"></a>Anpassningsetiketter för sidgränssnitt som åsidosättningar

När du aktiverar språkanpassning sparas dina tidigare redigeringar för etiketter med anpassning av sidgränssnittet i en JSON-fil för engelska (sv). Du kan fortsätta att ändra etiketter och andra strängar genom att ladda upp språkresurser i språkanpassningen.

### <a name="up-to-date-translations"></a>Aktuella översättningar

Microsoft strävar efter att tillhandahålla de senaste översättningarna för din användning. Microsoft förbättrar kontinuerligt översättningar och håller dem i överensstämmelse för dig. Microsoft identifierar buggar och ändringar i den globala terminologin och gör uppdateringar som fungerar sömlöst i ditt användarflöde.

### <a name="support-for-right-to-left-languages"></a>Stöd för språk från höger till vänster

Microsoft tillhandahåller för närvarande inte stöd för språk som inte är till vänster. Du kan åstadkomma detta genom att använda anpassade språk och använda CSS för att ändra hur strängarna visas. Om du behöver den här funktionen kan du rösta på den på [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Översättningar av leverantörer av social identitet

Microsoft tillhandahåller `ui_locales` OIDC-parametern till sociala inloggningar. Men vissa sociala identitetsleverantörer, inklusive Facebook och Google, hedrar dem inte.

### <a name="browser-behavior"></a>Beteende i webbläsaren

Chrome och Firefox båda begäran om deras fasta språk. Om det är ett språk som stöds visas det före standardvärdet. Microsoft Edge begär för närvarande inte ett språk och går direkt till standardspråket.

## <a name="supported-languages"></a>Språk som stöds

Azure AD B2C innehåller stöd för följande språk. Användarflödesspråk tillhandahålls av Azure AD B2C. MFA-meddelandespråken (multifaktorautentisering) tillhandahålls av [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Språk              | Språkkod | Användarflöden         | MFA-meddelanden  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabiska                | Ar            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Bulgariska             | Bg            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Bangla                | Bn            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Katalanska               | ca ( ca )            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Tjeckiska                 | Cs            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Danska                | da            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Tyska                | de            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Grekiska                 | El            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Svenska               | en            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Spanska               | ES            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Estniska              | Et            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Baskiska                | Eu            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Finska               | fi            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Franska                | fr            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Galiciska              | Gl            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Gujarati              | Gu            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Hebreiska                | Han            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Hej            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kroatiska              | tim            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Ungerska             | Hu            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Indonesiska            | id            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Italienska               | it            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Japanska              | ja            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kazakiska                | Kk            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kannada               | Kn            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Koreansk                | Ko            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Litauiska            | Lt            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Lettiska               | Lv            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Malayalam             | Ml            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Marathi               | Mr            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Malajiska                 | Ms            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Norska Bokmal      | Nb            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Nederländska                 | nl            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Norska             | nej            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Punjabi               | Pa            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Polska                | Pl            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Portugisiska – Brasilien   | pt-br         | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Portugisiska – Portugal | pt-pt         | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Rumänska              | Ro            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Ryska               | ru            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Slovakiska                | Sk            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Slovenska             | Sl            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Serbiska - kyrilliska    | sr-cryl-cs    | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Serbiska - latin       | sr-latn-cs    | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Svenska               | sv            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Tamilska                 | ta            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![ja](./media/user-flow-language-customization/yes.png) | ![nej](./media/user-flow-language-customization/no.png) |
| Thailändska                  | Th            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Turkiska               | Tr            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Ukrainska             | Storbritannien            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Vietnamesiska            | vi            | ![nej](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kinesiska - förenklad  | zh-hans (olikartade)       | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kinesiska - Traditionell | zh-hant (olika)       | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
