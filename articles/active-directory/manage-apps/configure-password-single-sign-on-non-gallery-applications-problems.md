---
title: Problem med att konfigurera lösenord SSO för en appar som inte är galleri
description: Vanliga problem som uppstår när du konfigurerar lösenord enkel inloggning (SSO) för anpassade appar som inte finns i Azure AD-programgalleriet.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274134"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera lösenord enkel inloggning för ett program som inte är galleri

I den här artikeln beskrivs vanliga problem som kan uppstå när du konfigurerar *lösenord enkel inloggning* (SSO) för en app som inte är galleri.

## <a name="capture-sign-in-fields-for-an-app"></a>Samla inloggningsfält för en app

Inhämtning av inloggningsfält stöds endast för HTML-aktiverade inloggningssidor. Det stöds inte för icke-standardiserade inloggningssidor, till exempel de som använder Adobe Flash eller andra tekniker som inte är HTML-aktiverade.

Det finns två sätt att samla in inloggningsfält för dina anpassade appar:

- **Automatisk inloggning av inloggningsfält** fungerar bra med de flesta HTML-aktiverade inloggningssidor, *om de använder välkända DIV-ID:n* för användarnamns- och lösenordsfälten. HTML-koden på sidan skrapas för att hitta DIV-ID:er som matchar vissa villkor. Metadata sparas så att de kan spelas upp i appen senare.

- **Manuell inloggningsfältinsamling** används om appleverantören *inte etiketterar inloggningsinmatningsfälten*. Manuell hämtning används också om leverantören *återger flera fält som inte kan identifieras automatiskt*. Azure Active Directory (Azure AD) kan lagra data för så många fält som det finns på inloggningssidan, om du talar om för den var dessa fält finns på sidan.

Om automatisk inhämtning av inloggningsfält inte fungerar kan du i allmänhet prova det manuella alternativet.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Samla in inloggningsfält automatiskt för en app

Så här konfigurerar du lösenordsbaserad SSO genom att använda automatisk inhämtning av inloggningsfält:

1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som global administratör eller medadministratör.

2. I navigeringsfönstret till vänster väljer du **Alla tjänster** för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filterkontrollen** högst upp i listan **Alla program.** Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har läses in väljer du **Enkel inloggning** i navigeringsfönstret till vänster.

8. Välj **Lösenordsbaserat inloggningsläge.**

9. Ange **inloggnings-URL:en**, som är webbadressen till den sida där användarna anger sitt användarnamn och lösenord för att logga in. *Kontrollera att inloggningsfälten visas på sidan för webbadressen som du anger*.

10. Välj **Spara**.

    Sidan skrapas automatiskt för inmatningsrutorna för användarnamn och lösenord. Du kan nu använda Azure AD för att säkert överföra lösenord till den appen med hjälp av webbläsartillägget för Åtkomstpanelen.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Samla in inloggningsfält manuellt för en app

Om du vill samla in inloggningsfält manuellt måste du ha webbläsartillägget För åtkomstpanelen installerat. Dessutom kan din webbläsare inte köras i *inPrivate,* *inkognito*eller *privat* läge.

