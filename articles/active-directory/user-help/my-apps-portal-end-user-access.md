---
title: Komma åt och använda appar på portalen Mina appar - Azure Active Directory | Microsoft Docs
description: Lär dig hur du kommer åt portalen Mina appar.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaed7357221f5b766bfb8b9e1a9031c08854ac9a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340131"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>Öppna och använda appar på portalen Mina appar
Du kan använda ditt arbets- eller skolkonto med den webbaserade **Mina appar** portalen för att visa och starta många av organisationens molnbaserade appar, att uppdatera en del av din profil och konto information att se din **grupper** information och utföra **åtkomstgranskningar** för dina appar och grupper. Om du inte har åtkomst till den **Mina appar** portalen, måste du kontakta din supportavdelning för behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Innehållet är avsett för användare. Om du är administratör kan du hittar mer information om hur du konfigurerar och hanterar dina molnbaserade appar i den [dokumentation om Application Management](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Webbläsare som stöds
Du får den **Mina appar** portalen från någon av följande webbläsare:

- Google Chrome

- Mozilla Firefox version 26.0 eller senare

- Microsoft Edge

- Internet Explorer version 11 (begränsat stöd)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Ladda ned och installera den Mina appar skyddat Inloggningstillägg
Hämta och installera den Mina appar skyddat Inloggningstillägg, om du uppmanas. Det här tillägget kan du starta någon av molnappar i din organisation som kräver att du kan använda en process med enkel inloggning. Om din organisation har redan in du för enkel inloggning, tillägget installeras automatiskt och du kan hoppa över det här avsnittet.

Det här tillägget kan du: 

- Logga in direkt på appar från sidan logga in.

- Starta några appar med hjälp av den **Snabbsökning** funktionen.

- Se de senaste apparna som du använde i den **nyligen använda** avsnittet.

- Använda internt företag URL: er när fjärrdata med [Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Ladda ned och installera tillägget
Hämta och installera tillägget, baserat på den webbläsare du använder:

- **Google Chrome.** Från Chrome Web Store, går du till den [Mina appar skyddat Inloggningstillägg](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) funktionen och välj sedan **lägga till i Chrome**.

- **Mozilla Firefox** från den **Firefox tillägg** går du till den [Mina appar skyddat Inloggningstillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/) funktionen och välj sedan **lägga till i Firefox**.

- **Microsoft Edge** från the Microsoft Store, gå till den [Mina appar skyddat Inloggningstillägg](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) funktionen och välj sedan **hämta**.

En ikon har lagts till höger om din **adress** stapeln, så att du kan logga in och anpassa tillägget.

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Ändra din Mina appar-portalen med hjälp av tillägget
Du kan välja hur många appar för att visa i den **nyligen använda** avsnittet och Bestäm om du vill att din organisations interna URL: er att omdirigera.

1. Välj den nya **Mina appar skyddat Inloggningstillägg** ikonen ![tilläggsikonen](media/my-apps-portal/my-apps-portal-extension-icon.png) till höger om din **adress** , och välj sedan **logga in att få igång**.

2. Högerklicka på den **inställningar** ikonen ![inställningsikonen](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), och välj sedan **inställningar**.

3. I den **inställningar** rutan, väljer du antalet senast använda appar som du vill ska visas på portalen och om att din organisation är interna URL: er att omdirigera så du kan använda dem via en fjärranslutning.

    ![Inställningssidan för tillägg, som visar tillgängliga anpassningar](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Öppna och använda portalen Mina appar av enheten
Du kan komma åt och använda portalen Mina appar på datorn från en Intune-hanterade webbläsare eller från en iOS- eller Android-mobilenhet.

![Sidan appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Öppna och använda portalen Mina appar på datorn
Om du har behörighet att komma åt och använda molnbaserade appar i din organisation kan du kan nå dem via den **Mina appar** portal.

1.  Logga in på ditt arbets- eller skolkonto konto.

2.  Öppna webbläsaren och gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Exempelvis kan du dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Den **appar** visas, som visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

3. Från den **appar** väljer du den app du vill börja använda.
    
    En ny sida öppnas för appen, där du kan logga in (vid behov) eller börja använda appen.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Öppna och använda portalen Mina appar på en Intune-hanterade webbläsare
Visa och använda din organisations appar från Intune Managed Browser på iOS- och Android-enheter.

1. På din mobila enhet, ladda ned och installera appen Intune Managed Browser från den [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) och [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Öppna appen Intune Managed Browser, gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Exempelvis kan du dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Den **appar** visas, som visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

3. Från den **appar** väljer du den app du vill börja använda.
    
    En ny sida öppnas för appen, där du kan logga in (vid behov) eller börja använda appen.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Öppna och använda portalen Mina appar på en iOS-enhet
Visa och använda den **Mina appar** portalen från en iPhone eller iPad-enhet kör iOS version 7 eller senare. Du kan också installera den [Mina appar mobilappen](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) att komma åt din organisations appar på iOS-enheter. 

1. Starta en web browser-appen, till exempel Safari på din mobila enhet.

2. Gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Exempelvis kan du dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Den **appar** visas, som visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

3. Från den **appar** väljer du den app du vill börja använda.
    
    En ny sida öppnas för appen, där du kan logga in (vid behov) eller börja använda appen.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Öppna och använda portalen Mina appar på en Android-enhet
Visa och använda den **Mina appar** portal på en Android-enhet.

1. Starta en web browser-appen, till exempel Google Chrome på din mobila enhet.

2. Gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Exempelvis kan du dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Den **appar** visas, som visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

3. Från den **appar** väljer du den app du vill börja använda.
    
    En ny sida öppnas för appen, där du kan logga in (vid behov) eller börja använda appen.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Lägg till en ny app i portalen Mina appar
Om din administratör har gett dig behörighet kan du lägga till en ny app till den **appar** sidan.

1. Från den **appar** väljer **Lägg till App**.

    Den **Lägg till appar** visas.

    ![Lägg till appar sidan i portalen Mina appar](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Välj den app som du vill lägga till i listan och välj sedan **Lägg till**.

3. Appen har lagts till i listan på den **appar** sidan.

    Vissa appar kan kräva administratörsgodkännande innan som läggs till. I så fall kan appen inte har lagts till till den **appar** sidan tills administratören godkänner den.

## <a name="next-steps"></a>Nästa steg
När du kommer till den **appar** kan du kan utföra följande åtgärder:

- [Ändra profilinformationen](my-apps-portal-end-user-update-profile.md).

- [Visa och uppdatera dina grupper-relaterad information](my-apps-portal-end-user-groups.md).

- [Utföra egna åtkomstgranskningar](my-apps-portal-end-user-access-reviews.md).