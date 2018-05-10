---
title: 'Översätta länkar och URL: er Azure AD App Proxy | Microsoft Docs'
description: Beskriver grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e2057241065ecefbbcd398ddf482961359f51de7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Omdirigera hårdkodad länkar till appar som publiceras med Azure AD Application Proxy

Azure AD Application Proxy gör dina lokala appar tillgängliga för användare som är fjärranslutna eller på sina egna enheter. Vissa appar har utvecklats med lokala länkar som är inbäddade i HTML. De här länkarna fungerar inte när appen används via fjärranslutning. När du har flera lokala program peka till varandra, räknar användarna länkar kan fortsätta arbeta när de inte är på kontoret. 

Det bästa sättet att se till att länkarna fungerar på samma sätt både inom och utanför företagets nätverk är att konfigurera de externa URL: er för dina appar för att vara samma som deras interna URL: er. Använd [anpassade domäner](active-directory-application-proxy-custom-domains.md) att konfigurera externa URL: er om du vill att företagets domännamn i stället för standarddomänen proxy.


Om du inte kan använda anpassade domäner i din klientorganisation, finns det flera alternativ för att tillhandahålla den här funktionen. Alla dessa är också kompatibla med anpassade domäner och varandra, så att du kan konfigurera anpassade domäner och andra lösningar om det behövs. 

**Alternativ 1: Använd den hanterade webbläsaren** – den här lösningen kan bara användas om du planerar att rekommenderar eller kräver att användare åtkomst till programmet via Intune Managed Browser. Det kan hantera alla publicerade URL: er. 

**Alternativ 2: Har filnamnstillägget MyApps** – denna lösning kräver användare att installera webbläsartillägget på klientsidan, men det kan hantera alla publicerade URL: er och fungerar i de flesta populära webbläsare. 

**Alternativ 3: Inställningen länken översättning** – detta är en administratör på klientsidan inställning som inte visas för användare. Det kan bara hantera URL: er i HTML- och CSS. Hårdkodade interna URL: er genereras via Javascript fungerar (till exempel) inte.  

Dessa tre funktioner Behåll länkarna fungerar oavsett var dina användarna finns. När du har appar som pekar direkt på interna slutpunkter eller portar, kan du mappa dessa interna URL: er publicerade externa Application Proxy URL: er. 

 
> [!NOTE]
> Det sista alternativet är endast för klienter som av något skäl inte kan använda anpassade domäner har samma interna och externa URL: er för sina appar. Innan du aktiverar den här funktionen finns om [anpassade domäner i Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md) du kan använda. 

>Eller, om programmet som du behöver konfigurera länk översättningen är SharePoint finns [konfigurera alternativa åtkomstmappningar för SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) en annan metod för mappning av länkar. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Alternativ 1: Intune Managed Browser Integration 

Du kan använda Intune Managed Browser för att ytterligare skydda dina program och innehåll. Om du vill använda den här lösningen måste du kräver/rekommenderar användare åtkomst till programmet via Intune Managed Browser. Alla interna URL: er publiceras med Application Proxy kan identifieras av den hanterade webbläsaren och omdirigeras till en motsvarande extern URL. Detta säkerställer att alla hårdkodade Intern URL fungerar och om en användare går till webbläsaren och direkt typer Intern URL, fungerar även om användaren är en fjärrplats.  

