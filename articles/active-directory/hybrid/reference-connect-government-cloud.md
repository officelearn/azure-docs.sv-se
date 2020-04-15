---
title: 'Azure AD Connect: Hybrididentitetsöverväganden för Azure Government'
description: Särskilda överväganden för distribution av Azure AD Connect med myndighetsmolnet.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378930"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Hybrididentitetsöverväganden för Azure Government 
I följande dokument beskrivs övervägandena för att implementera en hybridmiljö med Azure Government-molnet.  Den här informationen tillhandahålls som referens för administratörer och arkitekter som arbetar med Azure Government-molnet.  
> [!NOTE] 
> För att integrera en lokal AD-miljö med Azure Governemnt-molnet måste du uppgradera till den senaste versionen av [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> En fullständig lista över dod-slutpunkter för amerikanska myndigheter finns i [dokumentationen](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Direktautentisering 
Följande information tillhandahålls för implementering av direktautentisering (PTA) och Azure Government-molnet.

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser  
Innan du distribuerar direktautentiseringsagenten kontrollerar du om det finns en brandvägg mellan servrarna och Azure AD. Om brandväggen eller proxyn tillåter DNS-vitlistning lägger du till följande anslutningar: 
> [!NOTE]
> Följande vägledning gäller även för installation av [Application Proxy-anslutningsappen](https://aka.ms/whyappproxy) för Azure Government-miljöer.

|URL |Hur den används|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Kommunikation mellan agenten och Azure AD-molntjänsten |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Agenten använder dessa url:er för att verifiera certifikat.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br> *.microsoftonline-p.us </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Agenten använder dessa webbadresser under registreringsprocessen.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installera agenten för Azure Government-molnet 
För att kunna installera agenten för Azure Government-molnet måste du följa dessa specifika steg: I kommandoradsterminalen navigerar du till mappen där den körbara filen för installation av agenten finns. Kör följande kommando som anger att installationen är för Azure Government. 

För passthrough-autentisering: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

För application proxy:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Enkel skylt på 
Konfigurera din Azure AD Connect-server: Om du använder direktautentisering som inloggningsmetod krävs ingen ytterligare nödvändig kontroll. Om du använder synkronisering av lösenordshã¥1er som inloggningsmetod och om det finns en brandvägg mellan Azure AD Connect och Azure AD kontrollerar du att:
- Du använder version 1.1.644.0 eller senare av Azure AD Connect. 
- Om brandväggen eller proxyn tillåter DNS-vitlistning lägger du till anslutningarna i *.msapproxy.us webbadresserna över port 443. Om inte, tillåt åtkomst till Azure datacenter IP-intervall, som uppdateras varje vecka. Den här förutsättningen gäller endast när du aktiverar funktionen. Det krävs inte för faktiska användarloggningar. 

### <a name="rolling-out-seamless-sso"></a>Rulla ut sömlös SSO 
Du kan gradvis distribuera Sömlös SSO till användarna med hjälp av instruktionerna nedan. Du börjar med att lägga till följande Azure AD-URL i alla eller valda användares zoninställningar för intranät med hjälp av Grupprincip i Active Directory:https://autologon.microsoft.us 

Dessutom måste du aktivera en zonprincipinställning för intranät som heter Tillåt uppdateringar av statusfältet via skript via grupprincip. Webbläsaröverväganden Mozilla Firefox (alla plattformar) Mozilla Firefox använder inte automatiskt Kerberos-autentisering. Varje användare måste manuellt lägga till Azure AD-URL:en i sina Firefox-inställningar med hjälp av följande steg: 
1. Kör Firefox och ange om: config i adressfältet. Avvisa alla aviseringar som visas. 
2. Sök efter inställningarna network.negotiate-auth.trusted-uris. Den här inställningen visar Firefox betrodda platser för Kerberos-autentisering. 
3. Högerklicka och välj Ändra. 
4. Skriv https://autologon.microsoft.us in i fältet .
5. Välj OK och öppna sedan webbläsaren igen. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baserat på krom (alla plattformar) 
Om du har åsidosatt `AuthNegotiateDelegateAllowlist`  `AuthServerAllowlist` principinställningarna eller i din miljö kontrollerar duhttps://autologon.microsoft.us) att du också lägger till Azure AD:s URL (till dem. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (alla plattformar) 
Om du har åsidosatt `AuthNegotiateDelegateWhitelist`  `AuthServerWhitelist` principinställningarna eller i din miljö kontrollerar duhttps://autologon.microsoft.us) att du också lägger till Azure AD:s URL (till dem. 

## <a name="next-steps"></a>Nästa steg
[Enkel inloggning för direktautentisering](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Single Sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
