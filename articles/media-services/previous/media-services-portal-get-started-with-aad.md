---
title: Kom igång med Azure AD-autentisering med hjälp av Azure Portal | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att komma åt Azure Active Directory (Azure AD)-autentisering för att använda Azure Media Services API.
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
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162777"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Kom igång med Azure AD-autentisering med hjälp av Azure Portal

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Lär dig hur du använder Azure Portal för att komma åt Azure Active Directory-autentisering (Azure AD) för att få åtkomst till Azure Media Services API.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett konto börjar du med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).
- Se till att granska [åtkomst Azure Media Services-API: t med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

När du använder Azure AD-autentisering med Azure Media Services har du två autentiserings alternativ:

- **Användarautentisering.** Autentisera en person som använder appen för att interagera med Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en hanterings konsol app som används av behöriga användare för att övervaka kodnings jobb eller direktsänd strömning. 
- **Autentisering av tjänstens huvud namn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb: webbappar, funktions program, Logic Apps, API: er eller en mikrotjänst.

> [!IMPORTANT]
> För närvarande har Media Services stöd för Azure Access Control-tjänstens autentiserings modell. Access Control auktorisering kommer dock att föråldras den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="select-the-authentication-method"></a>Välj autentiseringsmetod

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt Media Services-konto.
2. Välj hur du vill ansluta till Media Services API.

    ![Sidan Välj anslutnings metod](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Användarautentisering

För att ansluta till Media Services-API med hjälp av alternativet för användarautentisering måste klient programmet begära en Azure AD-token som har följande parametrar:  

* Azure AD-klientens slut punkt
* Media Services resurs-URI
* Media Services (inbyggt) program klient-ID 
* Omdirigerings-URI för Media Services (inbyggt) 
* Resurs-URI för REST-Media Services

Du kan hämta värdena för dessa parametrar på sidan **Media Services-API med** användarautentisering. 

![Sidan Anslut med användarautentisering](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Om du ansluter till Media Services-API med hjälp av Media Services Microsoft .NET SDK är de nödvändiga värdena tillgängliga som en del av SDK: n. Mer information finns i [använda Azure AD-autentisering för att få åtkomst till Azure Media Services API med .net](media-services-dotnet-get-started-with-aad.md).

Om du inte använder Media Services .NET-klient-SDK måste du skapa en Azure AD-Tokenbegäran manuellt med hjälp av de parametrar som beskrivs ovan. Mer information finns i [så här använder du Azure AD Authentication Library för att hämta Azure AD-token](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

För att ansluta till Media Services-API med hjälp av tjänstens huvud namn måste du begära en Azure AD-token med följande parametrar med hjälp av tjänstens huvud namn (webb-API eller webb program):  

* Azure AD-klientens slut punkt
* Media Services resurs-URI 
* Resurs-URI för REST-Media Services
* Azure AD-program värden: **klient-ID** och **klient hemlighet**

Du kan hämta värdena för dessa parametrar på sidan **Anslut till Media Services API med tjänstens huvud namn** . Använd den här sidan om du vill skapa ett nytt Azure AD-program eller välja en befintlig. När du har valt Azure AD-appen kan du hämta klient-ID: t (program-ID) och generera värdena för klient hemligheten (nyckel). 

![Anslut med sidan för tjänstens huvud namn](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

När bladet för **tjänstens huvud** namn öppnas väljs det första Azure AD-program som uppfyller följande kriterier:

- Det är ett registrerat Azure AD-program.
- Den har deltagar-eller ägar rollbaserade Access Control behörigheter för kontot.

När du har skapat eller valt en Azure AD-App kan du skapa och kopiera en klient hemlighet (nyckel) och klient-ID: t (program-ID). Klient hemlighet och klient-ID krävs för att hämta åtkomsttoken i det här scenariot.

Om du inte har behörighet att skapa Azure AD-appar i din domän visas inte Azure AD App-kontrollerna för bladet och ett varnings meddelande visas.

Om du ansluter till Media Services-API med hjälp av Media Services .NET SDK kan du läsa mer i [använda Azure AD-autentisering för att få åtkomst till Azure Media Services API med .net](media-services-dotnet-get-started-with-aad.md).

Om du inte använder Media Services .NET-klient-SDK måste du skapa en Azure AD-Tokenbegäran manuellt med hjälp av de parametrar som beskrivs ovan. Mer information finns i [så här använder du Azure AD Authentication Library för att hämta Azure AD-token](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Hämta klient-ID och klient hemlighet

När du har valt en befintlig Azure AD-App eller valt alternativet för att skapa en ny, visas följande knappar:

![Knappen Hantera behörigheter och knappen hantera program](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Öppna bladet Azure AD-program genom att klicka på **hantera program**. På bladet **hantera program** kan du hämta appens klient-ID (program-ID). Om du vill generera en klient hemlighet (nyckel) väljer du **nycklar**.

![Alternativet hantera program blads nycklar](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Hantera behörigheter och programmet

När du har valt Azure AD-programmet kan du hantera program och behörigheter. Om du vill konfigurera Azure AD-programmet för att få åtkomst till andra program klickar du på **Hantera behörigheter**. För hanterings uppgifter som att ändra nycklar och svars-URL: er, eller redigera programmets manifest, klickar du på **hantera program**.

### <a name="edit-the-apps-settings-or-manifest"></a>Redigera appens inställningar eller manifest

Redigera appens inställningar eller manifest genom att klicka på **hantera program**.

![Sidan hantera program](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-portal-upload-files.md).