Mer information, inklusive hur du konfigurerar det här alternativet finns i [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentation.  

### <a name="option-2-myapps-browser-extension"></a>Alternativ 2: MyApps webbläsartillägg 

Med webbläsartillägget MyApps alla interna URL: er publiceras med Application Proxy identifieras av tillägget och omdirigeras till en motsvarande extern URL. Detta säkerställer att alla hårdkodade Intern URL fungerar och om en användare går till i webbläsarens adressfält och direkt typer Intern URL, fungerar även om användaren är en fjärrplats.  

Användaren måste ladda ned tillägget och logga in om du vill använda den här funktionen. Det finns ingen konfiguration krävs för administratörer eller användare. 

 

### <a name="option-3-link-translation-setting"></a>Alternativ 3: Länk anonymt 

När länköversättning är aktiverat tjänsten Application Proxy söker igenom HTML- och CSS för publicerade interna länkar och översätter dem så att användarna får en oavbruten upplevelse. 



## <a name="how-link-translation-works"></a>Hur länkar översättning fungerar

Efter autentisering när proxyservern skickar programdata till användaren, Application Proxy igenom program för hårdkodad länkar och ersätter dem med deras respektive publicerade externa URL: er.

Programproxy förutsätter att program är kodade i UTF-8. Om detta inte är fallet, ange kodningstyp i en http-Svarsrubrik som `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Vilka länkar påverkas?

Funktionen länken översättning söker bara efter länkar som finns i koden taggarna i en App. Programproxy har en separat funktion för översättning av cookies eller URL: er i rubriker. 

Det finns två vanliga typer av interna länkar i lokala program:

- **Relativa interna länkar** som pekar på en delad resurs i en lokal fil-struktur som `/claims/claims.html`. De här länkarna fungerar automatiskt i appar som publiceras via Application Proxy och fortsätter att fungera med eller utan länköversättning. 
- **Hårdkodad interna länkar** till andra lokala appar som `http://expenses` eller publicerade filer av `http://expenses/logo.jpg`. Funktionen länken översättning fungerar på hårdkodad interna länkar och ändrar dem så att den pekar till de externa URL: er som fjärranslutna användare behöver gå igenom.

### <a name="how-do-apps-link-to-each-other"></a>Hur länkar appar till varandra?

Länköversättning är aktiverat för varje program, så att du har kontroll över användarupplevelsen på per app-nivå. Aktivera länköversättning för en app när du vill att länkarna *från* appen översättas länkar inte *till* appen. 

Anta exempelvis att du har tre program som publicerats via Proxy för att alla länkar till varandra: fördelar, utgifter och resa. Det finns en fjärde app Feedback som inte publicerats via Application Proxy.

När du aktiverar länken översättningen av fördelar appen länkar till kostnader och resa omdirigeras till de externa URL: er för dessa appar, men länken till Feedback omdirigeras inte eftersom det inte finns några externa URL: en. Länkar från utgifter och resa till fördelar fungerar inte, eftersom länköversättning inte har aktiverats för dessa två appar.

![Länkar från fördelar till andra appar när länköversättning är aktiverad](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Vilka länkar inte är översatt?

För att förbättra prestanda och säkerhet, inte är vissa länkar översatt:

- Länkar inte inuti kodtaggar. 
- Länkar inte i HTML- eller CSS. 
- Interna länkar öppnas från andra program. Att kommer inte översätta länkar skickas via e-post eller snabbmeddelande eller ingår i andra dokument. Användarna behöver veta för att gå till den externa Webbadressen.

Om du behöver stöd för något av dessa två scenarier kan du använda samma interna och externa URL: er i stället för länköversättning.  

## <a name="enable-link-translation"></a>Aktivera länköversättning

Komma igång med länköversättning är lika enkelt som att klicka på en knapp:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **företagsprogram** > **alla program** > Välj den app som du vill hantera > **programproxy**.
3. Aktivera **översätta URL: er i programmet brödtext** till **Ja**.

   ![Välj Ja om du vill att översätta URL: er i programmet brödtext](./media/application-proxy-link-translation/select_yes.png).
4. Välj **spara** tillämpa dina ändringar.

När användarna har åtkomst till det här programmet ska nu proxyn automatiskt söka efter interna URL: er som har publicerats via Application Proxy på din klient.

## <a name="send-feedback"></a>Skicka feedback

Vi vill du att den här funktionen fungerar för alla dina appar. Vi söker efter 30 taggar i HTML- och CSS. Om du har ett exempel på genererade länkar som inte är som översätts skickar du ett kodstycke till [Application Proxy Feedback](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Nästa steg
[Använda anpassade domäner med Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md) ska ha samma interna och externa URL: en

[Konfigurera alternativa åtkomstmappningar för SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
