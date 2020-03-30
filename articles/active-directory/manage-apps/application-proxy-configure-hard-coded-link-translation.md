---
title: Översätt länkar och webbadresser Azure AD App Proxy | Microsoft-dokument
description: Täcker grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69533695"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Omdirigera hårdkodade länkar för appar som publicerats med Azure AD Application Proxy

Azure AD Application Proxy gör dina lokala appar tillgängliga för användare som är fjärranslutna eller på sina egna enheter. Vissa appar har dock utvecklats med lokala länkar inbäddade i HTML-koden. Dessa länkar fungerar inte korrekt när appen används på distans. När du har flera lokala program pekar på varandra förväntar sig användarna att länkarna fortsätter att fungera när de inte är på kontoret. 

Det bästa sättet att se till att länkar fungerar på samma sätt både i och utanför företagsnätverket är att konfigurera de externa webbadresserna till dina appar så att de är desamma som deras interna webbadresser. Använd [anpassade domäner](application-proxy-configure-custom-domain.md) för att konfigurera externa webbadresser så att företagets domännamn har i stället för standardprogramproxydomänen.


Om du inte kan använda anpassade domäner i din klientorganisation finns det flera andra alternativ för att tillhandahålla den här funktionen. Alla dessa är också kompatibla med anpassade domäner och varandra, så att du kan konfigurera anpassade domäner och andra lösningar om det behövs.

> [!NOTE]
> Länköversättning stöds inte för hårdkodade interna webbadresser som genereras via Javascript.

**Alternativ 1: Använd den hanterade webbläsaren eller Microsoft Edge** – Den här lösningen är endast tillämplig om du planerar att rekommendera eller kräva att användarna kommer åt programmet via Intune Managed Browser eller Microsoft Edge Browser. Det kommer att hantera alla publicerade webbadresser. 

**Alternativ 2: Använd MyApps Extension** - Den här lösningen kräver användare att installera en klient-side webbläsartillägg, men det kommer att hantera alla publicerade webbadresser och fungerar med de mest populära webbläsarna. 

**Alternativ 3: Använd inställningen länköversättning** – Det här är en administratörsinställning som är osynlig för användarna. Den hanterar dock bara webbadresser i HTML och CSS.   

Dessa tre funktioner håller dina länkar fungerar oavsett var användarna är. När du har appar som pekar direkt på interna slutpunkter eller portar kan du mappa dessa interna url:er till de publicerade externa programproxy-url:erna. 

 
> [!NOTE]
> Det sista alternativet är bara för klienter som av någon anledning inte kan använda anpassade domäner för att ha samma interna och externa webbadresser för sina appar. Innan du aktiverar den här funktionen kan du se om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md) kan fungera för dig. 
> 
> Om programmet som du behöver konfigurera med länköversättning är SharePoint läser du [Konfigurera alternativa åtkomstmappningar för SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) för en annan metod för mappning av länkar. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Alternativ 1: Intune Hanterad webbläsare och Microsoft Edge-integrering 

Du kan använda Intune Managed Browser eller Microsoft Edge för att ytterligare skydda ditt program och innehåll. Om du vill använda den här lösningen måste du kräva/rekommendera användare att komma åt programmet via Intune Managed Browser. Alla interna webbadresser som publiceras med programproxy kommer att kännas igen av den hanterade webbläsaren och omdirigeras till motsvarande externa URL. Detta säkerställer att alla hårdkodade interna webbadresser fungerar, och om en användare går till webbläsaren och direkt skriver den interna webbadressen, fungerar det även om användaren är avlägsen.  

