---
title: Problem med att konfigurera enkel inloggning med lösenord för en icke-galleriprogram | Microsoft Docs
description: Vanliga problem som uppstår när du konfigurerar lösenord för enkel inloggning (SSO) för anpassade appar som inte finns i Azure AD-programgalleriet.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440360"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera lösenord för enkel inloggning för en icke-galleriprogram

Den här artikeln beskrivs vanliga problem som kan uppstå när du konfigurerar *lösenord för enkel inloggning* (SSO) för en icke-galleri-app.

## <a name="capture-sign-in-fields-for-an-app"></a>Fånga in inlogggningsfält för en app

Logga in fältet capture stöds endast för HTML-aktiverade inloggningssidorna. Det har inte stöd för icke-standard-inloggningssidor, t.ex. de som använder Adobe Flash eller andra tekniker för icke-HTML-aktiverade.

Det finns två sätt att fånga in inlogggningsfält för dina appar:

- **Fältet för automatisk inloggning avbilda** fungerar bra med de flesta HTML-aktiverade inloggningssidorna, *om de använder välkända DIV-ID: N* för Användarfält namn och lösenord. HTML-kod på sidan skrapats för att hitta DIV ID: N som matchar vissa kriterier. Dessa metadata sparas så att den kan återupprepas till appen senare.

- **Avbildning av manuellt inloggningsfält** används om appleverantören *inte etiketten inloggning indatafält*. Manuell capture används också om leverantören *visas med flera fält som inte upptäcks automatiskt*. Azure Active Directory (Azure AD) kan lagra data för så många fält som finns på sidan logga in om du instruera där dessa fält finns på sidan.

Om automatisk inloggningsfält avbilda inte i allmänhet fungerar från, försöker det manuella alternativet.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatiskt fånga in inlogggningsfält för en app

Följ dessa steg om du vill konfigurera lösenordsbaserad SSO med hjälp av automatisk inloggningsfält avbildning:

1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som en global administratör eller medadministratör.

2. I navigeringsfönstret till vänster, Välj **alla tjänster** att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app som du vill använda den **Filter** kontroll högst upp på den **alla program** lista. Ange den **visa** alternativet ”alla program”.

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer **enkel inloggning** i navigeringsfönstret till vänster.

8. Välj **lösenordsbaserad inloggning** läge.

9. Ange den **inloggnings-URL**, vilket är Webbadressen till sidan där användare anger sina användarnamn och lösenord för att logga in. *Se till att logga in-fält kan visas på sidan för den URL som du anger*.

10. Välj **Spara**.

    Sidan automatiskt som skrapats för användare och lösenord inmatningsrutorna. Du kan nu använda Azure AD för att säkert överföra lösenord till appen genom att använda webbläsartillägget åtkomstpanel.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Avbilda manuellt inloggningsfält för en app

Om du vill samla in inloggningsfält manuellt, måste du ha åtkomstpanelen webbläsartillägget installerad. Dessutom din webbläsare kan inte köras i *inPrivate*, *incognito*, eller *privata* läge.

