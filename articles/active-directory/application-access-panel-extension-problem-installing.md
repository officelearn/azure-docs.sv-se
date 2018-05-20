---
title: Installera program åtkomst panelen webbläsare extension - Azure | Microsoft Docs
description: Åtgärda vanliga fel som uppstod när du installerar webbläsartillägget för åtkomst-panelen.
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
ms.date: 5/4/18
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 8db0c28b0ea6b6f78ef86c773ff24f9d22fe3ecd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget för åtkomst till Kontrollpanelen

Åtkomstpanelen är en webbaserad portal. Om du har ett arbets- eller skolkonto i Azure Active Directory (Azure AD) kan du använda åtkomstpanelen visa och starta molnbaserade program som en Azure AD-administratör har gett dig åtkomst till. 

Om du använder Azure AD-versioner kan du också använda självbetjäning och funktioner för hantering av appen via åtkomstpanelen. 

Åtkomstpanelen är separat från Azure-portalen. Det behöver du inte ha en Azure-prenumeration.

## <a name="web-browser-requirements"></a>Webbläsarkrav

Minimum åtkomstpanelen kräver en webbläsare som stöder JavaScript och har aktiverat för CSS. Om du vill logga in på program via lösenordsbaserade SSO i åtkomstpanelen, måste du ha åtkomst till panelen tillägget installeras i webbläsaren. Tillägget laddas ned automatiskt när du väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

Du kan använda någon av följande webbläsare för lösenordsbaserad enkel inloggning:

- **Edge**: på Windows 10 årsdagar Edition eller senare. 
- **Chrome**: på Windows 7 eller senare, och i MacOS X eller senare.
- **Firefox 26.0 eller senare**: Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.

## <a name="install-the-access-panel-browser-extension"></a>Installera webbläsartillägget för åtkomst till Kontrollpanelen

Om du vill installera webbläsartillägget för åtkomst till Kontrollpanelen, gör du följande:

1.  Öppna i en av webbläsarna som stöds, den [åtkomstpanelen](https://myapps.microsoft.com), och sedan logga in som en användare i Azure AD-kontot.

2.  Välj en lösenordsbaserad SSO-program.

3.  När du uppmanas, Välj **installera nu**.  
    Du dirigeras till länken för den valda webbläsaren. 
    
4.  Välj **Lägg till**.

5.  Om du uppmanas, antingen **aktivera** eller **Tillåt** tillägget.

6.  När installationen är klar kan du starta om webbläsaren.

7.  Logga in på åtkomstpanelen och kontrollera att se om du kan starta dina lösenordsbaserade SSO-program.

Du kan också hämta tillägget för Chrome och kanten direkt från följande platser:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge-tillägg](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Använd den Mina appar säker inloggning tillägg
* Om du använder en URL för Mina appar än `https://myapps.microsoft.com`, konfigurera standard-URL genom att göra följande:
   1. När du är *inte* inloggad-tillägget, högerklicka på ikonen för tillägget.
   2. Välj på menyn **Mina appar URL**.
   3. Välj standard-URL.
   4. Välj ikonen tillägg.
   5. Om du vill logga in på tillägget, Välj **logga in att komma igång**.

* Logga in direkt till en app från webbläsaren genom att göra följande:
   1. När du installerar tillägget för inloggning till den genom att välja **logga in att komma igång**.
   2. Logga in i appen med URL för inloggning.  
       URL för inloggning är vanligtvis Webbadressen till den app som visar inloggning formuläret.
      Tillägget bör ändra tillstånd och att du vet att ett lösenord är tillgänglig.
   3. Välj ikonen tillägg för att logga in.

* Om du vill starta en app från tillägget gör du följande:
   1. När du installerar tillägget för inloggning till den genom att välja **logga in att komma igång**.
   2. Välj ikonen tillägget menyn öppnas.
   3. Sök efter en app som är tillgängliga i portalen Mina appar.
   4. I listan över sökresultat, Välj appen.  
       De tre senaste appar som du har använt visas i den **nyligen använda** genvägen lista.
       
* Om du vill använda internt företag URL: er när remote gör du följande:
    1. [Konfigurera Application Proxy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-enable) på din klient
    2. [Publicera programmet](https://docs.microsoft.com/en-us/azure/active-directory/application-proxy-publish-azure-portal) och URL: en via Application Proxy
    3. Installera tillägget och logga in på den genom att välja logga in och komma igång
    4. Du kan nu bläddra till URL-Adressen för internt företag även fjärr

> [!NOTE]
> Föregående alternativ är endast tillgängligt för gräns, Chrome och Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan konfigurera en grupprincip som gör det möjligt att fjärrinstallera access panelen-tillägg för Internet Explorer på användarnas datorer.

Innan du konfigurerar en Grupprincip kan du se till att:

-   Du har ställt in [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), och du har anslutit användarnas datorer till domänen.

-   Om du vill redigera det grupprincipobjekt (GPO), måste du ha *redigera inställningar för* behörigheter. Som standard den här behörigheten till medlemmar i följande säkerhetsgrupper: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip.

Steg för steg instruktioner om hur du konfigurerar grupprincipen och distribuera den till användare, se [distribuera access panelen-tillägg för Internet Explorer med hjälp av Grupprincip](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Felsökning av tillägget för åtkomst-panelen i Internet Explorer

Åtkomst till en diagnostik och information om hur du konfigurerar tillägget för Internet Explorer finns [felsöka access panelen-tillägg för Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Internet Explorer finns på begränsat stöd och inte längre ta emot nya programuppdateringar. Kant är den rekommenderade webbläsaren.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Om de föregående stegen inte löser problemet

Öppna ett supportärende med följande information om den är tillgänglig:

-   Fel-ID för korrelation
-   UPN (användarens e-postadress)
-   Klient-ID
-   Typ av webbläsare
-   Tidszon och tid eller tidsram när felet uppstod
-   Fiddler spårningar

## <a name="next-steps"></a>Nästa steg
[Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
