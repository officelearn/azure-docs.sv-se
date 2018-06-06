---
title: Azure AD .NET-protokollöversikt | Microsoft Docs
description: Använd HTTP-meddelanden för att auktorisera åtkomst till webbappar och webb-API:er i din klient med hjälp av Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 72fb594265e69eb1dc16cb29ad4df6acb3a87720
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34663635"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Du måste först registrera programmet med din Azure Active Directory (Azure AD)-klient. Det ger dig en program-ID för ditt program och låter det ta emot tokens.

* Logga in på [Azure Portal](https://portal.azure.com).
* Välj din Azure AD-klient genom att klicka på ditt konto i det övre högra hörnet på sidan.
* I det vänstra navigeringsfönstret, klickar du på **Azure Active Directory**.
* Klicka på **App registreringar** och klicka på **nya appregistrering**.
* Följ anvisningarna och skapa ett nytt program. För den här kursen, spelar det ingen roll om det är en webbapp eller ett internt program, men om du behöver specifika exempel på webbprogram eller interna program kan du kolla vår [snabbstart](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Webbprogram, ange den **inloggnings-URL**, vilket är den grundläggande Webbadressen för din app, där användarna kan logga in t.ex `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * För interna program innehåller en **omdirigerings-URI**, som använder Azure AD för att returnera token svar. Ange ett värde som är specifikt för ditt program, till exempel `http://MyFirstAADApp`
* När du har slutfört registreringen, Azure AD tilldelas programmet ett unikt klient-ID, den **program-ID**. Du behöver det här värdet i nästa avsnitt så kopiera den från appen på sidan.
* Om du vill hitta ditt program i Azure-portalen klickar du på **App registreringar**, och klicka sedan på **visa alla program**.