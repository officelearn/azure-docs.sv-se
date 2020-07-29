---
title: Hitta & använda appar på portalen Mina appar – Azure AD
description: Lär dig hur du hittar portalen för Mina appar och hur du kommer åt din organisations molnbaserade appar.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: aa9813480425d179bdd11bac6f6f944f9f65e3ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83741966"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>Logga in och starta appar från portalen Mina appar

Du kan använda ditt arbets-eller skol konto med den webbaserade **min Apps** -portalen för att:

- Visa och starta många av organisationens molnbaserade appar
- Uppdatera din profil-och konto information
- Visa information om din **grupp**
- Utför **åtkomst granskningar** för dina appar och grupper

Om du inte har åtkomst till portalen för **Mina appar** , Kontakta supportavdelningen för att få behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> Det här innehållet är avsett för mina Apps-användare. Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar dina molnbaserade appar i [program hanterings dokumentationen](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Webbläsare som stöds

Du kan gå till portalen för **Mina appar** från någon av följande webbläsare:

- Google Chrome

- Mozilla Firefox, version 26,0 eller senare

- Microsoft Edge

- Internet Explorer, version 11 (begränsat stöd)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Ladda ned och installera säkra inloggnings tillägg för Mina appar

Hämta och installera säker inloggnings tillägg för Mina appar om du uppmanas att göra det. Med det här tillägget kan du starta valfri organisations molnappar som kräver att du använder en enkel inloggnings process. Om din organisation redan har konfigurerat dig för enkel inloggning installeras tillägget automatiskt och du kan hoppa över det här avsnittet.

Det här tillägget hjälper dig att:

- Logga in direkt till appar från inloggnings sidan.

- Starta alla appar med hjälp av funktionen **snabb sökning** .

- Se de senaste apparna som du använde i det **senast använda** avsnittet.

- Använd interna företags-URL: er vid fjärr anslutning med [Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Hämta och installera tillägget

Hämta och installera tillägget, baserat på den webbläsare som du använder:

- **Google Chrome.** Från Chrome-webbarkivet går du till funktionen [Mina appar säker inloggnings tillägg](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) och väljer sedan **Lägg till i Chrome**.

- **Mozilla Firefox** På sidan **Firefox-tillägg** går du till funktionen [Mina appar säker inloggnings tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/) och väljer sedan **Lägg till i Firefox**.

- **Microsoft Edge** Från Microsoft Store går du till funktionen [Mina appar säker inloggnings tillägg](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) och väljer **Hämta** för att hämta tillägget för Microsoft Edge Legacy-webbläsare.  
Om du använder den nya Microsoft Edge-webbläsaren (Edge på krom) kan du hämta extenion från [Microsoft Edge addons Store](https://microsoftedge.microsoft.com/addons/category/EdgeExtensionsEditorsPick).

En ikon läggs till till höger i **adress** fältet, så att du kan logga in och anpassa tillägget.

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>Så här ändrar du portalen för Mina appar med hjälp av tillägget

Du kan välja hur många appar som ska visas i avsnittet som **används nyligen** och bestämma om du vill tillåta att organisationens interna URL: er omdirigeras.

1. Välj ikonen för den nya ikonen **Mina appar säker inloggnings** tillägg ![ ](media/my-apps-portal/my-apps-portal-extension-icon.png) till höger om **adress** fältet och välj sedan **Logga in för att komma igång**.

2. Högerklicka på ikonen Inställningar **inställnings ikon** ![ ](media/my-apps-portal/my-apps-portal-extension-settings-icon.png) och välj sedan **Inställningar**.

3. I rutan **Inställningar** väljer du det antal senaste appar som du vill se på portalen, och om du vill att organisationens interna URL: er ska omdirigeras så att du kan använda dem via en fjärr anslutning.

    ![Sidan Inställningar för tillägget och visar tillgängliga anpassningar](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Komma åt och använda My Apps-portalen per enhet

Du kan komma åt och använda mina apps-portalen på din dator, från en Intune-hanterad webbläsare eller från en iOS-eller Android-mobil enhet.

![Sidan appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Få åtkomst till och använda Mina appar-portalen på din dator

Om du har åtkomst behörighet till och använder din organisations molnbaserade appar kan du komma åt dem via portalen **Mina appar** .

1. Logga in på ditt arbets-eller skol konto på din dator.

2. Öppna och gå till i en webbläsare som stöds https://myapps.microsoft.com , eller Använd länken som tillhandahålls av din organisation om de dirigerar dig till en anpassad sida, till exempel `https://myapps.microsoft.com/contoso.com` .

    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som är tillgängliga för användning.

3. Från sidan **appar** väljer du den app som du vill börja använda.

    En ny sida öppnas för appen där du kan logga in (om det behövs) eller börja använda appen.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Komma åt och använda mina apps-portalen i en Intune-hanterad webbläsare

Visa och Använd din organisations appar från en Intune Managed Browser på dina iOS-och Android-enheter.

1. Hämta och installera Intune Managed Browser-appen från Apple App Store och Google Play Butik på den mobila enheten.

2. Öppna appen Intune Managed Browser, gå till https://myapps.microsoft.com eller Använd länken från din organisation om de dirigerar dig till en anpassad sida, till exempel https://myapps.microsoft.com/contoso.com .

    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

3. Från sidan **appar** väljer du den app som du vill börja använda.

    En ny sida öppnas för appen där du kan logga in (om det behövs) eller börja använda appen.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Komma åt och använda mina apps-portalen på en iOS-enhet

Visa och använda **Mina Apps** -portalen från en iPhone-eller iPad-enhet, som kör iOS version 7 eller senare. Du kan också installera [mobilappen för Mina appar](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) för att få åtkomst till din organisations appar på dina iOS-enheter.

1. Starta en webbapp på din mobila enhet, till exempel Safari.

2. Gå till https://myapps.microsoft.com eller Använd länken från din organisation om de dirigerar dig till en anpassad sida, till exempel https://myapps.microsoft.com/contoso.com .

    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som är tillgängliga för användning.

3. Från sidan **appar** väljer du den app som du vill börja använda.

    En ny sida öppnas för appen där du kan logga in (om det behövs) eller börja använda appen.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Komma åt och använda mina apps-portalen på en Android-enhet

Visa och använda **Mina Apps** -portalen på en Android-enhet.

1. Starta en webbapp på din mobila enhet, till exempel Google Chrome.

2. Gå till https://myapps.microsoft.com eller Använd länken från din organisation om de dirigerar dig till en anpassad sida, till exempel https://myapps.microsoft.com/contoso.com .

    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

3. Från sidan **appar** väljer du den app som du vill börja använda.

    En ny sida öppnas för appen där du kan logga in (om det behövs) eller börja använda appen.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Lägg till en ny app i portalen Mina appar

Om din administratör har gett dig behörighet kan du lägga till en ny app på sidan **appar** .

1. På sidan **appar** gör du något av följande:
    - Om du befinner dig i den ursprungliga appen Mina appar väljer du **Lägg till app** som visas.

      ![Sidan Lägg till appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - Om du har den uppdaterade appen Mina appar, väljer du **Lägg till självbetjänings appar**.

       ![Sidan Lägg till appar i portalen Mina appar på myapplications.microsoft.com](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. Välj den app som du vill lägga till i listan och välj sedan **Lägg till**.

3. Appen läggs till i listan på sidan **appar** .

    Vissa appar kan kräva administratörs godkännande innan de läggs till. När detta inträffar läggs appen inte till på sidan **appar** förrän administratören godkänner den.

## <a name="start-a-cloud-based-app"></a>Starta en molnbaserad app

Du kan starta någon av de tillgängliga molnbaserade apparna från portalen **Mina appar** . Du ser bara appar som du har behörighet att använda.

- Från sidan **appar** väljer du den app som du vill börja använda.

    En ny sida öppnas för appen där du kan logga in (om det behövs) eller börja använda appen.

## <a name="activities-in-the-my-apps-portal"></a>Aktiviteter i portalen för Mina appar

När du har gå till **Mina Apps** -portalen kan du:

- Organisera dina appar i de olika kategorier som skapas och tillhandahålls av din organisation. Mer information finns i [Access and use Collections in the My Apps-portalen](my-applications-portal-workspaces.md).

- Granska, uppdatera eller återkalla behörigheter som beviljats till program. Mer information finns i [Redigera eller återkalla program behörigheter i portalen Mina appar](my-applications-portal-permissions-saved-accounts.md).

>[!Note]
>Om du inte ser några samlingar eller kategorier tillgängliga, innebär det att administratören inte har konfigurerat eller delat några med dig. Kontakta din organisations supportavdelning om du behöver ytterligare hjälp eller behörigheter för att se delade samlingar.

## <a name="next-steps"></a>Nästa steg

När du kommer till sidan **appar** kan du:

- [Ändra din profilinformation](my-apps-portal-end-user-update-profile.md)

- [Visa och uppdatera dina grupper – relaterad information](my-apps-portal-end-user-groups.md)

- [Utföra egna åtkomstgranskningar](my-apps-portal-end-user-access-reviews.md)
