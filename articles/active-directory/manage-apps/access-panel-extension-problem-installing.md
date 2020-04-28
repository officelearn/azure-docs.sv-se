---
title: Installera webb läsar tillägget för program åtkomst panelen – Azure AD
description: Åtgärda vanliga fel när du installerar åtkomst panelens webb läsar tillägg.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275863"
---
# <a name="install-the-access-panel-browser-extension"></a>Installera åtkomst panelens webb läsar tillägg

Åtkomst panelen är en webbaserad portal. Om du har ett arbets-eller skol konto i Azure Active Directory (Azure AD) kan du använda åtkomst panelen för att visa och starta molnbaserade program som en Azure AD-administratör har gett dig åtkomst till. 

Om du använder Azure AD-versioner kan du också använda funktioner för självbetjänings grupp och program hantering via åtkomst panelen. 

Åtkomst panelen är separat från Azure Portal. Du behöver inte ha någon Azure-prenumeration.

## <a name="web-browser-requirements"></a>Webbläsarkrav

Åtkomst panelen kräver minst en webbläsare som stöder Java Script och har CSS aktiverat. Om du vill logga in på program via lösenordsbaserad SSO på åtkomst panelen måste du ha åtkomst panel tillägget installerat i webbläsaren. Tillägget laddas ned automatiskt när du väljer ett program som är konfigurerat för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan du använda någon av följande webbläsare:

- **Microsoft Edge**: på Windows 10-jubileums version eller senare. 
- **Chrome**: på Windows 7 eller senare och på MacOS X eller senare.
- **Firefox 26,0 eller senare**: på Windows XP SP2 eller senare och på Mac OS X 10,6 eller senare.

## <a name="install-the-access-panel-browser-extension"></a>Installera åtkomst panelens webb läsar tillägg

Så här installerar du åtkomst panelens webb läsar tillägg:

1.  I någon av de webbläsare som stöds öppnar du [åtkomst panelen](https://myapps.microsoft.com)och loggar in som en användare i ditt Azure AD-konto.

2.  Välj ett lösenords-baserat SSO-program.

3.  När du uppmanas väljer du **Installera nu**.  
    Du dirigeras till nedladdnings länken för den valda webbläsaren. 
    
4.  Välj **Lägg till**.

5.  Om du uppmanas **att aktivera** eller **tillåta** tillägget.

6.  Starta om webbläsaren när installationen är klar.

7.  Logga in på åtkomst panelen och kontrol lera om du kan starta dina lösenordsbaserade SSO-program.

Du kan också ladda ned tillägget för Chrome och Microsoft Edge direkt från följande platser:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge-tillägg](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox-tillägg](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Använd tillägget Mina appar säker inloggning
* Om du använder en min Apps-URL än `https://myapps.microsoft.com`konfigurerar du din standard-URL genom att göra följande:
   1. När du *inte* är inloggad på tillägget högerklickar du på ikonen för tillägg.
   2. På menyn väljer du **min Apps URL**.
   3. Välj din standard-URL.
   4. Välj ikonen för tillägg.
   5. Om du vill logga in i tillägget väljer **du logga in för att komma igång**.

* Om du vill logga in direkt till en app från webbläsaren gör du följande:
   1. När du har installerat tillägget loggar du in på det genom att välja **Logga in för att komma igång**.
   2. Logga in på appen med inloggnings-URL: en.  
       Inloggnings-URL: en är vanligt vis URL: en för appen som visar inloggnings formuläret.
      Tillägget bör ändra tillstånd och meddela dig om att ett lösen ord är tillgängligt.
   3. Logga in genom att välja ikonen för tillägg.

* Om du vill starta en app från tillägget gör du följande:
   1. När du har installerat tillägget loggar du in på det genom att välja **Logga in för att komma igång**.
   2. Välj tilläggs ikonen för att öppna dess meny.
   3. Sök efter en app som är tillgänglig i portalen Mina appar.
   4. Välj appen i listan Sök resultat.  
       De tre senaste apparna som du har använt visas i listan över **nyligen använda** genvägar.
       
* Om du vill använda interna företags-URL: er vid fjärr anslutning gör du följande:
    1. [Konfigurera Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) på din klient
    2. [Publicera programmet](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) och URL: en via programproxyn
    3. Installera tillägget och logga in på det genom att välja Logga in för att komma igång
    4. Nu kan du bläddra till den interna företags-URL: en även vid fjärr anslutning

> [!NOTE]
> Föregående alternativ är endast tillgängliga för Microsoft Edge, Chrome och Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grup princip för Internet Explorer

Du kan konfigurera en grup princip som gör att du kan fjärrinstallera åtkomst panels tillägget för Internet Explorer på användarnas datorer.

Innan du konfigurerar en grup princip bör du kontrol lera att:

-   Du har konfigurerat [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)och du har anslutit användarnas datorer till din domän.

-   Om du vill redigera grupprincip-objektet (GPO) måste du ha behörigheterna *Redigera inställningar* . Den här behörigheten beviljas som standard medlemmar i följande säkerhets grupper: domän administratörer, företags administratörer och skapare och ägare av grup princip.

Steg för steg-anvisningar om hur du konfigurerar grup principen och distribuerar den till användare finns i [distribuera åtkomst panel tillägget för Internet Explorer med hjälp av grup princip](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Felsöka åtkomst panel tillägget i Internet Explorer

Information om åtkomst till ett diagnos verktyg och information om hur du konfigurerar tillägget för Internet Explorer finns i [Felsöka åtkomst panel tillägget för Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer har begränsad support och tar inte längre emot nya program uppdateringar. Microsoft Edge är den rekommenderade webbläsaren.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Om föregående steg inte löser problemet

Öppna ett support ärende med följande information, om det är tillgängligt:

-   Korrelations fel-ID
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och tid eller tidsram när felet uppstod
-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Vad är program åtkomst och enkel inloggning med Azure Active Directory?](what-is-single-sign-on.md)
