---
title: Problem med att konfigurera lösenord för enkel inloggning för en icke-galleriprogram | Microsoft Docs
description: Förstå de vanliga problem personer står inför när du konfigurerar lösenord för enkel inloggning för anpassade icke-galleriprogram som inte visas i Azure AD-Programgalleriet
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0d069490f2e5b495b417b5a4d511f02fa7891ea9
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240111"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera lösenord för enkel inloggning för en icke-galleriprogram

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du konfigurerar **lösenord för enkel inloggning** med en icke-galleriprogram.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Så här avbildar inloggningsfält för ett program

Logga in fältet har endast stöd för HTML-aktiverade-inloggningssidor och är **stöds inte för icke-standard-inloggningssidor**, som de som använder Flash eller andra icke-HTML-aktiverade tekniker.

Det finns två sätt kan du fånga in inloggningsfält för dina anpassade program:

-   Fältet för automatisk inloggning avbildning

-   Manuell inloggningsfält avbildning

**Fältet för automatisk inloggning avbilda** fungerar bra med de flesta HTML-aktiverade inloggningssidor, om de använder **välkända DIV-ID: N för användarnamnet och lösenordet som indata** fält. Hur detta fungerar är genom skrapning HTML på sidan för att hitta DIV ID: N som matchar vissa kriterier och sedan spara dessa metadata för det här programmet att spela upp lösenord till den senare.

**Avbildning av manuellt inloggningsfält** kan användas i fall som programmet **leverantör inte etiketten** inmatningsfält som används för att logga in. Manuell inloggningsfält avbildning kan också användas i fallet när den **leverantör visas med flera fält** som inte upptäcks automatiskt. Azure AD kan lagra data för så många fält som finns på sidan logga in så länge du berätta för oss var dessa fält finns på sidan.

I allmänhet **om automatisk inloggningsfält capture inte fungerar kan du prova det manuella alternativet.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Hur du automatiskt fånga in inlogggningsfält för ett program

Konfigurera **lösenordsbaserad enkel inloggning** för ett program med hjälp av **automatisk inloggningsfält capture**, Följ stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Ange den **inloggnings-URL**, URL: en där användare anger sina användarnamn och lösenord för att logga in. **Kontrollera att logga in-fält är synliga på den URL som du anger**.

10. Klicka på knappen **Spara**.

11. När du har gjort som det URL automatiskt som skrapats för ett användarnamn och lösenord Inmatningsruta och att du kan använda Azure AD att på ett säkert sätt överföra lösenord till programmet med hjälp av programåtkomstpanelens webbläsartillägg.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Så här avbildar manuellt inloggningsfält för ett program

