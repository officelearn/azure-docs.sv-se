---
title: Appregistreringar i guiden Azure Portal-utbildning – Azure
description: En introduktion till den nya program registrerings upplevelsen i Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a539859fb4853467863f3fd2ab4144bcb789b9f5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662505"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Tränings guide: Appregistreringar i Azure Portal

Du kan hitta flera förbättringar i den nya [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen i Azure Portal. Om du är bekant med den äldre upplevelsen kan du använda den här tränings guiden för att komma igång med den nya upplevelsen.

I Azure Active Directory är den nya program registrerings upplevelsen som beskrivs här allmänt tillgänglig (GA). I Azure Active Directory B2C (Azure AD B2C) är den här upplevelsen i för hands versionen.

## <a name="key-changes"></a>Nyckel ändringar

- Appregistreringar är inte begränsade till antingen en webbapp **/API** eller en **inbyggd** app. Du kan använda samma app-registrering för alla dessa genom att registrera respektive omdirigerings-URI: er.
- Appar som stöds av äldre versioner som bara loggar in organisations konton (Azure AD). Appar registrerades som en enskild klient (stöd endast organisations konton från katalogen som appen registrerades i) och kan ändras till att vara flera innehavare (stöd för alla organisations konton). Med den nya upplevelsen kan du registrera appar som har stöd för både dessa alternativ och ett tredje alternativ: alla organisations konton och personliga Microsoft-konton.
- Den äldre upplevelsen var bara tillgänglig när du loggade in på Azure Portal med ett organisations konto. Med den nya upplevelsen kan du använda personliga Microsoft-konton som inte är associerade med en katalog.

## <a name="list-of-applications"></a>Lista över program

- I listan med nya appar visas program som har registrerats via den äldre appens registrering i Azure Portal (appar som loggar in på Azure AD-konton) samt appar som registrerats, även om [program registrerings portalen](https://apps.dev.microsoft.com/) (appar som loggar in Azure AD och personliga Microsoft-konton).
- Den nya applistan har ingen **program typ** kolumn (eftersom en registrering av en enda app kan vara flera typer) och har två ytterligare kolumner: en **skapad i** kolumn och en **certifikat & hemligheter** som visar status (aktuell, förfaller snart eller har gått ut) autentiseringsuppgifter som har registrerats i appen.

## <a name="new-app-registration"></a>Ny app-registrering

I den tidigare versionen av kan du registrera en app som du krävde för att tillhandahålla: **namn**, **program typ**och **inloggnings-URL/omdirigerings-URI**. De appar som har skapats var endast Azure AD-program med en enda klient, vilket innebär att de bara har stöd för organisations konton från katalogen som appen registrerades i.

I den nya upplevelsen måste du ange ett **namn** för appen och välja de **konto typer som stöds**. Du kan också ange en **omdirigerings-URI**. Om du anger en omdirigerings-URI måste du ange om den är webb/offentlig (mobil och stationär). Mer information om hur du registrerar en app med hjälp av den nya program registreringen finns i [Registrera en app med Microsoft Identity Platform](quickstart-register-app.md). Mer Azure AD B2C finns [i registrera ett program i Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="the-legacy-properties-page"></a>Sidan med gamla egenskaper

Den tidigare upplevelsen hade en **egenskaps** sida som den nya upplevelsen inte har. **Egenskaps** bladet hade följande fält: **namn**, **objekt-ID**, **program-ID**, **app-ID URI**, **logo typ**, **Start sidans URL**, **utloggnings-URL**, **villkor för tjänsten URL**, **sekretess policy URL**, **program typ**och **flera innehavare.**

Här kan du hitta motsvarande funktioner i den nya upplevelsen:

- **Namn**, **logo typ**, **Start sidans URL**, **villkor för tjänste**-URL och **URL för sekretess policyn** finns nu på appens **varumärkes** sida.
- **Objekt-ID** och **program (klient) ID** finns på **översikts** sidan.
- De funktioner som styrs av växling vid **flera innehavare** i den tidigare versionen har ersatts av **konto typer som stöds** på sidan **autentisering** . Mer information om hur flera innehavare mappar till alternativen för konto typ som stöds finns i [den här snabb](quickstart-modify-supported-accounts.md)starten.
- **URL för utloggning** finns nu på sidan **autentisering** .
- **Program typen** är inte längre ett giltigt fält. I stället ska du använda omdirigerings-URI: er (som du hittar på sidan **autentisering** ) och bestämma vilka typer av appar som stöds.
- **App-ID-URI** heter nu **program-ID URI** och du hittar det på sidan **exponera ett API** . I den tidigare versionen registrerades den här egenskapen automatiskt med följande format: `https://{tenantdomain}/{appID}` (till exempel `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). I den nya upplevelsen genereras den automatiskt som `api://{appID}`, men den måste sparas explicit. I Azure AD B2C klienter används fortfarande formatet `https://{tenantdomain}/{appID}`.

## <a name="reply-urlsredirect-urls"></a>Svars webb adresser/omdirigerings-URL

I den tidigare versionen hade en app en sida med **svars-URL: er** . I den nya upplevelsen hittar du svars-URL: er i avsnittet **Authentication** i appen. Dessutom kallas de **omdirigerings-URI: er**. Dessutom har formatet för omdirigerings-URI: er ändrats. De måste vara kopplade till en app-typ (webb eller offentlig). Av säkerhets skäl stöds inte jokertecken och http://-scheman (med undantag för http://localhost).

## <a name="keyscertificates--secrets"></a>Nycklar/certifikat & hemligheter

I den äldre upplevelsen hade appen **nycklar** på sidan. I den nya upplevelsen har den bytt namn till **certifikat & hemligheter**. Dessutom kallas **offentliga nycklar** för **certifikat** och **lösen ord** som **klient hemligheter**.

## <a name="required-permissionsapi-permissions"></a>Nödvändiga behörigheter/API-behörigheter

- I den tidigare versionen hade en app en **nödvändig behörighets** sida. I den nya upplevelsen har den bytt namn till API- **behörigheter**.
- När du väljer ett API i den äldre miljön kan du välja från en liten lista med Microsoft API: er eller söka igenom tjänstens huvud namn i klient organisationen. I den nya upplevelsen kan du välja mellan flera flikar: **Microsoft API**: er, **API: er som används i organisationen**eller **Mina API**: er. Sök fältet i **API: er i min organisation** använder TABB-sökningar via tjänstens huvud namn i klient organisationen.

   > [!NOTE]
   > Den här fliken visas inte om programmet inte är associerat med en klient. Mer information om hur du begär behörigheter med hjälp av den nya upplevelsen finns i [den här snabb](quickstart-configure-app-access-web-apis.md)starten.

- Den tidigare upplevelsen hade fått knappen **bevilja behörighet** överst på den **begärda behörighets** sidan. I den nya upplevelsen finns avsnittet **beviljande medgivande** med knappen **bevilja administratörs medgivande** i avsnittet **API-behörigheter** för appen. Dessutom finns det vissa skillnader i hur knapparna fungerar:
   - I den tidigare miljön varierar logiken beroende på den inloggade användaren och de behörigheter som begärs. Logiken var:
      - Om endast användare som har tillstånds behörighet begärs och den inloggade användaren inte var administratör, kunde användaren bevilja användar medgivande för de begärda behörigheterna.
      - Om minst en behörighet som kräver administratörs medgivande begärs och den inloggade användaren inte var administratör, fick användaren ett fel vid försök att bevilja medgivande.
      - Om den inloggade användaren var administratör beviljas administratörs medgivande för alla begärda behörigheter.
   - I den nya upplevelsen kan endast en administratör bevilja medgivande. När en administratör väljer knappen **bevilja administratörs medgivande** beviljas administratörs medgivande till alla begärda behörigheter.

## <a name="deleting-an-app-registration"></a>Ta bort en app-registrering

I den tidigare versionen måste en app vara en-klient för att kunna tas bort. Knappen Ta bort har inaktiverats för appar för flera klienter. I den nya upplevelsen kan appar tas bort i vilket tillstånd som helst, men du måste bekräfta åtgärden. Mer information om hur du tar bort registrerings program finns i [den här snabb](quickstart-remove-app.md)starten.

## <a name="application-manifest"></a>Programmanifest

De äldre och nya upplevelserna använder olika versioner av JSON-formatet i manifest redigeraren. Mer information finns i [program manifestet](reference-app-manifest.md).

## <a name="new-ui"></a>Nytt användar gränssnitt

Det finns ett nytt gränssnitt för egenskaper som tidigare endast kunde anges med hjälp av manifest redigeraren eller API: t eller som inte finns.

- Du hittar oauth2AllowImplicitFlow ( **implicita tilldelnings flöde** ) på sidan **autentisering** . Till skillnad från den äldre upplevelsen kan du aktivera **åtkomsttoken** eller **ID-token**, eller båda.
- **Omfattningar som definieras av denna API** (oauth2Permissions) och **auktoriserade klient program** (preAuthorizedApplications) kan konfigureras via sidan **exponera en API** . Mer information om hur du konfigurerar en app så att den är ett webb-API och visar behörigheter/omfattningar finns i [den här snabb](quickstart-configure-app-expose-web-apis.md)starten.
- **Publisher-domän** (som visas för användare i [programmets medgivande fråga](application-consent-experience.md)) finns på **bladet varumärkes blad** . Mer information om hur du konfigurerar en Publisher-domän finns i [den här instruktionen](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Begränsningar

Den nya upplevelsen har följande begränsningar:

- Formatet på klient hemligheter (applösenord) skiljer sig från den äldre upplevelsen och kan bryta CLI.
- Det finns inte stöd för att ändra värdet för konton som stöds i användar gränssnittet. Du måste använda app-manifestet om du inte växlar mellan Azure AD Single-Tenant och multi-Tenant.
