---
title: "Komma igång med Azure AD-autentisering med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du använder Azure-portalen för att få åtkomst till Azure Active Directory (AD Azure) autentisering om du vill använda Azure Media Services API."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 829e0759f8aeb8758f6b8895b88b60b66ffb22ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Komma igång med Azure AD-autentisering med hjälp av Azure portal

Lär dig hur du använder Azure-portalen för att få åtkomst till Azure Active Directory (AD Azure) autentiseras för åtkomst till Azure Media Services API.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Om du inte har ett konto, börja med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto med hjälp av Azure portal](media-services-portal-create-account.md).
- Kontrollera att du läser den [åtkomst till Azure Media Services API med Azure AD-autentiseringsöversikt](media-services-use-aad-auth-to-access-ams-api.md). 

När du använder Azure AD-autentisering med Azure Media Services, har du två alternativ för autentisering:

- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services-resurser. Interaktiva program bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en management konsolapp som används av behöriga användare för att övervaka kodning jobb eller direktsänd strömning. 
- **Huvudnamn autentiseringen av tjänsten**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som körs daemon tjänster, mellannivå tjänster eller schemalagda jobb: webbappar, funktionen appar, logikappar, API: er eller en mikrotjänster.

> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control service autentisering modellen. Access Control tillstånd att bli inaktuell på den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentisering så snart som möjligt.

## <a name="select-the-authentication-method"></a>Välj autentiseringsmetoden

1. I den [Azure-portalen](https://portal.azure.com/), Välj Media Services-kontot.
2. Välj hur du ansluter till Media Services API.

    ![Välj metoden anslutningssidan](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Användarautentisering

För att ansluta till Media Services-API med hjälp av alternativet för autentisering av användare, måste klientapp att begära en Azure AD-token som har följande parametrar:  

* Azure AD-klient slutpunkt
* Media Services resurs-URI
* Klient-ID för Media Services (intern)-program 
* Media Services (intern)-programmet omdirigerings-URI 
* Resurs-URI för REST Media Services

Du kan få värdena för dessa parametrar på den **Media Services-API med användarautentisering med** sidan. 

![Ansluta till sidan för autentisering av användare](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Om du ansluter till Media Services-API med hjälp av Microsoft för Media Services .NET SDK är krävs värdena tillgängliga som en del av SDK. Mer information finns i [använda Azure AD-autentisering för åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md).

Om du inte använder klienten Media Services .NET SDK, måste du manuellt skapa en begäran om Azure AD-token med de parametrar som nämnts tidigare. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Om du vill ansluta till Media Services-API med hjälp av alternativet service principal måste appen mellannivå (web API eller webbprogram) begära en Azure AD-token som har följande parametrar:  

* Azure AD-klient slutpunkt
* Media Services resurs-URI 
* Resurs-URI för REST Media Services
* Azure AD application värden: den **klient-ID** och **klienthemlighet**

Du kan få värdena för dessa parametrar på den **Anslut till Media Services-API med tjänstens huvudnamn** sidan. Använd den här sidan för att skapa en ny Azure AD-program eller välja en befintlig. När du har valt Azure AD-appar kan du hämta klient-ID (program-ID) och skapa värdena klienten hemligheten (nyckel). 

![Ansluta till service principal sida](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

När den **tjänstens huvudnamn** blad öppnas, väljs det första Azure AD-program som uppfyller följande kriterier:

- Det är en registrerad Azure AD-program.
- Det har behörighet som deltagare eller Owner Role-Based åtkomstkontroll för kontot.

När du skapar eller välj en Azure AD-app, kan du skapa och kopiera en klienthemlighet (nyckel) och klient-ID (program-ID). Klienthemligheten och klient-ID krävs för att hämta åtkomsten-token i det här scenariot.

Om du inte har behörighet att skapa Azure AD-appar i din domän, Azure AD app kontroller av bladet visas inte och visas ett varningsmeddelande.

Om du ansluter till Media Services-API med hjälp av Media Services .NET SDK, se [använda Azure AD-autentisering för åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md).

Om du inte använder klienten Media Services .NET SDK, måste du manuellt skapa en Azure AD-tokenbegäran med parametrarna som beskrivs ovan. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Hämta klient-ID och klienthemlighet

När du väljer en befintlig Azure AD-app eller Välj alternativet för att skapa en ny, visas följande knappar:

![Hantera behörigheter och hantera program](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Klicka för att öppna bladet Azure AD application **hantera program**. På den **hantera program** bladet kan du hämta appens klient-ID (program-ID). Om du vill generera en klienthemlighet (nyckel), Välj **nycklar**.

![Hantera program bladet nycklar alternativet](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Hantera behörigheter och programmet

När du har valt Azure AD-program kan du hantera program och behörigheter. Klicka på Konfigurera Azure AD-program för att få åtkomst till andra program, **hantera behörigheter**. För hanteringsuppgifter, till exempel ändra reply URL: er och nycklar eller redigera programmanifestet, klicka på **hantera program**.

### <a name="edit-the-apps-settings-or-manifest"></a>Redigera inställningar för appens eller manifest

Om du vill redigera appens inställningar eller manifestet **hantera program**.

![Hantera appen på sidan](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Nästa steg

Kom igång med [överföringen av filer till ditt konto](media-services-portal-upload-files.md).
