---
title: Kom igång med Azure AD-autentisering med hjälp av Azure portal | Microsoft Docs
description: Lär dig hur du använder Azure-portalen för att få åtkomst till Azure Active Directory (Azure AD)-autentisering för att använda Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 37c5a97d5f7a872e916f223f2cdfc98bcdec3cad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130757"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Kom igång med Azure AD-autentisering med hjälp av Azure portal

Lär dig hur du använder Azure-portalen för att få åtkomst till Azure Active Directory (Azure AD)-autentisering för att komma åt Azure Media Services-API.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett Azure-konto. Om du inte har ett konto kan du börja med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto med hjälp av Azure portal](media-services-portal-create-account.md).
- Kontrollera att du läser igenom den [åtkomst till Azure Media Services-API med översikt över Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

När du använder Azure AD-autentisering med Azure Media Services har du två alternativ:

- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services-resurser. Interaktivt program bör först frågar användaren om autentiseringsuppgifter. Ett exempel är en management-konsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning. 
- **Autentisering av tjänstens huvudnamn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, mellannivå tjänster eller schemalagda jobb: webbappar, funktionsappar, logic apps, API: er eller en mikrotjänst.

> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control service-autentiseringsmodellen. Access Control-auktorisering kommer dock att bli inaktuella den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="select-the-authentication-method"></a>Välj autentiseringsmetod

1. I den [Azure-portalen](https://portal.azure.com/), Välj Media Services-kontot.
2. Välj hur du ansluter till Media Services-API.

    ![Välj metoden anslutningssidan](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Användarautentisering

Om du vill ansluta till Media Services-API med hjälp av alternativet för autentisering av användare, måste klientappen begära en Azure AD-token som har följande parametrar:  

* Azure AD-klientorganisationsslutpunkt
* Media Services resurs-URI
* Media Services (ursprunglig) programklients-ID 
* Media Services (ursprunglig) program omdirigerings-URI 
* Resurs-URI för REST Media Services

Du kan få värdena för dessa parametrar den **Media Services-API med användarautentisering** sidan. 

![Ansluta till sidan för autentisering av användare](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Om du ansluter till Media Services-API med hjälp av Microsoft för Media Services .NET SDK, är värdena som krävs tillgängliga för dig som en del av SDK: N. Mer information finns i [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md).

Om du inte använder SDK för Media Services .NET-klient, måste du manuellt skapa en begäran om Azure AD-token med hjälp av parametrar som beskrivs ovan. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

För att ansluta till Media Services-API med hjälp av alternativet tjänstens huvudnamn, måste appens mellannivå (webb-API eller ett webbprogram) begär en Azure AD-token som har följande parametrar:  

* Azure AD-klientorganisationsslutpunkt
* Media Services resurs-URI 
* Resurs-URI för REST Media Services
* Azure AD application värden: den **klient-ID** och **klienthemlighet**

Du kan få värdena för dessa parametrar den **Anslut till Media Services-API med tjänstens huvudnamn** sidan. Använd den här sidan för att skapa en ny Azure AD-program eller välja en befintlig. När du har valt Azure AD-app kan du hämta klient-ID (program-ID) och generera värden för klient-hemlighet (nyckel). 

![Anslut med tjänstens huvudnamn sidan](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

När den **tjänstens huvudnamn** bladet öppnas, väljs den första Azure AD-program som uppfyller följande kriterier:

- Det är en registrerad Azure AD-program.
- Den har deltagare eller Owner Role-Based Access Control-behörigheter för kontot.

När du skapar eller välj en Azure AD-app, kan du skapa och kopiera en klienthemlighet (nyckel) och klient-ID (program-ID). Klienthemligheten och klient-ID krävs för att hämta den åtkomsttoken i det här scenariot.

Om du inte har behörighet att skapa Azure AD-appar i din domän, Azure AD app-kontroller på bladet visas inte och visas ett varningsmeddelande.

Om du ansluter till Media Services-API med hjälp av Media Services .NET SDK, se [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md).

Om du inte använder SDK för Media Services .NET-klient, måste du manuellt skapa en Azure AD-tokenbegäran med parametrarna som beskrivs tidigare. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Hämta klient-ID och klienthemlighet

När du väljer en befintlig Azure AD-app eller Välj alternativet för att skapa en ny, visas följande knappar:

![Hantera behörigheter och hantera program](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Klicka för att öppna bladet Azure AD application **Hanteringsappen**. På den **Hanteringsappen** bladet som du kan hämta appens klient-ID (program-ID). Om du vill generera en klienthemlighet (nyckel), Välj **nycklar**.

![Hantera program bladet nycklar alternativet](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Hantera behörigheter och programmet

När du har valt Azure AD-program kan hantera du program och behörigheter. Om du vill konfigurera Azure AD-program för att få åtkomst till andra program, klickar du på **hantera behörigheter**. För hanteringsuppgifter, till exempel ändra nycklar och Svarswebbadresser eller redigera programmets manifest, klicka på **Hanteringsappen**.

### <a name="edit-the-apps-settings-or-manifest"></a>Redigera appens inställningar eller manifest

Om du vill redigera appens inställningar eller manifest, klickar du på **Hanteringsappen**.

![Hantera programsidan](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Nästa steg

Kom igång med [ladda upp filer till ditt konto](media-services-portal-upload-files.md).
