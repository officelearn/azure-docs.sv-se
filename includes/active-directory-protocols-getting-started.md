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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523904"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Registrera först ditt program med din Azure Active Directory (Azure AD)-klient. Det ger dig en program-ID för ditt program och låter det ta emot tokens.

1. Logga in på [Azure-portalen](https://portal.azure.com).
   
1. Välj din Azure AD-klient genom att välja ditt konto i det övre högra hörnet på sidan, följt av att välja **växel katalog** navigering och sedan välja lämplig klient. 
   - Hoppa över det här steget om du bara har en Azure AD-klient under ditt konto, eller om du redan har valt rätt Azure AD-klient.
   
1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
   
1. På den **Azure Active Directory** vänstra menyn väljer du **registrerade appar**och väljer sedan **ny registrering**.
   
1. Följ anvisningarna och skapa ett nytt program. Det spelar ingen roll om det är ett webb program eller ett offentligt klient program (mobilt & Desktop) för den här självstudien, men om du vill ha specifika exempel för webb program eller offentliga klient program kan du läsa våra [snabb starter](../articles/active-directory/develop/v1-overview.md).
   
   - **Namn** är appens namn och beskriver appen för användarna.
   - Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
   - Ange **omdirigerings-URI**. För webb program är detta den grundläggande URL: en för din app där användarna kan logga in.  Till exempel `http://localhost:12345`. För en offentlig klient (mobil & Desktop) använder Azure AD den för att returnera svar från token. Ange ett värde som är specifik för ditt program.  Till exempel `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. När du har slutfört registreringen tilldelar Azure AD programmet ett unikt klient-ID ( **program-ID**). Du behöver det här värdet i nästa avsnitt, så kopiera det från program sidan.
   
1. Om du vill hitta ditt program i Azure Portal väljer du **Appregistreringar**och väljer sedan **Visa alla program**.
