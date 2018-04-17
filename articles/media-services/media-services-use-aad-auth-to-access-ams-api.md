---
title: Åtkomst till Azure Media Services API med Azure Active Directory-autentisering | Microsoft Docs
description: Lär dig mer om koncept och steg för att använda Azure Active Directory (Azure AD) för att autentisera åtkomst till Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 57f2680d6b3f06a88a13a09018e7d72afcb710a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Åtkomst till Azure Media Services-API med Azure AD-autentisering
 
Azure Media Services API är en RESTful-API. Du kan använda den för att utföra åtgärder på media resurser med hjälp av REST-API eller genom att använda tillgängliga klient-SDK:. Azure Media Services erbjuder en klient för Media Services SDK för Microsoft .NET. Om du vill ha behörighet att komma åt Media Services-resurser och Media Services-API, måste du först autentiseras. 

Media Services stöder [Azure Active Directory (Azure AD)-baserad autentisering](../active-directory/active-directory-whatis.md). Azure Media REST-tjänsten kräver att användaren eller programmet som gör REST API-begäranden har antingen den **deltagare** eller **ägare** roll åtkomst till resurser. Mer information finns i [Kom igång med rollbaserad åtkomstkontroll i Azure portal](../role-based-access-control/overview.md).  

> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control service autentisering modellen. Access Control tillstånd att bli inaktuell på den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

Det här dokumentet ger en översikt över hur åtkomst till Media Services-API med hjälp av REST- eller .NET-API: er.

## <a name="access-control"></a>Åtkomstkontroll

Den anropande användaren måste ha en medarbetare eller ägare roll för Media Services-kontot som försöker komma åt för Azure Media REST-begäran ska lyckas.  
Bara en användare med rollen ägare kan ge åtkomst till företagsresurser (konto) media till nya användare eller appar. Deltagarrollen kan komma åt endast mediaresurs.
Obehöriga begäranden misslyckas med statuskoden 401. Om du ser felet, kontrollera om användaren har rollen Medarbetare eller ägare som tilldelas för Media Services användarkonto. Du kan kontrollera detta i Azure-portalen. Sök efter media-konto och klicka sedan på den **åtkomstkontroll** fliken. 

