---
title: Cookie programproxyinställningar - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) har åtkomst- och sessionsprinciper cookies för åtkomst till lokala program via programproxyn. I den här artikeln får du veta hur du använder och konfigurera cookie-inställningar.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: concept
ms.date: 01/16/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 6bd28ba3c7f94a3eb398d0819f1d8df42a765923
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960022"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-inställningar för att komma åt lokala program i Azure Active Directory

Azure Active Directory (Azure AD) har åtkomst- och sessionsprinciper cookies för åtkomst till lokala program via programproxyn. Ta reda på hur du använder cookie-inställningar för Application Proxy. 

## <a name="what-are-the-cookie-settings"></a>Vad är cookie-inställningar?

[Programproxy](application-proxy.md) använder följande inställningar för åtkomst och sessions-cookie.

| Cookie-inställning | Standard | Beskrivning | Rekommendationer |
| -------------- | ------- | ----------- | --------------- |
| Använd endast HTTP-cookie | **Nej** | **Ja** tillåter Application Proxy att inkludera flaggan HTTPOnly i HTTP-svarshuvuden. Den här flaggan innehåller ytterligare säkerhetsfördelarna, till exempel förhindras klientsidan scripting (CSS) kopiera eller ändrar cookies.<br></br><br></br>Innan vi stöds endast HTTP-inställningen Application Proxy krypteras och överförs cookies via en säker SSL-kanal för att skydda mot ändring. | Använd **Ja** på grund av de ytterligare säkerhetsfördelarna.<br></br><br></br>Använd **nr** för klienter eller användaragenter som kräver åtkomst till sessions-cookie. Till exempel använda **nr** för en RDP- eller MTSC-klient som ansluter till en Remote Desktop Gateway-server via programproxy.|
| Använd säker cookie | **Nej** | **Ja** tillåter Application Proxy att inkludera Secure flaggan i HTTP-svarshuvuden. Säkra Cookies ökar säkerheten genom att överföra cookies via en säker TLS-kanal, till exempel HTTPS. Detta förhindrar att cookies som observeras obehöriga personer på grund av överföring av cookien i klartext. | Använd **Ja** på grund av de ytterligare säkerhetsfördelarna.|
| Använd beständig cookie | **Nej** | **Ja** tillåter Application Proxy att ange dess åtkomst cookies för att inte upphör att gälla när webbläsaren stängs. Persistence varar tills den åtkomst-token upphör att gälla eller tills användaren manuellt tar bort beständiga cookies. | Använd **nr** på grund av säkerhetsrisk som är associerade med gör att användare som autentiseras.<br></br><br></br>Vi föreslår att du endast använder **Ja** för äldre program som inte kan dela cookies mellan processer. Är det bättre att uppdatera programmet ska hantera delning cookies mellan processer istället för att använda beständiga cookies. Du kanske till exempel behöver beständiga cookies för att tillåta användare att öppna Office-dokument i explorer-vy från en SharePoint-webbplats. Utan beständiga cookies kan den här åtgärden misslyckas om cookies som åtkomst inte delas mellan webbläsaren, explorer-processen och Office-process. |

## <a name="set-the-cookie-settings---azure-portal"></a>Ange cookie-inställningar – Azure-portalen
Ange cookie-inställningar med hjälp av Azure portal:

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Gå till **Azure Active Directory** > **företagsprogram** > **alla program**.
3. Välj det program som du vill aktivera en cookie-inställning.
4. Klicka på **programproxy**.
5. Under **ytterligare inställningar**, ange inställningen cookie till **Ja** eller **nr**.
6. Klicka på **spara** att tillämpa ändringarna. 

<!---

## View current cookie settings - PowerShell

To see the current cookie settings for the application, use this PowerShell command:  

```PowerShell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## Set cookie settings - PowerShell

In the following PowerShell commands, ```<ObjectId>``` is the ObjectId of the application. 

**Http-Only Cookie** 

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Secure Cookie**

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Persistent Cookies**

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```

-->
