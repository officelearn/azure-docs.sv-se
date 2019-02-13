---
title: Installera program programåtkomstpanelens webbläsartillägg – Azure | Microsoft Docs
description: Åtgärda vanliga fel som uppstod när du installerar programåtkomstpanelens webbläsartillägg.
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
ms.topic: article
ms.date: 5/4/18
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b9668a68ed57273c6198dc2ecd34b9001811d3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209310"
---
# <a name="install-the-access-panel-browser-extension"></a>Installera programåtkomstpanelens webbläsartillägg

Åtkomstpanelen är en webbaserad portal. Om du har ett arbets eller skolkonto i Azure Active Directory (Azure AD) kan använda du åtkomstpanelen för att visa och starta molnbaserade program som en Azure AD-administratör har gett dig åtkomst till. 

Om du använder Azure AD-versioner kan du också använda självbetjäning och apphanteringsfunktionerna via åtkomstpanelen. 

Åtkomstpanelen är separat från Azure-portalen. Det kräver inte du har en Azure-prenumeration.

## <a name="web-browser-requirements"></a>Webbläsarkrav

Minimum åtkomstpanelen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du vill logga in på program via lösenordsbaserad SSO i åtkomstpanelen, måste du ha access panel-tillägg som installerats i din webbläsare. Tillägget laddas ned automatiskt när du väljer ett program som har konfigurerats för lösenordsbaserad SSO.

Du kan använda någon av följande webbläsare för lösenordsbaserad enkel inloggning:

- **Microsoft Edge**: på Windows 10 Anniversary Edition eller senare. 
- **Chrome**: på Windows 7 eller senare, och i Mac OS X eller senare.
- **Firefox 26.0 eller senare**: på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.

## <a name="install-the-access-panel-browser-extension"></a>Installera programåtkomstpanelens webbläsartillägg

Om du vill installera programåtkomstpanelens webbläsartillägg, gör du följande:

1.  Öppna i någon av webbläsarna som stöds, den [åtkomstpanelen](https://myapps.microsoft.com), och sedan logga in som en användare i din Azure AD-konto.

2.  Välj en lösenordsbaserad SSO-program.

3.  När du uppmanas, väljer **installera nu**.  
    Du dirigeras till länken för din valda webbläsare. 
    
4.  Välj **Lägg till**.

5.  Om du uppmanas, antingen **aktivera** eller **Tillåt** tillägget.

6.  När installationen är klar startar du om din webbläsare.

7.  Logga in på åtkomstpanelen och kontrollera att se om du börjar din lösenordsbaserad SSO-program.

Du kan också hämta tillägget för Chrome och Microsoft Edge direkt från följande platser:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge-tillägget](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Använd den Mina appar skyddat Inloggningstillägg
* Om du använder en Mina appar-URL än `https://myapps.microsoft.com`, konfigurera standard-URL genom att göra följande:
   1. När du arbetar *inte* inloggad på tillägget, högerklicka på tilläggsikonen.
   2. På menyn, Välj **Mina appar URL**.
   3. Välj standard-URL.
   4. Välj tillägg.
   5. För att logga in till tillägget, Välj **logga in att komma igång**.

* För att logga in direkt till en app i webbläsaren, gör du följande:
   1. När du installerar tillägget, loggar du in till den genom att välja **logga in att komma igång**.
   2. Logga in på appen med inloggnings URL.  
       Inloggnings URL är vanligtvis URL: en för den app som visar formuläret logga in.
      Tillägget bör ändra tillstånd och att du vet att det finns ett lösenord.
   3. Välj tillägg för att logga in.

* Starta en app från tillägget genom att göra följande:
   1. När du installerar tillägget, loggar du in till den genom att välja **logga in att komma igång**.
   2. Välj tillägg till menyn öppnas.
   3. Sök efter en app som är tillgänglig i portalen Mina appar.
   4. Välj appen i listan med sökresultat.  
       De tre sista appar som du har använt visas i den **nyligen använda** genväg lista.
       
* Om du vill använda internt företag URL: er samtidigt remote, gör du följande:
    1. [Konfigurera programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) på din klient
    2. [Publicera programmet](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) och URL: en via programproxy
    3. Installera tillägget och logga in till den genom att välja inloggning att komma igång
    4. Nu kan du bläddra till interna företagets URL även under remote

> [!NOTE]
> Föregående alternativ är endast tillgängligt för Microsoft Edge, Chrome och Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan konfigurera en grupprincip som gör det möjligt att fjärrinstallera access panel-tillägg för Internet Explorer på användarnas datorer.

Innan du konfigurerar en Grupprincip kan du se till att:

-   Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit dina användares datorer till domänen.

-   Om du vill redigera den grupprincipobjekt (GPO), måste du ha *redigera inställningar för* behörigheter. Den här behörigheten ges som standard till medlemmar i följande säkerhetsgrupper: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip.

Steg för steg instruktioner om att konfigurera en Grupprincip och distribuera den till användare finns i [distribuera access panel-tillägg för Internet Explorer med hjälp av Grupprincip](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Felsöka access panel-tillägg i Internet Explorer

Åtkomst till en diagnostikverktyget och information om hur du konfigurerar tillägget för Internet Explorer, se [Felsök access panel-tillägg för Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer är på begränsat stöd och inte längre ta emot nya programuppdateringar. Microsoft Edge är den rekommendera webbläsaren.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Om föregående steg inte löser problemet

Öppna ett supportärende med följande information om den är tillgänglig:

-   Korrelations-ID för fel
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och den tid eller tidsram när felet uppstod
-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Vad är programåtkomst och enkel inloggning med Azure Active Directory?](what-is-single-sign-on.md)