![Access control-fliken](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typer av autentisering 
 
När du använder Azure AD-autentisering med Azure Media Services, har du två alternativ för autentisering:

- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services-resurser. Interaktiva program bör först uppmana användaren att användarens autentiseringsuppgifter. Ett exempel är en management konsolapp som används av behöriga användare för att övervaka kodning jobb eller direktsänd strömning. 
- **Huvudnamn autentiseringen av tjänsten**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som körs daemon tjänster, mellannivå tjänster eller schemalagda jobb. Exempel är webbappar, funktionen appar, logikappar, API och mikrotjänster.

### <a name="user-authentication"></a>Användarautentisering 

Program som ska använda användarautentiseringsmetoden är övervakning av interna appar eller hantering: mobila appar, Windows-appar och konsolprogram. Den här typen av lösningen är användbart när du vill mänsklig interaktion med tjänsten på något av följande scenarier:

- Instrumentpanelen för övervakning för kodning jobb.
- Instrumentpanelen för övervakning för din direktsända dataströmmar.
- Hanteringsprogram för stationära och mobila användare att administrera resurser i ett Media Services-konto.

> [!NOTE]
> Den här autentiseringsmetoden bör inte användas för konsumentinriktade program. 

Det ursprungliga programmet måste du först skaffa en åtkomst-token från Azure AD och använda den när du gör HTTP-förfrågningar till Media Services REST API. Lägg till åtkomst-token i huvudet i begäran. 

Följande diagram visar en typisk interaktiva program autentiseringsflödet: 

![Ursprungliga appar diagram](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

I föregående diagram representerar siffrorna flödet av begäranden i kronologisk ordning.

> [!NOTE]
> När du använder användarautentiseringsmetoden dela alla appar samma (standard) programspecifika klient-ID och det ursprungliga programmet omdirigerings-URI. 

1. Uppmana användaren att ange autentiseringsuppgifter.
2. Begär en åtkomst-token för Azure AD med följande parametrar:  

    * Slutpunkten för Azure AD-klient.

        Klient-informationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i övre högra hörnet.
    * Media Services resurs-URI. 

        Den här URI: N är samma för Media Services-konton som är i samma Azure-miljön (till exempel https://rest.media.azure.net).

    * Media Services (ursprunglig) programmet klient-ID.
    * Media Services (intern)-programmet omdirigerings-URI.
    * Resurs-URI för REST Media Services.
        
        URI representerar REST API-slutpunkt (till exempel https://test03.restv2.westus.media.azure.net/api/).

    Värden för dessa parametrar finns [Använd Azure-portalen för att få åtkomst till Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med alternativet användare för autentisering.

3. Azure AD-åtkomsttoken skickas till klienten.
4. Klienten skickar en begäran till Azure Media REST-API med Azure AD-åtkomsttoken.
5. Klienten hämtar tillbaka data från Media Services.

Information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med klienten för Media Services .NET SDK finns [Använd Azure AD-autentisering för åtkomst till Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder klienten Media Services .NET SDK, måste du manuellt skapa en Tokenbegäran för Azure AD-åtkomst med hjälp av parametrar som beskrivs i steg 2. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Program som ofta använder den här autentiseringsmetoden är appar som körs på mellannivå tjänster och schemalagda jobb: web apps, funktionen appar, logikappar, API: er och mikrotjänster. Den här autentiseringsmetoden är lämplig för interaktiva program som du kanske vill använda ett tjänstkonto för att hantera resurser.

När du använder service principal autentiseringsmetoden för att skapa användarscenarier hanteras autentisering vanligtvis i mellannivå (via vissa API) och inte direkt i ett program för mobila eller stationära. 

Om du vill använda den här metoden skapa en Azure AD-program och tjänstens huvudnamn i sin egen klient. När du har skapat programmet ge appen medarbetare eller ägare rollåtkomst till Media Services-kontot. Du kan göra detta i Azure-portalen med hjälp av Azure CLI eller med ett PowerShell-skript. Du kan också använda ett befintligt Azure AD-program. Du kan registrera och hantera dina Azure AD-program och tjänstens huvudnamn [i Azure portal](media-services-portal-get-started-with-aad.md). Du också kan göra detta med hjälp av [Azure CLI 2.0](media-services-use-aad-auth-to-access-ams-api.md) eller [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Mellannivå appar](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

När du har skapat din Azure AD-program får du värden för följande inställningar. Du behöver dessa värden för autentisering:

- Klient-ID 
- Klienthemlighet 

I föregående bild representerar siffrorna flödet av begäranden i kronologisk ordning:
    
1. En mellannivå app (web API eller webbprogram) begär en Azure AD-åtkomsttoken som har följande parametrar:  

    * Slutpunkten för Azure AD-klient.

        Klient-informationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i övre högra hörnet.
    * Media Services resurs-URI. 

        Den här URI: N är samma för Media Services-konton som finns i samma Azure-miljön (till exempel https://rest.media.azure.net).

    * Resurs-URI för REST Media Services.

        URI representerar REST API-slutpunkt (till exempel https://test03.restv2.westus.media.azure.net/api/).

    * Azure AD application värden: klient-ID och klienthemlighet.
    
    Värden för dessa parametrar finns [Använd Azure-portalen för att få åtkomst till Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med hjälp av alternativet service principal autentisering.

2. Azure AD-åtkomsttoken skickas till mellannivån.
4. På mellannivå skickar begäran till Azure Media REST-API med Azure AD-token.
5. Mellannivån hämtar tillbaka data från Media Services.

Mer information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med klienten för Media Services .NET SDK finns [använda Azure AD-autentisering för att komma åt Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder klienten Media Services .NET SDK, måste du manuellt skapa en Azure AD-tokenbegäran med parametrar som beskrivs i steg 1. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Felsökning

Undantag ”: fjärrservern returnerade ett fel: (401) obehörig”.

Lösning: För Media Services REST-begäran ska lyckas anropa användaren måste vara en medarbetare eller ägare roll i Media Services-kontot som försöker komma åt. Mer information finns i [åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) avsnitt.

## <a name="resources"></a>Resurser

I följande artiklar finns översikter över begrepp för Azure AD-autentisering: 

- [Autentiseringsscenarier åtgärdas av Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Lägga till, uppdatera eller ta bort ett program i Azure AD](../active-directory/develop/active-directory-integrating-applications.md)
- [Konfigurera och hantera rollbaserad åtkomstkontroll med hjälp av PowerShell](../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Nästa steg

* Använda Azure portal och [åtkomst till Azure AD-autentisering för att använda Azure Media Services API](media-services-portal-get-started-with-aad.md).
* Använda Azure AD-autentisering till [åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md).