Om du vill installera tillägget finns i den [installera Access Panel webbläsartillägget](#install-the-access-panel-browser-extension) i den här artikeln.

Följ dessa steg om du vill konfigurera lösenordsbaserad SSO för en app med hjälp av manuellt inloggningsfält avbildning:

1. Öppna [Azure-portalen](https://portal.azure.com/). Logga in som en global administratör eller medadministratör.

2. I navigeringsfönstret till vänster, Välj **alla tjänster** att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina appar.

   > [!NOTE] 
   > Om du inte ser den app som du vill använda den **Filter** kontroll högst upp på den **alla program** lista. Ange den **visa** alternativet ”alla program”.

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer **enkel inloggning** i navigeringsfönstret till vänster.

8. Välj **lösenordsbaserad inloggning** läge.

9. Ange den **inloggnings-URL**, vilket är den sida där användare anger sina användarnamn och lösenord för att logga in. *Se till att logga in-fält kan visas på sidan för den URL som du anger*.

10. Välj **konfigurera *&lt;appname&gt;* inställningar för lösenord för enkel inloggning**.

11. Välj **identifieras manuellt inloggningsfält**.

14. Välj **OK**.

15. Välj **Spara**.

16. Följ anvisningarna för att använda Access Panel.

## <a name="troubleshoot-problems"></a>Felsöka problem

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Jag får felet ”Det gick inte att hitta några inloggningsfält på den URL: en”

Du får detta felmeddelande när automatisk identifiering av inloggningsfält misslyckas. Lös problemet genom att prova identifiering av manuellt inloggningsfält. Se den [manuellt samla in inloggningsfält för ett program](#manually-capture-sign-in-fields-for-an-app) i den här artikeln.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>”Det går inte att spara konfigurationen för enkel inloggning” visas fel

Mycket sällan misslyckas uppdaterar konfigurationen för enkel inloggning. Lös problemet, försök att spara konfigurationen igen.

Om du får felet, öppnar du ett supportärende. Inkludera information som beskrivs i den [visa information om portalmeddelandet](#view-portal-notification-details) och [skicka meddelande till en supporttekniker för att få hjälp](#send-notification-details-to-a-support-engineer-to-get-help) avsnitt i den här artikeln.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Jag kan inte identifiera inloggningsfält manuellt för min app

Du kan se följande beteenden när Manuell identifiering inte fungerar:

- Manuell avbildningsprocessen verkade fungerar, men de avbildade fält är inte korrekt.

- Rätt fält hämta inte markerat när den här processen körs.

- Den här processen tar dig till inloggningssidan för appens som förväntat, men ingenting händer.

- Manuell avbildning ser ut att fungera, men SSO inträffa inte när användarna navigerar till appen från åtkomstpanelen.

Om det uppstår något av dessa problem kan du göra följande:

- Se till att du har den senaste versionen av webbläsartillägget åtkomstpanel *installerat och aktiverat*. Se den [installera webbläsartillägget åtkomstpanel](#install-the-access-panel-browser-extension) i den här artikeln.

- Kontrollera att din webbläsare inte är i *incognito*, *inPrivate*, eller *privata* läge under den här processen. Åtkomstpanel-tillägg stöds inte i dessa lägen.

- Se till att användarna inte försöker logga in på appen från åtkomstpanelen samtidigt i *incognito*, *inPrivate*, eller *privat läge*.

- Manuell bildtagningen. Försök igen. Se till att de röda markörerna är över rätt fält.

- Om det verkar som om den manuella processen svarar inte eller också svarar inte på inloggningssidan, försök den manuella processen igen. Men nu när du har slutfört processen, trycka på F12 för att öppna din webbläsare utvecklarkonsolen. Välj den **konsolen** fliken. Typ **window.location= ” *&lt;logga in URL: en som du angav när du konfigurerar appen&gt;* ”** , och tryck sedan på RETUR. Detta gör att en sida-omdirigering som slutar den här processen och lagrar de fält som har hämtats.

### <a name="contact-support"></a>Kontakta supporten

Öppna ett ärende hos Microsoft Support om problemet kvarstår. Beskriv vad du försökte. Innehåller information som beskrivs i den [visa portal-meddelandeinformation](#view-portal-notification-details) och [skicka meddelande till en supporttekniker för att få hjälp](#send-notification-details-to-a-support-engineer-to-get-help) avsnitt i den här artikeln (om tillämpligt).

## <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget åtkomstpanel

Följ de här stegen:

1. Öppna [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds. Logga in på Azure AD som en *användaren*.

2. Välj **lösenord SSO-program** i åtkomstpanelen.

3. När du uppmanas att installera programvaran, Välj **installera nu**.

4. Du omdirigeras till hämtningssidan för din webbläsare. Välja att **Lägg till** tillägget.

5. Om du uppmanas väljer **aktivera** eller **Tillåt**.

6. Starta om webbläsaren efter installationen.

7. Logga in på åtkomstpanelen. Se om du kan öppna dina lösenord-SSO-aktiverade appar.

Du kan också direkt hämta webbläsartillägget för Chrome och Firefox via följande länkar:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Visa portalmeddelandet information

Följ dessa steg om du vill se information om alla portal-meddelande:

1. Välj den **meddelanden** ikonen (klockan) i det övre högra hörnet i Azure Portal.

2. Välj något meddelande som visar en *fel* tillstånd. (De har ett rött ””!.)

   > [!NOTE]
   > Du kan inte välja meddelanden som finns i den *lyckade* eller *pågår* tillstånd.

3. Den **meddelandeinformation** öppnas fönstret. Läs informationen om du vill veta mer om problemet.

5. Om du fortfarande behöver hjälp med att dela informationen med en supporttekniker eller produktgruppen. Välj den **kopia** ikonen till höger om den **Kopieringsfel** om du vill kopiera meddelandeinformation att dela.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Skicka meddelande till en supporttekniker för att få hjälp

Det är viktigt att du dela *alla* den information som visas i det här avsnittet med stöd för så att de kan hjälpa dig snabbt. Om du vill spara den som du kan ta en skärmbild eller välja **Kopieringsfel**.

Informationen nedan förklaras vad varje meddelande innebär och innehåller exempel.

### <a name="essential-notification-items"></a>Viktigt meddelande objekt

- **Rubrik**: beskrivande rubrik för meddelanden.

   Exempel: *Programmets proxyinställningar*

- **Beskrivning av**: vad som hänt på grund av åtgärden.

   Exempel: *Intern URL som angetts används redan av ett annat program.*

- **Meddelande-ID**: unikt ID för meddelandet.

    Exempel: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID för klientbegäran**: begäran-ID som gjorts i din webbläsare.

    Exempel: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Tid UTC för stämpel**: tidsstämpel för när meddelandet uppstod i UTC.

    Exempel: *2017-03-23T19:50:43.7583681Z*

- **Internt transaktions-ID**: Internt ID som används för att leta upp fel i vårt system.

    Exempel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: Den användare som körde igen.

    Exempel: *tperkins\@f128.info*

- **Klient-ID**: unikt ID för den klient som den användare som körde åtgärden är medlem i.

    Exempel: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Objekt-ID för användaren**: Unikt ID för den användare som körde igen.

    Exempel: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Detaljerat meddelande objekt

- **Visningsnamn**: (kan vara tom) en mer detaljerad visningsnamn för felet.

    Exempel: *Programmets proxyinställningar*

- **Status för**: specifika status för meddelandet.

    Exempel: *Misslyckades*

- **Objekt-ID**: (kan vara tom) objekt-ID som åtgärden kördes.

   Exempel: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Information om**: detaljerad beskrivning av vad som hänt på grund av åtgärden.

    Exempel: *Intern url '<https://bing.com/>' är ogiltig eftersom den inte redan används.*

- **Kopiera fel**: Kan du välja den **kopieringsikonen** till höger om den **kopiera fel** textrutan att kopiera meddelandeinformation att med stöd för.

    Exempel:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
