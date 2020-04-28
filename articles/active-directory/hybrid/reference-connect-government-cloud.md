---
title: 'Azure AD Connect: hybrid identitets överväganden för Azure Government'
description: Särskilda överväganden vid distribution av Azure AD Connect med det offentliga molnet.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378930"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Hybrididentitetsöverväganden för Azure Government 
I följande dokument beskrivs överväganden för att implementera en hybrid miljö med Azure Government molnet.  Den här informationen tillhandahålls som referens för administratörer och arkitekter som arbetar med Azure Government molnet.  
> [!NOTE] 
> För att integrera en lokal AD-miljö med Azure Governemnt-molnet måste du uppgradera till den senaste versionen av [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> En fullständig lista över DoD-slutpunkter för amerikanska myndigheter finns i [dokumentationen](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Direktautentisering 
Följande information finns för implementering av direktautentisering (PTA) och Azure Government molnet.

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser  
Innan du distribuerar genom strömnings agenten kontrollerar du om det finns en brand vägg mellan dina servrar och Azure AD. Om din brand vägg eller proxy tillåter DNS-vit listning, lägger du till följande anslutningar: 
> [!NOTE]
> Följande rikt linjer gäller också när du installerar [Application Proxy-anslutningen](https://aka.ms/whyappproxy) för Azure Government miljöer.

|URL |Hur den används|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Kommunikation mellan agenten och moln tjänsten Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Agenten använder dessa URL: er för att verifiera certifikat.| 
|login.Windows.us Secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.net </br> *. msauthimages.net </br>*. msecnd.net</br>*. msftauth.net </br>*. msftauthimages.net</br>*. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Agenten använder dessa URL: er under registrerings processen.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installera agenten för Azure Government molnet 
För att kunna installera agenten för Azure Government molnet måste du följa dessa steg: i terminalen kommando rad navigerar du till mappen där den körbara filen för att installera agenten finns. Kör följande kommando som anger att installationen är för Azure Government. 

För genom strömnings autentisering: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

För Application Proxy:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Enkel inloggning 
Konfigurera din Azure AD Connect Server: om du använder direktautentisering som inloggnings metod krävs ingen ytterligare krav kontroll. Om du använder Password-hash-synkronisering som inloggnings metod och om det finns en brand vägg mellan Azure AD Connect och Azure AD, kontrollerar du att:
- Du använder version 1.1.644.0 eller senare av Azure AD Connect. 
- Om din brand vägg eller proxy tillåter DNS-vit listning, lägger du till anslutningarna i URL: erna *. msapproxy.us via port 443. Om inte, Tillåt åtkomst till Azure datacenter IP-intervall som uppdateras varje vecka. Den här förutsättningen gäller endast när du aktiverar funktionen. Det krävs inte för faktiska användar inloggningar. 

### <a name="rolling-out-seamless-sso"></a>Rulla ut sömlös SSO 
Du kan gradvis distribuera sömlös SSO till dina användare med hjälp av anvisningarna nedan. Du börjar med att lägga till följande Azure AD-URL till alla eller valda användares intranät zons inställningar med hjälp av grupprincip i Active Directory:https://autologon.microsoft.us 

Dessutom måste du aktivera en princip inställning för intranät zon som kallas Tillåt uppdateringar till statusfältet via skript genom att grupprincip. Webb läsar överväganden Mozilla Firefox (alla plattformar) i Mozilla Firefox används inte Kerberos-autentisering automatiskt. Varje användare måste manuellt lägga till Azure AD-URL: en i sina Firefox-inställningar med hjälp av följande steg: 
1. Kör Firefox och ange about: config i adress fältet. Stäng alla meddelanden som visas. 
2. Sök efter nätverket. inställningar för Negotiate-auth. Trusted-URI: er. Den här inställningen listar Firefoxs betrodda platser för Kerberos-autentisering. 
3. Högerklicka och välj Ändra. 
4. Ange https://autologon.microsoft.us i fältet.
5. Välj OK och öppna sedan webbläsaren igen. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baserat på krom (alla plattformar) 
Om `AuthNegotiateDelegateAllowlist` du har åsidosatt eller `AuthServerAllowlist` princip inställningarna i din miljö kontrollerar du att du lägger till Azure AD-URL: en (https://autologon.microsoft.us) till dem också. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (alla plattformar) 
Om `AuthNegotiateDelegateWhitelist` du har åsidosatt eller `AuthServerWhitelist` princip inställningarna i din miljö kontrollerar du att du lägger till Azure AD-URL: en (https://autologon.microsoft.us) till dem också. 

## <a name="next-steps"></a>Nästa steg
[Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Enkel inloggning med](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) direkt autentisering 
