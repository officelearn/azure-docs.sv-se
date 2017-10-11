---
title: "Azure Active Directory B2C: Med språket anpassning | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: Med språket anpassning

>[!NOTE] 
>Den här funktionen är tillgänglig som förhandsversion.  Det rekommenderas att du använder en Testklient när du använder den här funktionen.  Vi planerar inte ändringar bryta från förhandsversionen av versionen för allmän tillgänglighet, men vi förbehåller sig rätten att göra dessa ändringar för att förbättra funktionen.  Ge feedback om din upplevelse och hur vi kan göra den bättre när du har haft möjlighet att prova funktionen.  Du kan ge feedback via Azure-portalen med verktyget ansikte ansikte längst upp till höger.   Om det är ett företagskrav som du kan publicera med hjälp av den här funktionen under fasen preview berätta för oss dina scenarier och vi kan tillhandahålla rätt information och hjälp.  Du kan kontakta oss på [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).

Anpassning av språk kan du ändra dina användare resa till ett annat språk efter kundens behov.  Vi tillhandahåller översättningar för 36 språk (se [ytterligare information](#additional-information)).  Även om din upplevelse finns bara för ett språk, kan du anpassa text på sidorna som passar dina behov.  

## <a name="how-does-language-customization-work"></a>Hur fungerar språk anpassning?
Anpassning av språk kan du välja vilka språk som användaren-transporten finns i.  När funktionen är aktiverad, kan du ange frågesträngparametern ui_locales, från ditt program.  När du anropar i Azure AD B2C översätta vi sidan språk som du har angett.  Med hjälp av typen av konfiguration ger fullständig kontroll över språk i dina användare resa och ignorerar språkinställningar för kundens webbläsare. Alternativt kanske du inte behöver nivån kontroll över vilka språk som kunden finns.  Om du inte anger en parameter för ui_locales beror på kundupplevelsen sina webbläsarinställningar.  Du kan fortfarande styra vilka språk som dina användare resa översätts till genom att lägga till som ett språk som stöds.  Om en kund webbläsare är inställd på att visa ett språk som du inte vill stöder, är det språk du valt som standard i stöds kulturer visas i stället.

1. **UI-språk angett språk** -när du har aktiverat språket anpassning användaren-transporten översätts till det språk som anges här
2. **Webbläsaren begärda språket** -om ingen ui-språk har angetts, det innebär att webbläsaren begärda språk, **om den ingick i språk som stöds**
3. **Principen standardspråk** -om webbläsaren inte ange ett språk eller anger en som inte stöds, det innebär att standardspråk för principen

>[!NOTE]
>Om du använder anpassade användarattribut, måste du ange egna översättningar. Se ”[anpassa din strängar](#customize-your-strings)” mer information.
>

## <a name="support-uilocales-requested-languages"></a>Stöd för ui_locales begärda språk 
Du kan nu styra språket för transporten användare genom att lägga till parametern ui_locales genom att aktivera språk anpassning på en princip.
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure-portalen.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navigera till en princip som du vill aktivera för översättningar.
3. Klicka på **språk anpassning**.
4. Läst varningen noggrant.  När du har aktiverat, kan du inaktivera 'Språk anpassning'.
5. Aktivera funktionen och klickar på **OK**.
6. Spara din princip på det övre vänstra hörnet av din **Redigera princip** bladet.

## <a name="select-which-languages-your-user-journey-supports"></a>Välj vilka språk som har stöd för dina användare resa 
Skapa en lista över tillåtna språk för dina användare resa översättas i när ui_locales-parameter inte har angetts.
1. Kontrollera din princip har 'Språk anpassning' har aktiverat från föregående anvisningarna.
2. Från din **Redigera princip** bladet väljer **språk anpassning**.
3. Du kommer till din **språk som stöds** bladet.  Här kan du välja **lägga till språk**.
4. Välj alla språk som ska stödjas.  

>[!NOTE]
>Om en ui_locales-parameter inte har angetts, sedan översätts sidan till kundens webbläsarens språk om det finns i listan
>

5. Klicka på **Ok** längst ned
6. Stäng den **språk anpassning** bladet och **spara** din princip.

## <a name="customize-your-strings"></a>Anpassa din strängar
'Språk anpassning, kan du anpassa valfri sträng i användar-transporten.
1. Kontrollera din princip har 'Språk anpassning' har aktiverat från de föregående anvisningarna.
2. Från din **Redigera princip** bladet väljer **språk anpassning**.
3. Välj den vänstra navigeringsmenyn **hämtningar**.
4. Välj sidan som du vill redigera.
5. Välj det språk som du vill redigera för i den nedrullningsbara listrutan.
6. Klicka på **Hämta**. 

Här får du en JSON-fil som du kan använda för att börja redigera din strängar.

### <a name="changing-any-string-on-the-page"></a>Ändra valfri sträng på sidan
1. Öppna JSON-fil som hämtas från föregående anvisningarna i en JSON-redigerare.
2. Hitta de element som du vill ändra.  Du hittar den `StringId` av den sträng som du letar efter eller leta efter den `Value` du vill ändra.
3. Uppdatering av `Value` attribut med vad som ska visas.
4. Spara filen och överföra dina ändringar.

### <a name="changing-extension-attributes"></a>Ändra tilläggsattribut
Om du vill ändra strängen för en anpassad användarattribut eller vill du lägga till ett JSON har följande format:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Om du måste åsidosätta en sträng, se till att ange den `Override` värde till `true`.  Om värdet inte är ändras ignoreras posten. 
>

Ersätt `<ExtensionAttribute>` med namnet på din anpassade attribut.  
Ersätt `<ExtensionAttributeValue>` med den nya strängen som ska visas.

### <a name="using-localizedcollections"></a>Med hjälp av LocalizedCollections
Om du vill ange en fast lista med värden för svar, måste du skapa en `LocalizedCollections`.  En `LocalizedCollections` är en matris med `Name` och `Value` par.  Den `Name` är vad som visas och `Value` är det som returneras i anspråket.  Att lägga till en `LocalizedCollections`, den har följande format:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": false,
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
>[!IMPORTANT]
>Om du måste åsidosätta en sträng, se till att ange den `Override` värde till `true`.  Om värdet inte är ändras ignoreras posten. 
>

* `ElementId`är användaren attribut som den här `LocalizedCollections` är ett svar på
* `Name`värdet som visas för användaren
* `Value`är det som returneras i anspråket när det här alternativet är markerat

### <a name="upload-your-changes"></a>Ladda upp dina ändringar
1. När du har slutfört ändringarna till JSON-fil, gå tillbaka till din B2C-klient.
2. Från din **Redigera princip** bladet väljer **språk anpassning**.
3. Välj den vänstra navigeringsmenyn **överföra innehåll**.
4. Välj sidan som du vill överföra ändringarna för.
5. Om du vill överföra ett språk som du tidigare har angett en JSON för måste du ta bort den föregående posten.  Du kan ta bort den genom att klicka på den `...` till höger på det språk och väljer **ta bort**.
6. Klicka på **Lägg till** längst upp till vänster.
7. Välj språk för JSON-filen.
8. Klicka på knappen till höger och bläddra efter en JSON-fil.
9. Klicka på den **överför** knappen längst ned på bladet.
10. Gå tillbaka till din **Redigera princip** bladet och klicka på **spara**.

## <a name="additional-information"></a>Ytterligare information
### <a name="recommendations-for-language-customization"></a>Rekommendationer för 'Språk anpassning'
Vi rekommenderar att du bara lägga till i poster till språkresurser efter strängar som du uttryckligen vill ersätta.  Vi framtvinga en storleksgräns till den fil som är kompilerad utanför alla JSON-översättningar.  Om filerna blir för stor, påverkar prestandan för din användare resa.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>Page UI anpassning etiketter tas bort när 'Språk anpassning' har aktiverats
När du aktiverar 'Språk anpassning' bort tidigare ändringar för etiketter med Page UI anpassning förutom anpassade attribut.  Den här ändringen görs för att undvika konflikter i där du kan redigera din strängar.  Du kan fortsätta att ändra etiketterna och andra strängar genom att överföra språkresurser i 'Språk anpassning'.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft strävar efter att tillhandahålla den senaste översättningar för användning
Vi kontinuerligt förbättra översättningar och lagra dem på efterlevnad för dig.  Kommer att identifiera buggar och ändringar i globala terminologi och göra de uppdateringar som kommer att fungera sömlöst i användar-transporten.
### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk
Det finns inget stöd för höger-till-vänster-språk, om du behöver den här funktionen Ta rösta för den här funktionen på [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Sociala identitet providern översättningar
Vi ger parametern ui_locales OIDC sociala inloggningar men de hanteras inte av vissa sociala identitetsleverantörer, inklusive Facebook och Google. 
### <a name="browser-behavior"></a>Webbläsarens beteende
Chrome och Firefox både begäran för sina Ange språk och om det är ett språk som stöds, visas innan.  Gräns för närvarande begär inte ett språk och går direkt till standardspråk.
### <a name="known-issues"></a>Kända problem
* Överför språkresurser för sidan MFA i en profil Redigera princip är inte tillgänglig.
* `LocalizedCollections`inte genereras för värden när det krävs av svarstyp
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>Vad händer om jag vill ha ett språk som inte stöds?
Vi planerar att tillhandahålla en utökning av den här funktionen som gör det möjligt att överföra en JSON-resurs mot 'anpassade språk'.  Funktionen kan du ange namn och språk-koden för alla språk och ange *alla* översättningar för det språket.  Om du behöver den här funktionen kan skicka oss din situation på [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>Vilka språk som stöds?

| Språk              | Språkkod |
|-----------------------|---------------|
| Bangla                | bn            |
| Tjeckiska                 | cs            |
| Danska                | da            |
| Tyska                | de            |
| Grekiska                 | el            |
| Svenska               | en            |
| Spanska               | es            |
| Finska               | fi            |
| Franska                | fr            |
| Gujarati              | Gu            |
| Hindi                 | Hej            |
| Kroatiska              | hr            |
| Ungerska             | hu            |
| Italienska               | it            |
| Japanska              | ja            |
| Kannada               | KN            |
| Koreanska                | ko            |
| Malayalam             | ml            |
| Marathi               | MR            |
| Malajiska                 | MS            |
| Norska bokmål      | nb            |
| Nederländska                 | nl            |
| Punjabi               | pa            |
| Polska                | pl            |
| Portugisiska – Brasilien   | pt-br         |
| Portugisiska – Portugal | pt-pt         |
| Rumänska              | ro            |
| Ryska               | ru            |
| Slovakiska                | Sk            |
| Svenska               | sv            |
| Tamilska                 | ta            |
| Telugu                | te            |
| Thai                  | TH            |
| Turkiska               | TR            |
| Kinesiska – förenklad  | zh-hans       |
| Kinesiska – traditionell | zh-hant       |
