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
ms.openlocfilehash: cb31bb91c80e4d5dd032b009b40d8e3fc435e0c8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359467"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Du måste först registrera ditt program med Azure Active Directory (Azure AD)-klienten. Det ger dig en program-ID för ditt program och låter det ta emot tokens.

* Logga in på [Azure Portal](https://portal.azure.com).
* Välj Azure AD-klienten genom att klicka på ditt konto i det övre högra hörnet på sidan följt genom att klicka på den **växla katalog** navigering och välj sedan lämplig klienten. 
  * Hoppa över det här steget om du har endast en Azure AD-klient under ditt konto eller om du redan har valt rätt Azure AD-klient.
* I det vänstra navigeringsfönstret, klickar du på **Azure Active Directory**.
* Klicka på **Appregistreringar** och klicka på **ny programregistrering**.
* Följ anvisningarna och skapa ett nytt program. För den här kursen, spelar det ingen roll om det är en webbapp eller ett internt program, men om du behöver specifika exempel på webbprogram eller interna program kan du kolla vår [snabbstart](../articles/active-directory/develop/active-directory-developers-guide.md).
  * För webbprogram, anger du den **inloggnings-URL**, vilket är den grundläggande Webbadressen för din app, där användare kan logga in, t.ex `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * För interna program ger en **omdirigerings-URI**, som Azure AD ska använda för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel `http://MyFirstAADApp`
* När du har slutfört registreringen, tilldelar Azure AD ditt program en unik klientidentifierare, den **program-ID**. Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
* För att hitta ditt program i Azure portal, klickar du på **appregistreringar**, och klicka sedan på **visa alla program**.
