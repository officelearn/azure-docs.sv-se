---
title: Installera webbläsartillägg för programåtkomstpanelen - Azure AD
description: Åtgärda vanliga fel som påträffades när du installerar webbläsartillägget för åtkomstpanelen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275863"
---
# <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget för åtkomstpanelen

Åtkomstpanelen är en webbaserad portal. Om du har ett arbets- eller skolkonto i Azure Active Directory (Azure AD) kan du använda åtkomstpanelen för att visa och starta molnbaserade program som en Azure AD-administratör har gett dig åtkomst till. 

Om du använder Azure AD-utgåvor kan du också använda funktioner för självbetjäningsgrupp och apphantering via åtkomstpanelen. 

Åtkomstpanelen är separat från Azure-portalen. Det kräver inte att du har en Azure-prenumeration.

## <a name="web-browser-requirements"></a>Webbläsarkrav

Åtminstone kräver åtkomstpanelen en webbläsare som stöder JavaScript och har CSS aktiverat. Om du vill logga in på program via lösenordsbaserad SSO på åtkomstpanelen måste du ha tillägget för åtkomstpanelen installerat i webbläsaren. Tillägget hämtas automatiskt när du väljer ett program som är konfigurerat för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan du använda någon av följande webbläsare:

- **Microsoft Edge**: på Windows 10 Anniversary Edition eller senare. 
- **Chrome**: på Windows 7 eller senare, och på MacOS X eller senare.
- **Firefox 26.0 eller senare:** på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.

## <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget för åtkomstpanelen

Så här installerar du webbläsartillägget för åtkomstpanelen:

1.  Öppna [åtkomstpanelen](https://myapps.microsoft.com)i en av webbläsarna som stöds och logga sedan in som användare i ditt Azure AD-konto.

2.  Välj ett lösenordsbaserat SSO-program.

3.  När du uppmanas att göra det väljer du **Installera nu**.  
    Du dirigeras till nedladdningslänken för din valda webbläsare. 
    
4.  Välj **Lägg till**.

5.  Om du uppmanas att göra **det kan** du antingen aktivera eller **tillåta** tillägget.

6.  När installationen är klar startar du om webbläsaren.

7.  Logga in på åtkomstpanelen och kontrollera om du kan starta dina lösenordsbaserade SSO-program.

Du kan också hämta tillägget för Chrome och Microsoft Edge direkt från följande webbplatser:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge-tillägg](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox-förlängning](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Använda tillägget Säker inloggning för mina appar
* Om du använder en url `https://myapps.microsoft.com`för Mina appar än konfigurerar du standard-URL:en genom att göra följande:
   1. När du *inte* är inloggad på tillägget högerklickar du på tilläggsikonen.
   2. Välj Url för **Mina appar**på menyn .
   3. Välj standard-URL.
   4. Välj tilläggsikonen.
   5. Om du vill logga in på tillägget väljer du **Logga in för att komma igång**.

* Så här loggar du in direkt på en app från webbläsaren:
   1. När du har installerat tillägget loggar du in på det genom att välja **Logga in för att komma igång**.
   2. Logga in i appen med inloggningsadressen.  
       Inloggnings-URL:en är vanligtvis webbadressen till appen som visar inloggningsformuläret.
      Tillägget bör ändra tillstånd och låta dig veta att ett lösenord är tillgängligt.
   3. Om du vill logga in väljer du tilläggsikonen.

* Så här startar du en app från tillägget:
   1. När du har installerat tillägget loggar du in på det genom att välja **Logga in för att komma igång**.
   2. Välj tilläggsikonen för att öppna menyn.
   3. Sök efter en app som är tillgänglig i portalen Mina appar.
   4. Välj appen i sökresultatlistan.  
       De tre senaste apparna som du har använt visas i genvägslistan **Nyligen använd.**
       
* Så här använder du interna företagsadresser när du är fjärransluten:
    1. [Konfigurera programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) på din klientorganisation
    2. [Publicera programmet](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) och URL:en via programproxy
    3. Installera tillägget och logga in på det genom att välja Logga in för att komma igång
    4. Du kan nu bläddra till den interna företagsadressen även när

> [!NOTE]
> De föregående alternativen är endast tillgängliga för Microsoft Edge, Chrome och Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan ställa in en grupprincip som gör att du kan fjärr installera tillägget för åtkomstpanelen för Internet Explorer på användarnas datorer.

Innan du ställer in en grupprincip bör du se till att:

-   Du har konfigurerat [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)och du har anslutit användarnas datorer till domänen.

-   Om du vill redigera grupprincipobjektet måste du ha *behörigheten Redigera inställningar.* Som standard beviljas den här behörigheten till medlemmar i följande säkerhetsgrupper: domänadministratörer, företagsadministratörer och gruppprincipskapare.

Stegvisa instruktioner om hur du konfigurerar grupprincipen och distribuerar den till användare finns i [Distribuera tillägg till åtkomstpanelen för Internet Explorer med hjälp av grupprincip](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Felsöka tillägg till åtkomstpanelen i Internet Explorer

Åtkomst till ett diagnostikverktyg och information om hur du konfigurerar tillägget för Internet Explorer finns i [Felsöka tillägg till åtkomstpanelen för Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer har begränsad support och får inte längre nya programuppdateringar. Microsoft Edge är den rekommenderade webbläsaren.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Om föregående steg inte löser problemet

Öppna en supportbiljett med följande information, om den är tillgänglig:

-   Korrelationsfel-ID
-   UPN (användarens e-postadress)
-   TenantID (Klient-ID)
-   Typ av webbläsare
-   Tidszon och den tid eller tidsram då felet uppstod
-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Vad är programåtkomst och enkel inloggning med Azure Active Directory?](what-is-single-sign-on.md)
