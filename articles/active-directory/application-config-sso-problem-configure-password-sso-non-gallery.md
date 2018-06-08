---
title: Problem som konfigurerar lösenord enkel inloggning för ett icke-galleriet program | Microsoft Docs
description: Förstå de vanliga problem personer står inför när du konfigurerar lösenord enkel inloggning för anpassade program för icke-galleriet som inte listas i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7b671089bfac04b359717874928d6a342c44b3d4
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "34069682"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurera lösenord enkel inloggning för ett icke-galleriet program problem

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du konfigurerar **lösenord enkel inloggning** med ett icke-galleriet program.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Så här avbildar inloggning fält för ett program

Logga in fältet stöds bara för HTML-aktiverade inloggningssidor och är **stöds inte för icke-standard inloggningssidor**, som de som använder Flash eller andra icke-HTML-aktiverade tekniker.

Det finns två sätt som du kan avbilda inloggning fält för dina anpassade program:

-   Fältet för automatisk inloggning avbildning

-   Fältet för manuell inloggning avbildning

**Fältet för automatisk inloggning avbilda** fungerar väl med de flesta HTML-aktiverade inloggningssidor, om de använder **välkända DIV-ID: N för användarnamn och lösenord Ange** fältet. Hur detta fungerar är genom skrapning HTML på sidan för att hitta DIV-ID som matchar vissa villkor och sedan spara att metadata för det här programmet spela lösenord till den senare.

**Fältet för manuell inloggning avbilda** kan användas i fall som programmet **leverantör inte etiketten** indatafält används för inloggning. Fältet för manuell inloggning avbilda kan också användas i fallet när den **leverantör återgivningar flera fält** som inte upptäcks automatiskt. Azure AD kan lagra data för så många fält som finns på inloggningssidan, så länge du berätta var dessa fält finns på sidan.

I allmänhet **om automatisk inloggning fältet avbilda inte fungerar försöker du med manuell.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Så här avbildar automatiskt inloggning fält för ett program

Så här konfigurerar du **lösenordsbaserade enkel inloggning** för ett program som använder **fält för automatisk inloggning avbilda**, Följ stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  Ange den **inloggnings-URL**, där användarna anger sina användarnamn och lösenord för att logga in URL: en. **Kontrollera fälten inloggning är synliga på den URL som du anger**.

10. Klicka på knappen **Spara**.

11. När du gör det, som URL automatiskt skrapats för ett användarnamn och lösenord Inmatningsruta och du kan använda Azure AD för att säkert överföra lösenord för det aktuella programmet med hjälp av webbläsartillägget för åtkomst-panelen.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Så här avbildar inloggning fält för ett program manuellt