Om du vill samla in inloggningsfält manuellt, måste du först ha åtkomst till panelen webbläsartillägget installerad och **inte körs i läget för inPrivate, incognito eller privata.** Om du vill installera webbläsartillägget, följer du stegen i den [så här installerar du åtkomst till panelen webbläsartillägget](#i-cannot-manually-detect-sign-in-fields-for-my-application) avsnittet.

Konfigurera **lösenordsbaserad enkel inloggning** för ett program med hjälp av **manuellt inloggningsfält capture**, Följ stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Ange den **inloggnings-URL**, URL: en där användare anger sina användarnamn och lösenord för att logga in. **Kontrollera att logga in-fält är synliga på den URL som du anger**.

10. Klicka på knappen **Spara**.

11. När du har gjort som det URL automatiskt som skrapats för ett användarnamn och lösenord Inmatningsruta och att du kan använda Azure AD att på ett säkert sätt överföra lösenord till programmet med hjälp av programåtkomstpanelens webbläsartillägg. Om det finns fel, kan du **ändra inloggning-läge för att använda manuell inloggningsfält capture** genom att fortsätta att steg 12.

12. Klicka på **konfigurera &lt;appname&gt; inställningar för lösenord för enkel inloggning**.

13. Välj den **identifieras manuellt inloggningsfält** konfigurationsalternativet.

14. Klicka på **OK**.

15. Klicka på **Spara**.

16. Följ instruktionerna på skärmen att använda åtkomstpanelen.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Jag ser felet ”Det gick inte att hitta några inloggningsfält på den URL: en”

Du ser detta fel när automatisk identifiering av inloggningsfält misslyckas. Lös problemet genom att prova identifiering av manuellt inloggningsfält genom att följa stegen i den [avbilda manuellt inloggningsfält för ett program](#how-to-manually-capture-sign-in-fields-for-an-application) avsnittet.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Jag ser ”det går inte att spara konfigurationen för enkel inloggning” fel

I vissa sällsynta fall kan uppdatera konfigurationen för enkel inloggning misslyckas. För att lösa, försök att spara enkel inloggning för konfigurationen igen.

Om det fortfarande misslyckas regelbundet, öppna ett supportärende och ange information som samlas in den [hur du visar information om ett Portalmeddelande om](#i-cannot-manually-detect-sign-in-fields-for-my-application) och [få hjälp genom att skicka information om meddelande till en Engineer](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) avsnitt.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Jag kan inte identifiera inloggningsfält manuellt för mitt program

Några av de beteenden som kan uppstå när Manuell identifiering inte fungerar är:

-   Manuell avbildningsprocessen verkade fungerar, men de fält som avbildas inte korrekt

-   Till rätt fält hämta inte markerat när du utför den här processen

-   Den här processen kommer jag till programmets inloggningssida som förväntat, men ingenting händer

-   Manuell avbildning ser ut att fungera, men SSO händer inte när användarna navigerar till programmet från åtkomstpanelen.

Kontrollera följande om du stöter på någon av de här problemen:

-   Kontrollera att du har den senaste versionen av programåtkomstpanelens webbläsartillägg **installerat** och **aktiverat** genom att följa stegen i den [hur du installerar webbläsartillägget för åtkomst till panelen](#how-to-install-the-access-panel-browser-extension) avsnittet.

-   Se till att du inte försöker avbildningsprocessen när webbläsaren i **inkognito, InPrivate- eller privata läge**. Access panel-tillägg stöds inte i dessa lägen.

-   Se till att användarna inte försöker logga in till programmet från åtkomstpanelen samtidigt i **inkognito, InPrivate- eller privata läge**. Access panel-tillägg stöds inte i dessa lägen.

-   Prova manuell insamlingen igen och försäkra dig om de röda markörerna är över rätt fält.

-   Om det verkar som om den manuella processen låser sig eller på inloggningssidan inte göra något (fall 3 ovan), försök den manuella processen igen. Men nu när du har slutfört processen, tryck på den **F12** knappen för att öppna din webbläsare utvecklarkonsolen. När det, öppna den **konsolen** och skriv **window.location= ”&lt;ange inloggning url som du angav när du konfigurerar appen&gt;”** och tryck sedan på **RETUR** . Detta gör att en sida-omdirigering som slutar den här processen och lagrar de fält som har spelats in.

Om inget av dessa sätt fungerar för dig, hjälpa support. Öppna ett supportärende med information om vad du försökte, tillsammans med den information som samlas in den [hur du visar information om ett Portalmeddelande om](#i-cannot-manually-detect-sign-in-fields-for-my-application) och [få hjälp genom att skicka information om meddelande till en supporttekniker ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) avsnitten (om tillämpligt).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst till panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst till panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en av de webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** i åtkomstpanelen.

3.  I meddelandet som ber att installera programvaran, väljer **installera nu**.

4.  Beroende på din webbläsare du omdirigerad till länken. **Lägg till** tillägget till din webbläsare.

5.  Om webbläsaren ber, väljer du antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in på åtkomstpanelen på och se om kan du **starta** lösenord SSO-program.

Du kan också ladda ned tillägget för Chrome och Firefox från direkt länkarna nedan:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Hur du visar information om en portal-meddelande

Du kan se information om alla portal-meddelande genom att följa stegen nedan:

1.  Klicka på den **meddelanden** ikonen (klockan) uppe till höger på Azure portal

2.  Välj något meddelande i en **fel** tillstånd (de med ett rött (!) bredvid dem).

  >! Observera] du kan klicka på aviseringar i en **lyckade** eller **pågår** tillstånd.
  >
  >

3.  Den **meddelandeinformation** öppnas fönstret.

4.  Använd informationen själv för att förstå mer information om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela informationen med en supporttekniker eller produktgruppen för att få hjälp med problemet.

6.  Klicka på den **kopia** **ikonen** till höger om den **kopiera fel** textrutan att kopiera alla meddelandeinformation att dela med en support eller produkt grupp-tekniker.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Få hjälp genom att skicka information om meddelande till en supporttekniker

Det är mycket viktigt att du dela **all information som anges nedan** med en supporttekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan **ta en skärmbild** eller klicka på den **kopia felikon**, som finns till höger om den **Kopieringsfel** textrutan.

## <a name="notification-details-explained"></a>Meddelandeinformation förklaras

Den nedan beskriver mer vad de meddelandet objekt innebär och ger exempel på var och en av dem.

### <a name="essential-notification-items"></a>Viktigt meddelande objekt

-   **Rubrik** – beskrivande rubrik för meddelanden

    -   Exempel – **proxyinställningarna för programmet**

-   **Beskrivning av** – beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **interna URL: en som angetts används redan av ett annat program**

-   **Meddelande-ID** – unikt id för meddelandet

    -   Exempel – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID för klientbegäran** – specifik begäran-id som gjorts av din webbläsare

    -   Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Tid UTC för stämpel** – tidsstämpeln som aviseringen inträffade, i UTC

    -   Exempel – **2017-03-23T19:50:43.7583681Z**

-   **Internt transaktions-ID** – internt ID som används för att hämta värdet felet i våra system

    -   Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – den användare som utförde åtgärden

    -   Exempel – **tperkins@f128.info**

-   **Klient-ID** – unikt ID för den klient som den användare som utförde åtgärden var medlem av

    -   Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Objekt-ID för användaren** – unikt ID för den användare som utförde åtgärden

    -   Exempel – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerat meddelande objekt

-   **Visningsnamn** – **(kan vara tom)** en mer detaljerad visningsnamn för felet

    -   Exempel * – **proxyinställningarna för programmet**

-   **Status för** – specifika status för meddelandet

    -   Exempel * – **misslyckades**

-   **Objekt-ID** – **(kan vara tom)** objekt-ID som åtgärden utfördes

    -   Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Information om** – detaljerad beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **interna URL: en ”http://bing.com/' är ogiltig eftersom den inte redan används**

-   **Kopiera fel** – klickar du på den **kopieringsikonen** till höger om den **Kopieringsfel** textrutan att kopiera alla meddelandeinformation att dela med en support eller produkt grupp-tekniker

    -   Exempel – ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

