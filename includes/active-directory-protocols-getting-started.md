---
title: "Azure AD .NET-protokollöversikt | Microsoft Docs"
description: "Använd HTTP-meddelanden för att auktorisera åtkomst till webbappar och webb-API:er i din klient med hjälp av Azure AD."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
ms.openlocfilehash: 42ce8dfd30cda7d4085778954350550fd9fdf13d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
## Registrera ditt program med din AD-klient
Först måste du registrera ditt program med din Azure Active Directory (Azure AD)-klient. Det ger dig en program-ID för ditt program och låter det ta emot tokens.

* Logga in på [Azure Portal](https://portal.azure.com).
* Välj din Azure AD-klient genom att klicka på ditt konto i det övre högra hörnet på sidan.
* I det vänstra navigeringsfönstret, klickar du på **Azure Active Directory**.
* Klicka på **Appregistreringar** och klicka på **Lägg till**.
* Följ anvisningarna och skapa ett nytt program. För den här kursen, spelar det ingen roll om det är en webbapp eller ett internt program, men om du behöver specifika exempel på webbprogram eller interna program kan du kolla vår [snabbstart](../articles/active-directory/develop/active-directory-developers-guide.md).
  * För webbappar, anger du **inloggnings-URL** som är bas-URL:en för din app, där användare loggar in exempelvis `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * För interna program, anger du en **omdirigerings-URI**, som Azure AD använder för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel `http://MyFirstAADApp`
* När du har slutfört registreringen, tilldelar Azure AD ditt program en unik klientidentifierare, program-ID:t. Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
