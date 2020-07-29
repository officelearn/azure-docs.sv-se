---
title: Problem med att konfigurera SSO för lösen ord för appar som inte är gallerier
description: Vanliga problem som uppstår när du konfigurerar enkel inloggning för lösen ord (SSO) för anpassade appar som inte finns i Azure AD-programgalleriet.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9620a6ad584f20a0956e6a29c89609d79832f4d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763456"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera enkel inloggning för lösen ord för ett program som inte är ett galleri program

I den här artikeln beskrivs vanliga problem som kan uppstå när du konfigurerar *enkel inloggning för lösen ord* (SSO) för en app som inte är en Galleri.

## <a name="capture-sign-in-fields-for-an-app"></a>Avbilda inloggnings fält för en app

Registrering av inloggnings fält stöds bara för HTML-aktiverade inloggnings sidor. Det stöds inte för inloggnings sidor som inte är standard, t. ex. sådana som använder Adobe Flash eller andra icke-HTML-aktiverade tekniker.

Det finns två sätt att registrera inloggnings fält för dina anpassade appar:

- **Automatisk insamling av inloggnings fält** fungerar bra med de flesta HTML-aktiverade inloggnings sidor, *om de använder välkända div-ID: n* för fälten användar namn och lösen ord. HTML på sidan klipps av för att hitta DIV-id: n som matchar vissa villkor. Metadata sparas så att de kan spelas upp till appen senare.

- **Registrering av manuellt inloggnings fält** används om app-leverantören *inte förser inloggnings fälten med inloggnings information*. Manuell fångst används också om leverantören *återger flera fält som inte kan identifieras automatiskt*. Azure Active Directory (Azure AD) kan lagra data för så många fält som finns på inloggnings sidan, om du anger var fälten finns på sidan.

Om det inte går att skapa automatiska inloggnings fält i allmänhet, kan du prova det manuella alternativet.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Registrera inloggnings fält automatiskt för en app

Följ dessa steg om du vill konfigurera lösenordsbaserad SSO med hjälp av automatisk registrering av inloggnings fält:

1. Öppna [Azure Portal](https://portal.azure.com/). Logga in som global administratör eller medadministratör.

2. I navigerings fönstret till vänster väljer du **alla tjänster** för att öppna tillägget Azure AD.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i listan **alla program** . Ange alternativet **Visa** som "alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har lästs in väljer du **enkel inloggning** i navigerings fönstret till vänster.

8. Välj **lösenordsbaserad inloggnings** läge.

9. Ange **inloggnings-URL**, som är webb adressen till sidan där användarna anger användar namn och lösen ord för att logga in. *Kontrol lera att inloggnings fälten är synliga på sidan för den URL som du anger*.

10. Välj **Spara**.

    Sidan kasseras automatiskt för rutorna användar namn och lösen ord. Nu kan du använda Azure AD för att på ett säkert sätt överföra lösen ord till appen med hjälp av åtkomst panelens webb läsar tillägg.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Registrera inloggnings fält manuellt för en app

Om du vill samla in inloggnings fält manuellt måste du ha åtkomst panelens webb läsar tillägg installerat. Det går inte heller att köra webbläsaren i *InPrivate*-, *Incognito*-eller *Private* -läge.

Information om hur du installerar tillägget finns i avsnittet [Installera åtkomst panelens webb läsar tillägg](#install-the-access-panel-browser-extension) i den här artikeln.

Följ dessa steg om du vill konfigurera lösenordsbaserad SSO för en app med hjälp av manuella inloggnings fält för registrering:

1. Öppna [Azure Portal](https://portal.azure.com/). Logga in som global administratör eller medadministratör.

2. I navigerings fönstret till vänster väljer du **alla tjänster** för att öppna tillägget Azure AD.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina appar.

   > [!NOTE] 
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i listan **alla program** . Ange alternativet **Visa** som "alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har lästs in väljer du **enkel inloggning** i navigerings fönstret till vänster.

8. Välj **lösenordsbaserad inloggnings** läge.

9. Ange **inloggnings-URL**, som är sidan där användarna anger användar namn och lösen ord för att logga in. *Kontrol lera att inloggnings fälten är synliga på sidan för den URL som du anger*.

10. Välj **Konfigurera inställningar för lösen ord för enkel inloggning för * &lt; APPNAME &gt; * **.

11. Välj **identifiera inloggnings fält manuellt**.

14. Välj **OK**.

15. Välj **Spara**.

16. Följ anvisningarna för att använda åtkomst panelen.

## <a name="troubleshoot-problems"></a>Felsöka problem

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Jag får ett fel meddelande om att det inte gick att hitta några inloggnings fält på den URL: en

Du får det här fel meddelandet när det inte går att identifiera inloggnings fält automatiskt. Försök att lösa problemet genom att försöka med manuell inloggnings fält identifiering. Se [inloggnings fälten manuellt för ett program](#manually-capture-sign-in-fields-for-an-app) i den här artikeln.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Jag får fel meddelandet "det går inte att spara enkel inloggnings konfiguration"

Det går sällan att uppdatera SSO-konfigurationen. Försök att lösa det här problemet genom att spara konfigurationen igen.

Om du fortfarande får felet kan du öppna ett support ärende. Ta med den information som beskrivs i [Visa Portal meddelande information](#view-portal-notification-details) och [Skicka meddelande information till en support tekniker för att få hjälp](#send-notification-details-to-a-support-engineer-to-get-help) avsnitt i den här artikeln.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Jag kan inte identifiera inloggnings fält manuellt för min app

Du kan observera följande beteenden när manuell identifiering inte fungerar:

- Processen för manuell inhämtning verkar fungera, men de insamlade fälten är inte korrekta.

- De korrekta fälten markeras inte när insamlings processen körs.

- Hämtnings processen tar dig till appens inloggnings sida som förväntat, men ingenting händer.

- Manuell inspelning verkar fungera, men SSO sker inte när användarna navigerar till appen från åtkomst panelen.

Om du upplever något av dessa problem kan du göra följande:

- Kontrol lera att du har den senaste versionen av åtkomst panelens webb läsar tillägg *installerat och aktiverat*. Se avsnittet [Installera åtkomst panels tillägg](#install-the-access-panel-browser-extension) i den här artikeln.

- Kontrol lera att din webbläsare inte är i *Incognito*, *InPrivate*eller *privat* läge under insamlings processen. Åtkomst panels tillägget stöds inte i de här lägena.

- Se till att användarna inte försöker logga in på appen från åtkomst panelen i *Incognito*, *InPrivate*eller *privat läge*.

- Försök att utföra manuell inhämtning igen. Se till att de röda markeringarna är över rätt fält.

- Om den manuella hämtningen verkar sluta svara eller om inloggnings sidan inte svarar, kan du försöka att utföra den manuella insamlingen igen. Men den här gången har du slutfört processen genom att trycka på F12-tangenten för att öppna webbläsarens utvecklarverktyg. Välj fliken **konsol** . Skriv **window. location = "* &lt; den inloggnings-URL som du angav när du konfigurerade &gt; appen*"** och tryck sedan på RETUR. Detta innebär en omdirigering av sidan som avslutar insamlings processen och lagrar de fält som har registrerats.

### <a name="contact-support"></a>Kontakta supporten

Om du fortfarande har problem kan du öppna ett ärende med Microsoft Support. Beskriv vad du försökte. Ta med den information som beskrivs i [Visa Portal meddelande information](#view-portal-notification-details) och [Skicka meddelande information till en support tekniker för att få hjälp](#send-notification-details-to-a-support-engineer-to-get-help) avsnitt i den här artikeln (om tillämpligt).

## <a name="install-the-access-panel-browser-extension"></a>Installera åtkomst panelens webb läsar tillägg

Följ de här stegen:

1. Öppna [åtkomst panelen](https://myapps.microsoft.com) i en webbläsare som stöds. Logga in på Azure AD som en *användare*.

2. Välj **Password-SSO-program** i åtkomst panelen.

3. När du uppmanas att installera program varan väljer du **Installera nu**.

4. Du dirigeras till en nedladdnings sida för din webbläsare. Välj att **lägga till** tillägget.

5. Om du uppmanas väljer du **Aktivera** eller **Tillåt**.

6. Starta om webbläsaren efter installationen.

7. Logga in på åtkomst panelen. Se om du kan öppna dina appar för inloggning med lösen ord.

Du kan också hämta webbläsarens tillägg direkt för Chrome och Firefox genom följande länkar:

-   [Tillägg för Chrome Access panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Åtkomst panels tillägg för Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Visa information om Portal meddelanden

Följ dessa steg om du vill se information om eventuella Portal meddelanden:

1. Välj **meddelande** ikonen (klock ikonen) i det övre högra hörnet av Azure Portal.

2. Välj ett meddelande som visar ett *fel* tillstånd. (De har ett rött "!".)

   > [!NOTE]
   > Det går inte att välja meddelanden som har statusen *lyckades* eller *pågår* .

3. Fönstret **meddelande information** öppnas. Läs informationen om du vill veta mer om problemet.

5. Om du fortfarande behöver hjälp kan du dela informationen med en support tekniker eller produkt gruppen. Välj **kopierings** ikonen till höger om rutan **Kopiera fel** för att kopiera meddelande informationen till resursen.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Skicka meddelande information till en support tekniker för att få hjälp

Det är viktigt att du delar *all* information som anges i det här avsnittet med support så att de snabbt kan hjälpa dig. Om du vill spela in det kan du ta en skärm bild eller välja **Kopiera fel**.

Följande information förklarar vad varje meddelande objekt innebär och innehåller exempel.

### <a name="essential-notification-items"></a>Viktiga meddelande objekt

- **Title**: meddelandets beskrivande rubrik.

   Exempel: *Inställningar för programproxy*

- **Beskrivning**: vad som har uppstått till följd av åtgärden.

   Exempel: *en intern URL som angetts används redan av ett annat program.*

- **Meddelande-ID**: det unika ID: t för meddelandet.

    Exempel: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID för klientbegäran**: det angivna ID: t för begäran som webbläsaren har gjort.

    Exempel: *302fd775-3329-4670-a9f3-bea37004f0bc*

- Tidstämpel **UTC: tidsstämpeln**för när meddelandet inträffade, i UTC.

    Exempel: *2017-03-23T19:50:43.7583681 z*

- **Internt transaktions-ID**: det interna ID: t som används för att leta upp felet i våra system.

    Exempel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: den användare som körde åtgärden.

    Exempel: *tperkins \@ f128.info*

- **Klient-ID**: det unika ID: t för den klient som den användare som körde åtgärden är medlem i.

    Exempel: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Användar objekt-ID**: det unika ID: t för den användare som körde åtgärden.

    Exempel: *17f84be4-51f8-483a-B533-383791227a99*

### <a name="detailed-notification-items"></a>Detaljerade meddelande objekt

- **Visnings namn**: (kan vara tomt) ett mer detaljerat visnings namn för felet.

    Exempel: *Inställningar för programproxy*

- **Status**: meddelandets aktuella status.

    Exempel: *misslyckades*

- **Objekt-ID**: (kan vara tomt) det objekt-ID som åtgärden kördes mot.

   Exempel: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Information**: den detaljerade beskrivningen av vad som har uppstått till följd av åtgärden.

    Exempel: *den interna URL: en <https://bing.com/> är ogiltig eftersom den redan används.*

- **Kopierings fel**: gör att du kan välja **kopierings ikonen** till höger om text rutan **Kopiera fel** för att kopiera meddelande informationen så att du får hjälp med supporten.

    Exempel```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