Om du vill samla in inloggning fält manuellt, måste du först ha åtkomst panelen webbläsartillägget installerad och **inte körs i inPrivate incognito eller privat läge.** Om du vill installera webbläsartillägg för, följer du stegen i den [installera åtkomst panelen webbläsartillägget](#i-cannot-manually-detect-sign-in-fields-for-my-application) avsnitt.

Så här konfigurerar du **lösenordsbaserade enkel inloggning** för ett program som använder **manuell inloggning fältet avbilda**, Följ stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  Ange den **inloggnings-URL**, där användarna anger sina användarnamn och lösenord för att logga in URL: en. **Kontrollera fälten inloggning är synliga på den URL som du anger**.

10. Klicka på knappen **Spara**.

11. När du gör det, som URL automatiskt skrapats för ett användarnamn och lösenord Inmatningsruta och du kan använda Azure AD för att säkert överföra lösenord för det aktuella programmet med hjälp av webbläsartillägget för åtkomst-panelen. Vid fel, kan du **Ändra läget för att använda manuell inloggning fältet avbilda** genom att fortsätta att steg 12.

12. Klicka på **konfigurera &lt;appname&gt; inställningar för lösenord enkel inloggning**.

13. Välj den **identifieras manuellt inloggning fält** konfigurationsalternativet.

14. Klicka på **OK**.

15. Klicka på **Spara**.

16. Följ instruktionerna på skärmen att använda åtkomstpanelen.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Felet ”Det gick inte att hitta alla fält på den URL” visas

Det här felet visas när automatisk identifiering av inloggning fält misslyckas. Försök för att lösa problemet manuell inloggning fältet identifiering genom att följa stegen i den [hur du manuellt samla in inloggning fält för ett program](#how-to-manually-capture-sign-in-fields-for-an-application) avsnitt.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>”Det går inte att spara konfigurationen enkel inloggning” visas fel

I vissa sällsynta fall, kan uppdatera konfigurationen för enkel inloggning misslyckas. Försök för att lösa sparar enkel inloggning konfigurationen igen.

Om det fortfarande misslyckas regelbundet, öppna ett supportärende och ange den information som samlats in i den [så visas detaljerad information om en portalmeddelandet](#i-cannot-manually-detect-sign-in-fields-for-my-application) och [få hjälp genom att skicka meddelandeinformation till en supportbegäran tekniker](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) avsnitt.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Jag kan inte identifiera inloggning fält manuellt för Mina program

Några av de funktioner som kan uppstå när Manuell identifiering inte fungerar är:

-   Den manuella processen fanns ska fungera, men fälten avbildas inte korrekt

-   Höger-fält hämta inte markeras när du utför den här processen

-   Den här processen för att komma till programmets inloggningssida som förväntat, men ingenting händer

-   Manuell avbilda verkar fungera, men SSO inträffa inte när användarna navigerar till programmet från åtkomstpanelen.

Kontrollera följande om du stöter på något av följande problem:

-   Kontrollera att du har den senaste versionen av webbläsartillägget för åtkomst till Kontrollpanelen **installerat** och **aktiverat** genom att följa stegen i den [installera åtkomst panelen webbläsartillägget](#how-to-install-the-access-panel-browser-extension) avsnitt.

-   Se till att du inte försöker utföra den här processen när webbläsaren i **incognito InPrivate- eller privat läge**. Tillägget för åtkomst-panelen stöds inte i dessa lägen.

-   Se till att användarna inte försöker logga in till programmet från åtkomstpanelen när i **incognito InPrivate- eller privat läge**. Tillägget för åtkomst-panelen stöds inte i dessa lägen.

-   Försök manuella processen igen, säkerställt röda markörer är över rätt fält.

-   Om manuell insamlingen verkar låser sig eller inloggningssida visas inte göra något (fall 3 ovan), försök manuella processen igen. Men nu när du har slutfört processen, tryck på den **F12** knappen för att öppna din webbläsare developer-konsolen. En gång, öppna den **konsolen** och skriv **window.location= ”&lt;ange inloggning url som du angav när du konfigurerar appen&gt;”** och tryck sedan på **RETUR** . Detta tvingar en sida-omdirigering som slutar insamlingen och lagrar de fält som har spelats in.

Om inget av dessa sätt fungerar kan hjälpa support. Öppna ett supportärende med information om vad du försökte, samt information som samlats in i den [så visas detaljerad information om en portalmeddelandet](#i-cannot-manually-detect-sign-in-fields-for-my-application) och [få hjälp genom att skicka meddelandeinformation till en supporttekniker ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) avsnitten (om tillämpligt).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du Access panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** på åtkomstpanelen.

3.  Fråga om att installera programvara, Välj **installera nu**.

4.  Baserat på din webbläsare du dirigeras till länken. **Lägg till** tillägg till webbläsaren.

5.  Om din webbläsare, Välj antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in till åtkomstpanelen och se om kan du **starta** lösenord SSO-program.

Du kan också ladda ned tillägget för Chrome och Firefox från direkt med länkarna nedan:

-   [Tillägget för Chrome åtkomst panelen](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägget för Firefox åtkomst panelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Hur du visar information om ett meddelande om portal

Du kan se information om eventuella portalmeddelandet genom att följa stegen nedan:

1.  Klicka på den **meddelanden** ikonen (sal) i övre högra hörnet i Azure-portalen

2.  Markera alla meddelanden i en **fel** tillstånd (de med ett rött (!) bredvid dem).

  >! Observera] du kan klicka på meddelanden i en **lyckade** eller **pågår** tillstånd.
  >
  >

3.  Den **detaljer** fönstret öppnas.

4.  Använd informationen dig att förstå mer information om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela informationen med en supporttekniker eller produktgruppen för att få hjälp med problemet.

6.  Klickar du på den **kopiera** **ikonen** till höger om den **Kopieringsfel** textruta för att kopiera meddelande allt delar med en support eller produkt grupp tekniker.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Få hjälp genom att skicka meddelandeinformation till en supporttekniker

Det är mycket viktigt att du dela **allt som anges nedan** med en supporttekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan **ta en skärmbild** eller klicka på den **kopiera felikonen**, hittade till höger om den **Kopieringsfel** textruta.

## <a name="notification-details-explained"></a>Meddelandeinformation förklaras

Den nedan beskrivs mer vad varje av meddelandet objekt innebär och innehåller exempel på var och en av dem.

### <a name="essential-notification-items"></a>Viktigt meddelande objekt

-   **Rubrik** – beskrivande rubrik i meddelandet

    -   Exempel – **Application proxy-inställningar**

-   **Beskrivning** – beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **intern url har angett används redan av ett annat program**

-   **Meddelande-ID** – unikt id för meddelandet

    -   Exempel – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID för klientbegäran** – specifik begäran-id som din webbläsare

    -   Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Tid UTC stämpel** – tidsstämpeln då uppstod meddelandet i UTC

    -   Exempel – **2017-03-23T19:50:43.7583681Z**

-   **Internt transaktions-ID** – internt ID som används för att söka av fel i vårt system

    -   Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – användaren som utförde åtgärden

    -   Exempel – **tperkins@f128.info**

-   **Klient-ID** – unikt ID för den klient som användaren som utförde åtgärden var medlem av

    -   Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Objekt-ID för användaren** – unikt ID för den användare som utförde åtgärden

    -   Exempel – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerat meddelande objekt

-   **Visningsnamn** – **(kan vara tom)** en mer detaljerad visningsnamn efter fel

    -   Exempel * – **Application proxy-inställningar**

-   **Status för** – specifika status för meddelandet

    -   Exempel * – **misslyckades**

-   **Objekt-ID** – **(kan vara tom)** objekt-ID som åtgärden utfördes

    -   Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Information om** – detaljerad beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **intern url 'http://bing.com/' är ogiltig eftersom den redan används**

-   **Kopiera fel** – klickar du på den **kopiera-ikonen** till höger om den **Kopieringsfel** textruta för att kopiera meddelande allt delar med en support eller produkt grupp tekniker

    -   Exempel – ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

