---
title: Få åtkomst till Azure Media Services-API med Azure Active Directory-autentisering | Microsoft Docs
description: Läs mer om begrepp och hur du kan använda Azure Active Directory (Azure AD) för att autentisera åtkomsten till Azure Media Services-API.
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
ms.openlocfilehash: 08b7f50c3051c174158cff0b4c591a2b22fb4ab4
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502710"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Åtkomst till Azure Media Services-API med Azure AD-autentisering
 
Azure Media Services-API är ett RESTful-API. Du kan använda den för att utföra åtgärder på media resurser med hjälp av ett REST-API eller genom att använda tillgängliga klient-SDK: er. Azure Media Services erbjuder en Media Services-klient SDK för Microsoft .NET. Om du vill ha behörighet att komma åt Media Services-resurser och Media Services-API, måste du först autentiseras. 

Media Services stöder [Azure Active Directory (Azure AD)-baserad autentisering](../../active-directory/fundamentals/active-directory-whatis.md). Azure Media REST-tjänsten kräver att användaren eller programmet som gör REST API-begäranden ha antingen den **deltagare** eller **ägare** roll som ska få åtkomst till resurserna. Mer information finns i [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../../role-based-access-control/overview.md).  

> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control service-autentiseringsmodellen. Access Control-auktorisering kommer dock att bli inaktuella den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

Det här dokumentet ger en översikt över hur du kommer åt Media Services-API med hjälp av REST- eller .NET API: er.

## <a name="access-control"></a>Åtkomstkontroll

För Azure Media REST-begäran ska lyckas måste den anropande användaren ha rollen deltagare eller ägare för Media Services-kontot som försöker komma åt.  
Endast användare med rollen ägare kan ge media resursåtkomst (konto) till nya användare eller program. Rollen som deltagare kan komma åt endast mediaresurs.
Ej begäran misslyckas med statuskod 401. Om du ser den här felkoden, kontrollera om användaren har rollen deltagare eller ägare som tilldelas för användarens Media Services-konto. Du kan kontrollera detta i Azure-portalen. Sök efter media-konto och klicka sedan på den **åtkomstkontroll** fliken. 