Information om hur du installerar tillägget finns i avsnittet [Installera webbläsartillägget för åtkomstpanelen](#install-the-access-panel-browser-extension) i den här artikeln.

Så här konfigurerar du lösenordsbaserad SSO för en app genom att använda manuell inloggningsfältinsamling:

1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som global administratör eller medadministratör.

2. I navigeringsfönstret till vänster väljer du **Alla tjänster** för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina appar.

   > [!NOTE] 
   > Om du inte ser den app du vill använda använder du **filterkontrollen** högst upp i listan **Alla program.** Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har läses in väljer du **Enkel inloggning** i navigeringsfönstret till vänster.

8. Välj **Lösenordsbaserat inloggningsläge.**

9. Ange **inloggnings-URL: n**, som är den sida där användarna anger sitt användarnamn och lösenord för att logga in. *Kontrollera att inloggningsfälten visas på sidan för webbadressen som du anger*.

10. Välj ** * &lt;Konfigurera&gt; lösenord för lösenord* för lösenord Enkel inloggning**.

11. Välj **Identifiera inloggningsfält manuellt**.

14. Välj **Ok**.

15. Välj **Spara**.

16. Följ instruktionerna för att använda Åtkomstpanelen.

## <a name="troubleshoot-problems"></a>Felsökning av problem

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Jag får felmeddelandet "Vi kunde inte hitta några inloggningsfält på den webbadressen"

Det här felmeddelandet visas när automatisk identifiering av inloggningsfält misslyckas. Lös problemet genom att prova manuell inloggningsfältidentifiering. Se [inloggningsfälten manuellt för ett program i](#manually-capture-sign-in-fields-for-an-app) den här artikeln.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Jag får felmeddelandet "Det går inte att spara en enda inloggningskonfiguration"

Det går sällan att uppdatera SSO-konfigurationen. LÃ¶s problemet genom att fÃ¶ ¶0ã¥ringar ã¶kã¶ Ã¶s lÃ¤5sÃ

Om du fortsätter att få felet öppnar du ett supportärende. Inkludera informationen som beskrivs i [information om visa portalaviseringar](#view-portal-notification-details) och [Skicka information om meddelanden till en supporttekniker för att få hjälpavsnitt](#send-notification-details-to-a-support-engineer-to-get-help) i den här artikeln.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Jag kan inte identifiera inloggningsfält manuellt för min app

Du kan observera följande beteenden när manuell identifiering inte fungerar:

- Den manuella insamlingsprocessen verkade fungera, men de infångade fälten är inte korrekta.

- Rätt fält markeras inte när insamlingsprocessen körs.

- Insamlingsprocessen tar dig till appens inloggningssida som förväntat, men inget händer.

- Manuell hämtning verkar fungera, men SSO inträffar inte när användare navigerar till appen från Åtkomstpanelen.

Om du upplever något av dessa problem gör du följande:

- Kontrollera att du har den senaste versionen av webbläsartillägget Access Panel *installerad och aktiverad*. Se avsnittet [Installera webbläsartillägget för Åtkomstpanelen](#install-the-access-panel-browser-extension) i den här artikeln.

- Kontrollera att din webbläsare inte är *i inkognito,* *inPrivat eller* *Privat* läge under insamlingsprocessen. Tillägget till åtkomstpanelen stöds inte i dessa lägen.

- Kontrollera att användarna inte försöker logga in på appen från Åtkomstpanelen i *inkognito,* *inPrivate*eller *Privat läge*.

- Prova den manuella insamlingsprocessen igen. Kontrollera att de röda markrarna är över rätt fält.

- Om den manuella insamlingsprocessen verkar sluta svara eller om inloggningssidan inte svarar provar du den manuella insamlingsprocessen igen. Men den här gången, efter att ha slutfört processen, tryck på F12 för att öppna webbläsarens utvecklarkonsol. Välj **konsolfliken.** Skriv **fönster.location="*&lt;den inloggnings-URL som du&gt;angav när du konfigurerade appen*"** och tryck sedan på Retur. Detta tvingar en sidomdirigering som avslutar insamlingsprocessen och lagrar de fält som fångades.

### <a name="contact-support"></a>Kontakta supporten

Om du fortfarande har problem öppnar du ett ärende med Microsoft Support. Beskriv vad du försökte. Inkludera informationen som beskrivs i [information om visa portalaviseringar](#view-portal-notification-details) och [Skicka information om meddelanden till en supporttekniker för att få hjälpavsnitt](#send-notification-details-to-a-support-engineer-to-get-help) i den här artikeln (om tillämpligt).

## <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget för Åtkomstpanelen

Följ de här stegen:

1. Öppna [Åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds. Logga in på Azure AD som *användare*.

2. Välj **lösenords-SSO-program** på Åtkomstpanelen.

3. När du uppmanas att installera programvaran väljer du **Installera nu**.

4. Du dirigeras till en nedladdningssida för din webbläsare. Välj att **lägga till** tillägget.

5. Om du uppmanas att göra en fråga väljer du **Aktivera** eller **Tillåt**.

6. Starta om webbläsaren efter installationen.

7. Logga in på åtkomstpanelen. Se om du kan öppna dina lösenords-SSO-aktiverade appar.

Du kan också direkt ladda ner webbläsartillägget för Chrome och Firefox via dessa länkar:

-   [Tillägg till Chrome Access Panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägg till Firefox Access Panel](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Visa information om portalavisering

Så här ser du information om eventuella portalmeddelanden:

1. Välj ikonen **Meddelanden** (klockan) i det övre högra hörnet av Azure-portalen.

2. Markera ett meddelande som visar ett *feltillstånd.* (De har en röd "!".)

   > [!NOTE]
   > Du kan inte välja aviseringar som är i tillståndet *Lyckad* eller *Pågår.*

3. Fönstret **Meddelandeinformation** öppnas. Läs informationen om problemet.

5. Om du fortfarande behöver hjälp kan du dela informationen med en supporttekniker eller produktgruppen. Markera **kopieringsikonen** till höger om rutan **Kopiera fel** om du vill kopiera meddelandeinformationen som ska delas.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Skicka information om avisering till en supporttekniker för att få hjälp

Det är viktigt att du delar *alla* detaljer som visas i det här avsnittet med stöd så att de kan hjälpa dig snabbt. Om du vill spela in den kan du ta en skärmbild eller välja **Kopiera fel**.

Följande information förklarar vad varje meddelandeobjekt betyder och ger exempel.

### <a name="essential-notification-items"></a>Viktiga meddelandeobjekt

- **Benämning**: Anmälans beskrivande titel.

   Exempel: *Proxyinställningar för program*

- **Beskrivning**: vad som hände som ett resultat av operationen.

   Exempel: *Intern URL som anges används redan av ett annat program.*

- **Meddelande-ID:** det unika ID:t för meddelandet.

    Exempel: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Klientbegärande-ID:** det specifika begärande-ID som din webbläsare har gjort.

    Exempel: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Tidsstämpel UTC:** tidsstämpeln för när meddelandet inträffade, i UTC.

    Exempel: *2017-03-23T19:50:43.7583681Z*

- **Internt transaktions-ID:** det interna ID som används för att slå upp felet i våra system.

    Exempel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: Användaren som körde åtgärden.

    Exempel: *tperkins\@f128.info*

- **Klient-ID:** det unika ID:et för klienten som användaren som körde åtgärden är medlem i.

    Exempel: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Användarobjekt-ID:** Det unika ID:et för den användare som körde åtgärden.

    Exempel: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Detaljerade meddelandeobjekt

- **Visningsnamn**: (kan vara tomt) ett mer detaljerat visningsnamn för felet.

    Exempel: *Proxyinställningar för program*

- **Status**: anmälans särskilda status.

    Exempel: *Misslyckades*

- **Objekt-ID**: (kan vara tomt) det objekt-ID som åtgärden kördes mot.

   Exempel: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Uppgifter**: Den detaljerade beskrivningen av vad som inträffade till följd av operationen.

    Exempel: *Intern<https://bing.com/>url ' ' är ogiltig eftersom den redan används.*

- **Kopieringsfel**: Gör att du kan välja **kopieringsikonen** till höger om textrutan **Kopiera fel** för att kopiera meddelandeinformationen för att hjälpa till med supporten.

    Exempel:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
