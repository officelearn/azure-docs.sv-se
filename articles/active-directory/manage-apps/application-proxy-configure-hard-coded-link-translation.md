---
title: 'Översätta länkar och URL: er Azure AD App Proxy | Microsoft Docs'
description: Beskriver grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2949559542759cadf90d329bc50b352998b3eb7e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262558"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Omdirigera hårdkodad länkar till appar som publiceras med Azure AD Application Proxy

Azure AD-programproxyn gör lokala appar tillgängliga för användare som är fjärranslutna eller på sina egna enheter. Vissa appar har utvecklats med lokala länkar som är inbäddad i HTML. Länkarna fungerar inte när appen används via fjärranslutning. När du har flera lokala program som pekar på varandra kan användarna förväntar sig länkar kan fortsätta att arbeta när de inte är på kontoret. 

Det bästa sättet att se till att länkar fungerar på samma både i och utanför företagets nätverk är att konfigurera de externa URL: er för dina appar för att vara samma som deras interna URL: er. Använd [anpassade domäner](application-proxy-configure-custom-domain.md) att konfigurera din externa URL: er om du vill att företagets domännamn i stället för standarddomänen application proxy.


Om du inte kan använda anpassade domäner i din klient, finns det flera alternativ för att tillhandahålla den här funktionen. Alla dessa är också kompatibla med anpassade domäner och varandra, så att du kan konfigurera anpassade domäner och andra lösningar om det behövs. 

**Alternativ 1: Använda Managed Browser** – den här lösningen gäller endast om du planerar att rekommenderar eller kräva att användarna kommer åt programmet via Intune Managed Browser. Det kan hantera alla publicerade URL: er. 

**Alternativ 2: Använda tillägget MyApps** – den här lösningen kräver att användarna ska kunna installera ett webbläsartillägg på klientsidan, men det kan hantera alla publicerade URL: er och fungerar i de flesta populära webbläsare. 

**Alternativ 3: Använd länken translation inställningen** – detta är en administratör på klientsidan inställning som inte visas för användarna. Det kan endast hantera URL: er i HTML och CSS. Hårdkodade interna URL: er genereras via Javascript fungerar (till exempel) inte.  

Dessa tre funktioner Behåll länkarna fungerar oavsett var dina användare är. När du har appar som pekar direkt på interna slutpunkter eller portar, kan du mappa dessa interna URL: er publicerade externa Application Proxy URL: er. 

 
> [!NOTE]
> Det sista alternativet är endast för klienter som av något skäl inte kan använda anpassade domäner har samma interna och externa URL: er för sina appar. Innan du aktiverar den här funktionen kan du se om [anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md) kan hjälpa dig. 
> 
> Eller, om programmet som du vill konfigurera med länken är translation SharePoint finns i [konfigurera alternativa åtkomstmappningar för SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) en annan metod för mappning av länkar. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Alternativ 1: Intune Managed Browser-integrering 

Du kan använda Intune Managed Browser för att ytterligare skydda ditt program och innehåll. Om du vill använda den här lösningen, måste du kräver/rekommenderar användare åtkomst till programmet via Intune Managed Browser. Alla interna URL: er som publiceras med Application Proxy ska identifieras av Managed Browser och omdirigeras till motsvarande extern URL. Detta säkerställer att alla hårdkodade interna URL: er fungerar och om en användare går till webbläsaren och skriver den interna URL: en direkt, den fungerar även om användaren är en fjärrplats.  