![Fliken för kontroll av åtkomst](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typer av autentisering 
 
När du använder Azure AD-autentisering med Azure Media Services har du två alternativ:

- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services-resurser. Interaktivt program bör först frågar användaren om användarens autentiseringsuppgifter. Ett exempel är en management-konsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning. 
- **Autentisering av tjänstens huvudnamn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, mellannivå tjänster eller schemalagda jobb. Exempel är webbappar, funktionsappar, logic apps, API och mikrotjänster.

### <a name="user-authentication"></a>Användarautentisering 

Program som ska använda autentiseringsmetoden som användaren är hantering och övervakning inbyggda appar: mobila appar och appar för Windows-konsolprogram. Den här typen av lösning är användbart när du vill mänsklig interaktion med tjänsten på något av följande scenarier:

- Övervakningsinstrumentpanel för kodningsjobben.
- Övervakning av instrumentpanelen för dina direktsända strömmar.
- Av hanteringsprogram för stationära och mobila användare att administrera resurser i ett Media Services-konto.

> [!NOTE]
> Den här autentiseringsmetoden bör inte användas för konsumentinriktade program. 

Ett internt program måste du först hämta en åtkomsttoken från Azure AD och sedan använda den när du gör HTTP-begäranden till Media Services REST-API. Lägga till åtkomsttoken i huvudet för begäran. 

I följande diagram visas en typisk interaktiv autentisering programflödet: 

![Inbyggda appar diagram](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

I det föregående diagrammet representerar talen flödet av begäranden i kronologisk ordning.

> [!NOTE]
> När du använder autentiseringsmetoden som användaren kan dela samma (standard) internt program klient-ID och programspecifik omdirigerings-URI i alla appar. 

1. Uppmana användaren att ange autentiseringsuppgifter.
2. Begär en åtkomsttoken för Azure AD med följande parametrar:  

    * Azure AD tenant-slutpunkten.

        Klient-informationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i övre högra hörnet.
    * Media Services resurs-URI. 

        Den här URI: N är detsamma för Media Services-konton som tillhör samma Azure-miljön (till exempel https://rest.media.azure.net).

    * Media Services (ursprunglig) program klient-ID.
    * Media Services (ursprunglig) program omdirigerings-URI.
    * Resurs-URI för REST Media Services.
        
        URI: N representerar REST API-slutpunkt (till exempel https://test03.restv2.westus.media.azure.net/api/).

    Värden för dessa parametrar finns [använder Azure portal för att få åtkomst till Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med alternativet för autentisering av användare.

3. Azure AD-åtkomsttoken skickas till klienten.
4. Klienten skickar en begäran till Azure Media REST-API med Azure AD-åtkomsttoken.
5. Klienten får tillbaka data från Media Services.

Information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med hjälp av klienten för Media Services .NET SDK finns i [Använd Azure AD-autentisering för att få åtkomst till Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder SDK för Media Services .NET-klient, måste du manuellt skapa en Azure AD begäran om åtkomsttoken med hjälp av parametrar som beskrivs i steg 2. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Program som ofta använder den här autentiseringsmetoden är appar som kör mellannivå tjänster och schemalagda jobb: web apps, funktionsappar, logic apps, API: er och mikrotjänster. Den här autentiseringsmetoden är lämplig för interaktiva program som du kanske vill använda ett tjänstkonto för att hantera resurser.

När du använder tjänsten huvudnamn autentiseringsmetoden för att skapa användarscenarier hanteras autentisering vanligtvis i mellannivå (via vissa API) och inte direkt i ett mobilt eller skrivbord program. 

Om du vill använda den här metoden, skapa en Azure AD-program och tjänstens huvudnamn i sin egen klientorganisation. När du har skapat programmet kan ge appen deltagare eller ägare rollåtkomst till Media Services-kontot. Du kan göra detta i Azure-portalen med hjälp av Azure CLI eller med ett PowerShell-skript. Du kan också använda ett befintligt Azure AD-program. Du kan registrera och hantera dina Azure AD-app och tjänstens huvudnamn [i Azure-portalen](media-services-portal-get-started-with-aad.md). Du också kan göra detta med hjälp av [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) eller [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Mellannivå appar](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

När du har skapat din Azure AD-program kan få du värden för följande inställningar. Du behöver dessa värden för autentisering:

- Klient-ID 
- Klienthemlighet 

I bilden ovan representerar talen flödet av begäranden i kronologisk ordning:
    
1. En mellannivå-app (webb-API eller ett webbprogram) begär en åtkomsttoken för Azure AD som har följande parametrar:  

    * Azure AD tenant-slutpunkten.

        Klient-informationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i övre högra hörnet.
    * Media Services resurs-URI. 

        Den här URI: N är detsamma för Media Services-konton som finns i samma Azure-miljön (till exempel https://rest.media.azure.net).

    * Resurs-URI för REST Media Services.

        URI: N representerar REST API-slutpunkt (till exempel https://test03.restv2.westus.media.azure.net/api/).

    * Azure AD application värden: klient-ID och klienthemlighet.
    
    Värden för dessa parametrar finns [använder Azure portal för att få åtkomst till Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med hjälp av alternativet service principal authentication.

2. Azure AD-åtkomsttoken skickas till mellannivån.
4. På mellannivå skickar begäran till Azure Media REST-API med Azure AD-token.
5. Mellannivån får tillbaka data från Media Services.

Mer information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med hjälp av klienten för Media Services .NET SDK finns i [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder SDK för Media Services .NET-klient, måste du manuellt skapa en begäran om Azure AD-token med hjälp av parametrar som beskrivs i steg 1. Mer information finns i [hur du använder Azure AD-Autentiseringsbiblioteket för att hämta Azure AD-token](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Felsökning

Undantag ”: fjärrservern returnerade ett fel: (401) Ej behörig”.

Lösning: För Media Services REST-begäran ska lyckas måste den anropande användaren vara en deltagare eller ägare roll i Media Services-kontot som försöker komma åt. Mer information finns i den [åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) avsnittet.

## <a name="resources"></a>Resurser

I följande artiklar finns en översikt över Azure AD authentication-koncepten: 

- [Autentiseringsscenarier som bemöts av Azure AD](../../active-directory/develop/authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Lägga till, uppdatera eller ta bort ett program i Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurera och hantera rollbaserad åtkomstkontroll med hjälp av PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Nästa steg

* Använda Azure portal för att [få åtkomst till Azure AD-autentisering för att använda Azure Media Services-API](media-services-portal-get-started-with-aad.md).
* Använd Azure AD-autentisering till [åtkomst till Azure Media Services-API med .NET](media-services-dotnet-get-started-with-aad.md).

