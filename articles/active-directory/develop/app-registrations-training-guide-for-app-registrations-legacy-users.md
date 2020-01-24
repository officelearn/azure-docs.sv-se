---
title: Ny utbildningsguide för Azure-portalappsregistreringar
description: Introducerar den nya Azure Portal appens registrerings upplevelse
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: b110929051098917d7c3f73161ca8694d4698070
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698228"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Ny utbildningsguide för Azure-portalappsregistreringar

Du kan hitta många förbättringar i den nya [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen i Azure Portal. Om du är bekant med Appregistreringar (äldre) i Azure Portal använder du den här utbildnings guiden för att komma igång med den nya upplevelsen.

I Azure Active Directory är den nya program registrerings upplevelsen som beskrivs här allmänt tillgänglig (GA). I Azure Active Directory B2C (Azure AD B2C) är den här upplevelsen i för hands versionen.

## <a name="key-changes"></a>Nyckel ändringar

- Appregistreringar är inte begränsade till antingen en webbapp */API* eller en *inbyggd* app. Du kan använda samma app-registrering för alla dessa appar genom att registrera respektive omdirigerings-URI: er.

- Appar som stöds av äldre versioner som bara loggar in med hjälp av organisations konton (Azure AD). Appar registrerades som en enda klient. Appar som endast har stöd för organisations konton från den katalog som appen registrerades i. Appar kan ändras så att de blir flera innehavare och har stöd för alla organisations konton. Med den nya upplevelsen kan du registrera appar som har stöd för både dessa alternativ och ett tredje alternativ: alla organisations konton och personliga Microsoft-konton.

- Den äldre upplevelsen var bara tillgänglig när du loggade in på Azure Portal med ett organisations konto. Med den nya upplevelsen kan du använda personliga Microsoft-konton som inte är associerade med en katalog.

## <a name="list-of-applications"></a>Lista över program

I listan med nya appar visas program som har registrerats via den äldre appens registrering i Azure Portal. De här apparna loggar in med Azure AD-konton. I listan ny app visas även registrerade appar på program registrerings portalen. De här apparna loggar in med Azure AD och personliga Microsoft-konton.

>[!NOTE]
>Program registrerings portalen är inaktuell.

Den nya applistan har ingen **program typ** kolumn eftersom det kan finnas flera typer av registreringar för en enda app. Listan har två ytterligare kolumner: **skapad på** och **certifikat & hemligheter**. **Certifikat & hemligheter** visar status för de autentiseringsuppgifter som har registrerats i appen. Status är **aktuell**, **förfaller snart**och **har upphört att gälla**.

## <a name="new-app-registration"></a>Ny appregistrering

I den tidigare versionen av kan du registrera en app som du krävde för att tillhandahålla: **namn**, **program typ**och **inloggnings-URL/omdirigerings-URI**. Appar som har skapats var endast Azure AD-program med en klient. De har endast stöd för organisations konton från katalogen som appen registrerades i.

I den nya upplevelsen måste du ange ett **namn** för appen och välja de **konto typer som stöds**. Du kan också ange en **omdirigerings-URI**. Om du anger en omdirigerings-URI måste du ange om den är webb/offentlig (mobil och stationär). Mer information finns i [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md). Mer Azure AD B2C finns [i registrera ett program i Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Skillnader mellan program registrerings portalen och Appregistreringar Sidan

### <a name="the-legacy-properties-page"></a>Sidan med gamla egenskaper

Den tidigare versionen hade en **egenskaps** sida. **Egenskaperna** innehåller följande fält:

- **Namn**
- **Objekt-ID**
- **Program-ID**
- **URI för app-id**
- **Logotyp**
- **URL för start sidan**
- **Utloggnings-URL**
- **URL för tjänst villkor**
- **URL för sekretesspolicy**
- **Programtyp**
- **Flera innehavare**

Den nya upplevelsen har inte den sidan. Här kan du hitta motsvarande funktioner:

- **Namn**, **logo typ**, **Start sidans URL**, **villkor för tjänste**-URL och **URL för sekretess policyn** finns nu på appens **varumärkes** sida.
- **Objekt-ID** och **program (klient) ID** finns på **översikts** sidan.
- De funktioner som styrs av växling vid **flera innehavare** i den tidigare versionen har ersatts av **konto typer som stöds** på sidan **autentisering** . Mer information finns i [snabb start: ändra de konton som stöds av ett program](quickstart-modify-supported-accounts.md).
- **URL för utloggning** finns nu på sidan **autentisering** .
- **Program typen** är inte längre ett giltigt fält. I stället måste du i stället använda URI: er, som du hittar på sidan **autentisering** , bestämma vilka typer av appar som stöds.
- **App-ID-URI** heter nu **program-ID URI** och du hittar den på att **exponera ett API**. I den tidigare versionen registrerades den här egenskapen med följande format: `https://{tenantdomain}/{appID}`, till exempel `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. I den nya upplevelsen skapas den automatiskt som `api://{appID}`, men den måste sparas explicit. I Azure AD B2C klienter används fortfarande formatet `https://{tenantdomain}/{appID}`.

### <a name="reply-urlsredirect-urls"></a>Svars webb adresser/omdirigerings-URL

I den tidigare versionen hade en app en sida med **svars-URL: er** . I den nya upplevelsen hittar du svars-URL: er på sidan **Authentication** för en app. De kallas nu **omdirigerings-URI: er**.

Formatet för omdirigerings-URI: er har ändrats. De måste vara kopplade till en app-typ, antingen webb eller offentlig. Av säkerhets skäl stöds inte jokertecken och `http://` scheman, förutom för *http://localhost* .

### <a name="keyscertificates--secrets"></a>Nycklar/certifikat & hemligheter

I den äldre upplevelsen hade appen **nycklar** på sidan. I den nya upplevelsen har den bytt namn till **certifikat & hemligheter**.

**Offentliga nycklar** kallas nu för **certifikat**. **Lösen ord** kallas nu **klient hemligheter**.

### <a name="required-permissionsapi-permissions"></a>Nödvändiga behörigheter/API-behörigheter

I den tidigare versionen hade en app en **nödvändig behörighets** sida. I den nya upplevelsen har den bytt namn till API- **behörigheter**.

När du har valt ett API i den äldre miljön kan du välja från en liten lista med Microsoft API: er. Du kan också söka igenom tjänstens huvud namn i klient organisationen. I den nya upplevelsen kan du välja mellan flera flikar: **Microsoft API**: er, **API: er som används i organisationen**eller **Mina API**: er. Sök fältet i **API: er i min organisation** använder TABB-sökningar via tjänstens huvud namn i klient organisationen.

> [!NOTE]
> Den här fliken visas inte om programmet inte är associerat med en klient. Mer information om hur du begär behörigheter finns i [snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md).

Den tidigare upplevelsen hade fått knappen **bevilja behörighet** överst på den **begärda behörighets** sidan. På den nya upplevelsen har sidan **bevilja godkännande** en knapp för att **bevilja administratörs medgivande** i avsnittet **API-behörigheter** för appen. Det finns också vissa skillnader i hur knappar fungerar.

I den tidigare miljön varierar logiken beroende på den inloggade användaren och de behörigheter som begärs. Logiken var:

- Om endast användare som har tillstånds behörighet begärs och den inloggade användaren inte var en administratör, kan användaren bevilja användar medgivande för de begärda behörigheterna.
- Om minst en behörighet som kräver administratörs medgivande begärdes och den inloggade användaren inte var administratör, fick användaren ett fel när han försökte bevilja medgivande.
- Om den inloggade användaren var administratör beviljas administratörs medgivande för alla begärda behörigheter.

I den nya upplevelsen kan endast en administratör bevilja medgivande. När en administratör väljer **bevilja administrativt medgivande**beviljas administratörs medgivande till alla begärda behörigheter.

## <a name="deleting-an-app-registration"></a>Ta bort en app-registrering

I den äldre miljön kunde du bara ta bort appar för en enda klient. Knappen Ta bort har inaktiverats för appar för flera klienter. I den nya upplevelsen kan du ta bort appar i vilket tillstånd som helst, men du måste bekräfta åtgärden. Mer information finns i [snabb start: ta bort ett program som är registrerat hos Microsoft Identity Platform](quickstart-remove-app.md).

## <a name="application-manifest"></a>Programmanifest

De äldre och nya upplevelserna använder olika versioner av JSON-formatet i manifest redigeraren. Mer information finns i [Azure Active Directory app manifest](reference-app-manifest.md).

## <a name="new-ui"></a>Nytt användar gränssnitt

Den nya upplevelsen lägger till UI-kontroller för följande egenskaper:

- Sidan **autentisering** har ett **implicit beviljat flöde** (`oauth2AllowImplicitFlow`). Till skillnad från den äldre upplevelsen kan du aktivera **åtkomsttoken** eller **ID-token**, eller båda.
- Sidan **exponera en API** innehåller **omfattningar som definieras av detta API** (`oauth2Permissions`) och **auktoriserade klient program** (`preAuthorizedApplications`). Mer information om hur du konfigurerar en app så att den är ett webb-API och visar behörigheter/omfattningar finns i [snabb start: Konfigurera ett program för att exponera webb-API: er](quickstart-configure-app-expose-web-apis.md).
- Sidan **anpassning** innehåller **utgivarens domän**. Utgivarens domän visas för användare i [programmets medgivande fråga](application-consent-experience.md). Mer information finns i [How to: Configure the Application ' Publisher Domain](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Begränsningar

Den nya upplevelsen har följande begränsningar:

- Formatet på klient hemligheter (applösenord) skiljer sig från den äldre upplevelsen och kan bryta CLI.
- Det finns inte stöd för att ändra värdet för konton som stöds i användar gränssnittet. Du måste använda app-manifestet om du inte växlar mellan Azure AD Single-Tenant och multi-Tenant.
