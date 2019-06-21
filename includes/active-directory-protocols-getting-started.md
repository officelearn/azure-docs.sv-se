---
title: Azure AD .NET-protokollöversikt | Microsoft Docs
description: Använd HTTP-meddelanden för att auktorisera åtkomst till webbappar och webb-API:er i din klient med hjälp av Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187516"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Du måste först registrera ditt program med Azure Active Directory (Azure AD)-klienten. Det ger dig en program-ID för ditt program och låter det ta emot tokens.

* Logga in på [Azure Portal](https://portal.azure.com).
* Välj Azure AD-klienten genom att klicka på ditt konto i det övre högra hörnet på sidan följt genom att klicka på den **växla katalog** navigering och välj sedan lämplig klienten. 
  * Hoppa över det här steget om du bara har en Azure AD-klientorganisation under ditt konto eller om du redan har valt den rätta Azure AD-klientorganisationen.
* I det vänstra navigeringsfönstret, klickar du på **Azure Active Directory**.
* Klicka på **Appregistreringar** och klicka på **ny registrering**.
* Följ anvisningarna och skapa ett nytt program. Den inte är viktiga för dig om det är ett webbprogram eller en offentlig (mobila och stationära) klientprogram för den här självstudien, men om du vill ha specifika exempel på webbprogram eller offentliga klientprogram, Kolla in vår [snabbstarter](../articles/active-directory/develop/v1-overview.md).
  * **Namn** är appens namn och beskriver appen för användarna.
  * Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
  * Ange den **omdirigerings-URI**. För webbprogram, det här är den grundläggande Webbadressen för din app där användare kan logga in.  Till exempel `http://localhost:12345`. För offentlig klient (mobila och stationära) använder Azure AD den för att returnera tokensvar. Ange ett värde som är specifika för ditt program.  Till exempel `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* När du har slutfört registreringen, tilldelar Azure AD ditt program en unik klientidentifierare (den **program-ID**). Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
* För att hitta ditt program i Azure portal, klickar du på **appregistreringar**, och klicka sedan på **visa alla program**.
