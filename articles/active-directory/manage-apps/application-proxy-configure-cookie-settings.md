---
title: Inställningar för programproxycookie - Azure Active Directory | Microsoft-dokument
description: Azure Active Directory (Azure AD) har åtkomst- och sessionscookies för åtkomst till lokala program via programproxy. I den här artikeln får du reda på hur du använder och konfigurerar cookie-inställningarna.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481372"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-inställningar för åtkomst till lokala program i Azure Active Directory

Azure Active Directory (Azure AD) har åtkomst- och sessionscookies för åtkomst till lokala program via programproxy. Ta reda på hur du använder inställningarna för application proxy cookie. 

## <a name="what-are-the-cookie-settings"></a>Vilka är cookie-inställningarna?

[Application Proxy](application-proxy.md) använder följande inställningar för åtkomst- och sessionscookie.

| Inställningen Cookie | Default | Beskrivning | Rekommendationer |
| -------------- | ------- | ----------- | --------------- |
| Använd endast HTTP-cookie | **Nej** | **Ja** tillåter att programproxy inkluderas i HTTPOnly-flaggan i HTTP-svarshuvuden. Den här flaggan ger ytterligare säkerhetsfördelar, till exempel förhindrar det skript på klientsidan (CSS) från att kopiera eller ändra cookies.<br></br><br></br>Innan vi stödde HTTP-only-inställningen krypterade och överförde Application Proxy cookies via en säker TLS-kanal för att skydda mot ändringar. | Använd **Ja** på grund av de ytterligare säkerhetsfördelarna.<br></br><br></br>Använd **Nej** för klienter eller användaragenter som kräver åtkomst till sessionscookien. Använd till exempel **Nej** för en RDP- eller MTSC-klient som ansluter till en server för fjärrskrivbordsgateway via Programproxy.|
| Använd säker cookie | **Nej** | **Ja** tillåter att programproxy inkluderas i den säkra flaggan i HTTP-svarshuvuden. Säkra cookies ökar säkerheten genom att överföra cookies via en TLS-säker kanal som HTTPS. Detta förhindrar att cookies observeras av obehöriga parter på grund av överföringen av cookien i klartext. | Använd **Ja** på grund av de ytterligare säkerhetsfördelarna.|
| Använd beständig cookie | **Nej** | **Ja** tillåter Application Proxy att ställa in sin åtkomst cookies inte upphör att gälla när webbläsaren är stängd. Persistensen varar tills åtkomsttoken går ut, eller tills användaren manuellt tar bort de beständiga cookiesna. | Använd **Nej** på grund av säkerhetsrisken som är associerad med att hålla användarna autentiserade.<br></br><br></br>Vi föreslår att du bara använder **Ja** för äldre program som inte kan dela cookies mellan processer. Det är bättre att uppdatera ditt program för att hantera delningscookies mellan processer istället för att använda beständiga cookies. Du kan till exempel behöva beständiga cookies för att tillåta en användare att öppna Office-dokument i explorer-vyn från en SharePoint-webbplats. Utan beständiga cookies kan den här åtgärden misslyckas om åtkomstcookiesna inte delas mellan webbläsaren, utforskaren och Office-processen. |

## <a name="samesite-cookies"></a>Cookies på samma plats
Från och med version Chrome 80 och så småningom i webbläsare utnyttja Krom, cookies som inte anger [SameSite](https://web.dev/samesite-cookies-explained) attributet kommer att behandlas som om de var inställda på **SameSite = Lax**. Attributet SameSite förklarar hur cookies ska begränsas till en kontext på samma webbplats. När den är inställd på Lax skickas cookien endast till förfrågningar på samma plats eller navigering på högsta nivå. Programproxy kräver dock att dessa cookies bevaras i tredjepartssammanhang för att användarna ska kunna loggas in korrekt under sessionen. På grund av detta gör vi uppdateringar av Application Proxy-åtkomsten och sessionscookies för att undvika negativa effekter av den här ändringen. Uppdateringarna inkluderar:

* Ange attributet **SameSite** till **Ingen**. Detta gör att Application Proxy åtkomst och sessioner cookies som ska skickas på rätt sätt i tredje part sammanhang.
* Ställa in inställningen **Använd säker cookie** för att använda **Ja** som standard. Chrome kräver också att cookies anger flaggan Säker, annars avvisas den. Den här ändringen gäller för alla befintliga program som publiceras via Application Proxy. Observera att cookies för åtkomst till application proxy alltid har ställts in på Säker och endast överförts via HTTPS. Den här ändringen gäller endast sessionscookies.

Dessa ändringar av Application Proxy cookies kommer att rulla ut under loppet av de närmaste veckorna före Chrome 80 release datum.

Om din backend-applikation dessutom har cookies som måste vara tillgängliga i en tredjepartskontext måste du uttryckligen anmäla dig genom att ändra ditt program för att använda SameSite=None för dessa cookies. Application Proxy översätter Set-Cookie-huvudet till sina webbadresser och kommer att respektera inställningarna för dessa cookies som ställts in av backend-programmet.



## <a name="set-the-cookie-settings---azure-portal"></a>Ange cookie-inställningar - Azure-portal
Så här ställer du in cookie-inställningarna med Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Navigera till **Azure Active Directory** > **Enterprise-program** > **Alla program**.
3. Välj det program som du vill aktivera en cookie-inställning för.
4. Klicka på **Programproxy**.
5. Under **Ytterligare inställningar**ställer du in cookie-inställningen på **Ja** eller **Nej**.
6. Klicka på **Spara** för att tillämpa dina ändringar. 

## <a name="view-current-cookie-settings---powershell"></a>Visa aktuella cookie-inställningar - PowerShell

Om du vill se de aktuella cookie-inställningarna för programmet använder du det här PowerShell-kommandot:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Ange cookie-inställningar - PowerShell

I följande PowerShell-kommandon ```<ObjectId>``` är programmets ObjectId. 

**Http-Endast Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Säker cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Beständiga cookies**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