Mer information, inklusive hur du konfigurerar det här alternativet finns i [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentation.  

### <a name="option-2-myapps-browser-extension"></a>Alternativ 2: MyApps webbläsartillägg 

Med webbläsartillägget MyApps är alla interna URL: er som publiceras med Application Proxy kan identifieras av tillägget och omdirigeras till motsvarande extern URL. Detta säkerställer att alla hårdkodade interna URL: er fungerar och om en användare går till i webbläsarens adressfält och skriver den interna URL: en direkt, den fungerar även om användaren är en fjärrplats.  

Användaren måste ladda ned tillägget och logga in om du vill använda den här funktionen. Det finns ingen annan konfiguration som krävs för administratörer eller användare. 

 

### <a name="option-3-link-translation-setting"></a>Alternativ 3: Link Translation Setting 

När länken translation är aktiverat programproxytjänsten söker igenom HTML och CSS för publicerade interna länkar och omvandlar dem så att användarna får en oavbruten upplevelse. 



## <a name="how-link-translation-works"></a>Hur länkar translation fungerar

Efter autentisering, när proxyservern skickas programdata till användaren, Application Proxy söker igenom program för hårdkodad länkar och ersätter dem med deras respektive publicerade externa URL: er.

Programproxy förutsätter att program är kodade i UTF-8. Om detta inte är fallet, ange kodningstyp i en http-Svarsrubrik som `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Vilka länkar som påverkas?

Översättningsfunktionen länk söker bara efter länkar som finns i kodtaggarna i brödtexten i en app. Programproxyn har en separat funktion för översättning av cookies eller URL: er i rubriker. 

Det finns två vanliga typer av interna länkar i dina lokala program:

- **Relativa interna länkar** som pekar på en delad resurs i en lokal fil-struktur som `/claims/claims.html`. Länkarna fungerar automatiskt i appar som publicerats via programproxy och fortsätta att arbeta med eller utan länköversättning. 
- **Hårdkodad interna länkar** till andra lokala appar som `http://expenses` eller publicerat filer som `http://expenses/logo.jpg`. Översättningsfunktionen länken fungerar på hårdkodad interna länkar och ändrar dem så att den pekar till de externa URL: er som fjärranslutna användare måste du gå igenom.

Den fullständiga listan med HTML-kod taggar som Application Proxy stöder länköversättning för inkludera:
* a
* Ljud
* Base
* Knappen
* div
* Bädda in
* formuläret
* ramens
* HEAD
* html
* IFRAME
* bild
* indata
* länk
* MenuItem
* meta
* objekt
* skript
* källa
* Spåra
* video

Dessutom översättas URL-attributet i CSS också.

### <a name="how-do-apps-link-to-each-other"></a>Hur länkar appar till varandra?

Länköversättning är aktiverat för varje program, så att du har kontroll över användarupplevelsen på per app-nivå. Aktivera länköversättning för en app när du vill ha länkarna *från* appen ska översättas länkar inte *till* appen. 

Anta att du har tre program som publicerats via programproxy att alla länkar till varandra: Fördelar, kostnader och resa. Det finns en fjärde app Feedback, som inte har publicerats via programproxy.

När du aktiverar länköversättning för appen fördelar, länkar till utgifter och resor omdirigeras till de externa URL: er för dessa appar, men en länk till Feedback omdirigeras inte eftersom det finns inga externa URL: en. Länkar från utgifter och resor tillbaka till förmånerna fungerar inte, eftersom länken translation inte har aktiverats för dessa två appar.

![Länkar från förmåner till andra appar när länken translation är aktiverat](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Vilka länkar inte är översatt?

För att förbättra prestanda och säkerhet, inte är länkar som översatt:

- Länkar inte i kodtaggarna. 
- Länkar inte i HTML- eller CSS. 
- Länkar i URL-kodat format.
- Interna länkar öppnas från andra program. Länkar skickas via e-post eller snabbmeddelande eller ingår i andra dokument kommer inte att översätta. Användarna behöver veta för att gå till den externa URL: en.

Om du behöver stöd för ett av dessa två scenarier kan du använda samma interna och externa URL: er i stället länköversättning.  

## <a name="enable-link-translation"></a>Aktivera länköversättning

Det är lika enkelt som att klicka på en knapp för att komma igång med länköversättning:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **företagsprogram** > **alla program** > Välj den app som du vill hantera >  **Programproxy**.
3. Aktivera **Översätt URL: er i brödtext för program** till **Ja**.

   ![Välj Ja om du vill översätta URL: er i brödtext för program](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Välj **spara** att tillämpa ändringarna.

Nu när dina användare har åtkomst till det här programmet, genomsökas proxyn för interna URL: er som har publicerats via programproxy på din klient.

## <a name="send-feedback"></a>Skicka feedback

Vi vill ha din hjälp för att den här funktionen fungerar för alla dina appar. Vi söker efter 30 taggar i HTML och CSS. Om du har ett exempel på genererade länkar som inte är som översätts skickar du ett kodfragment till [Proxy programfeedback](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Nästa steg
[Använda anpassade domäner med Azure AD Application Proxy](application-proxy-configure-custom-domain.md) ha samma interna och externa URL: en

[Konfigurera alternativa åtkomstmappningar för SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