Mer information, till exempel hur du konfigurerar det här alternativet, finns i dokumentationen [för hanterad webbläsare.](https://docs.microsoft.com/intune/app-configuration-managed-browser)  

### <a name="option-2-myapps-browser-extension"></a>Alternativ 2: MyApps webbläsartillägg 

Med MyApps Browser Extension, alla interna webbadresser som publiceras med Application Proxy känns igen av tillägget och omdirigeras till motsvarande externa URL. Detta säkerställer att alla hårdkodade interna webbadresser fungerar, och om en användare går till webbläsarens adressfält och direkt skriver den interna webbadressen, fungerar det även om användaren är avlägsen.  

För att kunna använda den här funktionen måste användaren hämta tillägget och vara inloggad. Det finns ingen annan konfiguration som behövs för administratörer eller användare. 

Mer information, inklusive hur du konfigurerar det här alternativet, finns i dokumentationen till [webbläsartillägget för MyApps.](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension)

### <a name="option-3-link-translation-setting"></a>Alternativ 3: Länköversättning 

När länköversättning är aktiverat söker tjänsten Application Proxy igenom HTML och CSS efter publicerade interna länkar och översätter dem så att användarna får en oavbruten upplevelse. Använda MyApps Browser Extension är att föredra framför link översättningsinställning eftersom det ger en mer högpresterande upplevelse för användarna.

> [!NOTE]
> Om du använder alternativ 2 eller 3 bör endast en av dessa aktiveras åt gången.

## <a name="how-link-translation-works"></a>Så här fungerar länköversättning

När proxyservern skickar programdata till användaren efter autentisering söker Application Proxy igenom programmet efter hårdkodade länkar och ersätter dem med sina respektive publicerade externa webbadresser.

Application Proxy förutsätter att program kodas i UTF-8. Om så inte är fallet anger du kodningstypen i `Content-Type:text/html;charset=utf-8`ett http-svarshuvud, till exempel .

### <a name="which-links-are-affected"></a>Vilka länkar påverkas?

Länköversättningsfunktionen söker bara efter länkar som finns i kodtaggar i en apps brödtext. Application Proxy har en separat funktion för att översätta cookies eller webbadresser i rubriker. 

Det finns två vanliga typer av interna länkar i lokala program:

- **Relativa interna länkar** som pekar på en delad `/claims/claims.html`resurs i en lokal filstruktur som . Dessa länkar fungerar automatiskt i appar som publiceras via Programproxy och fortsätter att arbeta med eller utan länköversättning. 
- **Hårdkodade interna länkar** till andra `http://expenses` lokala appar `http://expenses/logo.jpg`som eller publicerade filer som . Länköversättningsfunktionen fungerar på hårdkodade interna länkar och ändrar dem så att de pekar på de externa webbadresser som fjärranvändare behöver gå igenom.

Den fullständiga listan över HTML-kodtaggar som Application Proxy stöder länköversättning för inkluderar:
* a
* ljud
* base
* knapp
* div
* Bädda in
* Formuläret
* Ram
* Huvud
* html
* iframe
* bild
* indata
* länka
* Menuitem
* Meta
* objekt
* -skriptet
* källa
* spåra
* video

Dessutom översätts url-attributet i CSS.

### <a name="how-do-apps-link-to-each-other"></a>Hur länkar appar till varandra?

Länköversättning är aktiverat för varje program, så att du har kontroll över användarupplevelsen på nivån per app. Aktivera länköversättning för en app när du vill att länkarna *från* appen ska översättas, inte länkar *till* den appen. 

Anta till exempel att du har tre program publicerade via Programproxy som alla länkar till varandra: Förmåner, Utgifter och Resor. Det finns en fjärde app, Feedback, som inte publiceras via Application Proxy.

När du aktiverar länköversättning för appen Förmåner omdirigeras länkarna till Utgifter och Resor till externa webbadresser för dessa appar, men länken till feedback omdirigeras inte eftersom det inte finns någon extern URL. Länkar från utgifter och resor tillbaka till förmåner fungerar inte, eftersom länköversättning inte har aktiverats för dessa två appar.

![Länkar från förmåner till andra appar när länköversättning är aktiverat](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Vilka länkar översätts inte?

För att förbättra prestanda och säkerhet översätts inte vissa länkar:

- Länkar som inte finns inuti kodtaggar. 
- Länkar som inte finns i HTML eller CSS. 
- Länkar i URL-kodat format.
- Interna länkar öppnas från andra program. Länkar som skickas via e-post eller snabbmeddelanden, eller inkluderas i andra dokument, översätts inte. Användarna måste veta för att gå till den externa webbadressen.

Om du behöver stödja något av dessa två scenarier använder du samma interna och externa webbadresser i stället för länköversättning.  

## <a name="enable-link-translation"></a>Aktivera länköversättning

Det är lika enkelt att komma igång med länköversättning som att klicka på en knapp:

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **Enterprise-program** > **Alla program** > väljer den app som du vill hantera > Application **proxy**.
3. Förvandla **översätt webbadresser i programtexten** till **Ja**.

   ![Välj Ja om du vill översätta webbadresser i programtexten](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Klicka på **Spara** för att tillämpa dina ändringar.

Nu, när användarna kommer åt det här programmet, kommer proxyn automatiskt söka efter interna webbadresser som har publicerats via Application Proxy på din klient.

## <a name="send-feedback"></a>Skicka feedback

Vi vill ha din hjälp med att få den här funktionen att fungera för alla dina appar. Vi söker över 30 taggar i HTML och CSS. Om du har ett exempel på genererade länkar som inte översätts skickar du ett kodavsnitt till [Application Proxy Feedback](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Nästa steg
[Använda anpassade domäner med Azure AD Application Proxy](application-proxy-configure-custom-domain.md) för att ha samma interna och externa URL

[Konfigurera alternativa åtkomstmappningar för SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
