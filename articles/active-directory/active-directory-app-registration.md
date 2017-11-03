---
title: Azure Active Directory-Appregistrering | Microsoft Docs
description: "Den här artikeln beskriver hur du använder Azure-portalen för att registrera ett program med Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 2f2817688beb2028fd0bba8522827d87a0097f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Registrera ditt program med Azure Active Directory-klient

Du kan använda Azure-portalen för att registrera ditt program med Azure Active Directory (Azure AD)-klient. Detta skapar ett program-ID för programmet och gör det möjligt att ta emot token.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj Azure AD-klienten genom att välja kontot i det övre högra hörnet på sidan.
3. I det vänstra navigeringsfönstret väljer **fler tjänster**, klickar du på **App registreringar**, och klicka på **Lägg till**.
4. Följ anvisningarna och skapa ett nytt program. Om du vill att specifika exempel för webbprogram och interna program, Kolla in våra [Snabbstart](active-directory-developers-guide.md).
  * Webbprogram, ange den **inloggnings-URL**, vilket är den grundläggande Webbadressen för din app, där användarna kan logga in t.ex `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Interna program, ange en **omdirigerings-URI**, som används i Azure AD för att returnera token svar. Ange ett värde som är specifikt för ditt program, till exempel `http://MyFirstAADApp`
5. När du har slutfört registreringen, tilldelar Azure AD ditt program ett unikt klient-ID, program-ID.

## <a name="update-application-settings-from-the-azure-portal"></a>Uppdatera inställningar för program i Azure Portal

Du kan enkelt ändra ett befintligt program inställningar med hjälp av Azure-portalen. Du kanske vill konfigurera en reply-URL som är där Azure AD utfärdar token svar. Du kan också konfigurera behörigheter för andra program, till exempel så att programmet åtkomst till Microsoft Graph API. Du kan göra allt detta via inställningssidan för programmet.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj Azure AD-klienten genom att välja kontot i det övre högra hörnet på sidan.
3. I det vänstra navigeringsfönstret väljer **fler tjänster**, klickar du på **App registreringar**, och välj ditt program i listan.
4. Klicka på **inställningar** Öppna inställningssidan för programmet.
  * Den **egenskaper** sida kan du ändra den allmänna informationen för programmet. Detta inkluderar programmets namn, URL för inloggning och logga ut URL-Adressen.
  * Den **Reply URL: er** sidan kan du lägga till en reply-URL som är där Azure AD skickar token svar.
  * Den **ägare** sidan kan du lägga till ägare.
  * Den **behörigheter** sidan kan du konfigurera behörigheter för appen. Klicka till exempel för att komma åt Microsoft Graph API **Lägg till** och välj **Microsoft Graph** i API-selector Välj behörigheten som krävs, till exempel **läsa katalogdata**.
  * Den **nycklar** sidan kan du lägga till programmet hemligheter. Hemligheten som visas endast när omedelbart efter att skapa, så se till att kopiera den för ytterligare användning.

## <a name="use-the-inline-manifest-editor"></a>Använda redigeraren infogade manifest

Du kan använda redigeraren infogade manifest för att ändra vissa egenskaper som inte exponeras direkt i Azure-portalen. Du kan till exempel använda det att ändra programmets App-ID URI eller aktivera OAuth2.0 implicita flödet i stället för de standard grant-auktoriseringskodflödet.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj Azure AD-klienten genom att välja kontot i det övre högra hörnet på sidan.
3. I det vänstra navigeringsfönstret väljer **fler tjänster**, klickar du på **App registreringar**, och välj ditt program i listan.
4. Klicka på **Manifest** från sidan programmet att öppna redigeraren infogade manifestet.
5. Direkt kan du göra ändringar i manifestet och spara den när du är klar. Alternativt kan du hämta manifestet för att öppna den i din favorit redigeraren och ladda upp det uppdaterade manifestet.

## <a name="next-steps"></a>Nästa steg

1. Kolla in den [Snabbstart](active-directory-developers-guide.md) för detaljerad genomgång av program som utför autentisering med hjälp av Azure AD.
2. Kolla in våra fullständig lista över kodexempel i [GitHub](https://github.com/azure-samples).
