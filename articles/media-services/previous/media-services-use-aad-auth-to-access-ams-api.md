---
title: Komma åt Azure Media Services API med Azure Active Directory-autentisering | Microsoft-dokument
description: Lär dig mer om begrepp och åtgärder som ska vidtas för att använda Azure Active Directory (Azure AD) för att autentisera åtkomst till Azure Media Services API.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8b38b38789edfd5a0a30fdd589849bfa345eaac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157864"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Ansluta till API:et för Azure Media Services med Azure AD-autentisering  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services API är ett RESTful API. Du kan använda den för att utföra åtgärder på medieresurser med hjälp av ett REST API eller med hjälp av tillgängliga klient-SDK:er. Azure Media Services erbjuder en Media Services-klient SDK för Microsoft .NET. Om du vill ha åtkomst till Media Services-resurser och API:et för Medietjänster måste du först autentiseras. 

Media Services stöder [Azure Active Directory (Azure AD)-baserad autentisering](../../active-directory/fundamentals/active-directory-whatis.md). Azure Media REST-tjänsten kräver att användaren eller programmet som gör REST API-begäranden har antingen **rollen Deltagare** eller **Ägare** för att komma åt resurserna. Mer information finns [i Komma igång med rollbaserad åtkomstkontroll i Azure-portalen](../../role-based-access-control/overview.md).  

Det här dokumentet ger en översikt över hur du kommer åt API:et för Medietjänster med hjälp av REST- eller .NET API:er.

> [!NOTE]
> Access Control-auktoriseringen inaktuella den 1 juni 2018.

## <a name="access-control"></a>Åtkomstkontroll

För att Azure Media REST-begäran ska lyckas måste den anropande användaren ha en deltagar- eller ägarroll för det Media Services-konto som den försöker komma åt.  
Endast en användare med ägarrollen kan ge medieresurs (konto) åtkomst till nya användare eller appar. Rollen Deltagare kan bara komma åt medieresursen.
Obehöriga begäranden misslyckas, med statuskoden 401. Om du ser den här felkoden kontrollerar du om användaren har rollen Deltagare eller Ägare tilldelad för användarens Media Services-konto. Du kan kontrollera detta i Azure-portalen. Sök efter ditt mediekonto och klicka sedan på fliken **Åtkomstkontroll.** 

![Fliken Åtkomstkontroll](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typer av autentisering 
 
När du använder Azure AD-autentisering med Azure Media Services har du två autentiseringsalternativ:

- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services-resurser. Det interaktiva programmet bör först fråga användaren om användarens autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller livestreaming. 
- **Autentisering av tjänstens huvudnamn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemontjänster, mellannivåtjänster eller schemalagda jobb. Exempel är webbappar, funktionsappar, logikappar, API och mikrotjänster.

### <a name="user-authentication"></a>Användarautentisering 

Program som ska använda användarens autentiseringsmetod är hantering eller övervakning av inbyggda appar: mobilappar, Windows-appar och konsolprogram. Den här typen av lösning är användbar när du vill ha mänsklig interaktion med tjänsten i något av följande scenarion:

- Övervakningsinstrumentpanel för dina kodningsjobb.
- Övervakningsinstrumentpanel för dina livestreamar.
- Hanteringsprogram för stationära eller mobila användare för att administrera resurser i ett Media Services-konto.

> [!NOTE]
> Den här autentiseringsmetoden bör inte användas för konsumentinriktade program. 

Ett inbyggt program måste först hämta en åtkomsttoken från Azure AD och sedan använda den när du gör HTTP-begäranden till REST-API:et för Media Services. Lägg till åtkomsttoken i begäranhuvudet. 

I följande diagram visas ett typiskt interaktivt programautentiseringsflöde: 

![Diagram över inbyggda appar](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

I föregående diagram representerar talen flödet av begäranden i kronologisk ordning.

> [!NOTE]
> När du använder metoden för användarautentisering delar alla appar samma (standard) inbyggda programklient-ID och URI för inbyggt program. 

1. Fråga en användare om autentiseringsuppgifter.
2. Begär en Azure AD-åtkomsttoken med följande parametrar:  

   * Slutpunkten för Azure AD-klient.

       Klientinformationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i det övre högra hörnet.
   * Resurs-URI för Media Services. 

       Den här URI:n är densamma för Media Services-konton https://rest.media.azure.net)som finns i samma Azure-miljö (till exempel .

   * Media Services (inbyggt) programklient-ID.
   * Media Services (native) program omdirigera URI.
   * Resurs-URI för REST Media Services.
        
       URI:et representerar REST API-slutpunkten (till exempel https://test03.restv2.westus.media.azure.net/api/).

     Information om hur du hämtar värden för dessa parametrar finns i [Använda Azure-portalen för att komma åt Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med hjälp av alternativet användarautentisering.

3. Azure AD-åtkomsttoken skickas till klienten.
4. Klienten skickar en begäran till AZURE Media REST API med Azure AD-åtkomsttoken.
5. Klienten får tillbaka data från Media Services.

Information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med hjälp av Media Services .NET-klienten SDK finns i [Använda Azure AD-autentisering för att komma åt Api:et för medietjänster med .NET](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder Media Services .NET-klienten SDK måste du manuellt skapa en Azure AD-åtkomsttokenbegäran med hjälp av de parametrar som beskrivs i steg 2. Mer information finns i [Så här använder du Azure AD-autentiseringsbiblioteket för att hämta Azure AD-token](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Program som ofta använder den här autentiseringsmetoden är appar som kör tjänster på mellannivå och schemalagda jobb: webbappar, funktionsappar, logikappar, API:er och mikrotjänster. Den här autentiseringsmetoden är också lämplig för interaktiva program där du kanske vill använda ett tjänstkonto för att hantera resurser.

När du använder tjänstens huvudautentiseringsmetod för att skapa konsumentscenarier hanteras autentisering vanligtvis på mellannivå (via vissa API) och inte direkt i ett mobil- eller skrivbordsprogram. 

Om du vill använda den här metoden skapar du ett Azure AD-program och tjänsthuvudnamn i den egna klienten. När du har skapat programmet ger du appen Contributor eller Owner roll åtkomst till Media Services-kontot. Du kan göra detta i Azure-portalen, med hjälp av Azure CLI eller med ett PowerShell-skript. Du kan också använda ett befintligt Azure AD-program. Du kan registrera och hantera ditt Azure AD-app- och tjänsthuvudnamn [i Azure-portalen](media-services-portal-get-started-with-aad.md). Du kan också göra detta genom att använda [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) eller [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Appar på mellannivå](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

När du har skapat ditt Azure AD-program får du värden för följande inställningar. Du behöver dessa värden för autentisering:

- Klientorganisations-ID 
- Klienthemlighet 

I föregående figur representerar talen flödet av begäranden i kronologisk ordning:
    
1. En app på mellannivå (webb-API eller webbprogram) begär en Azure AD-åtkomsttoken som har följande parametrar:  

   * Slutpunkten för Azure AD-klient.

       Klientinformationen kan hämtas från Azure-portalen. Placera markören över namnet på den inloggade användaren i det övre högra hörnet.
   * Resurs-URI för Media Services. 

       Den här URI:n är densamma för Media Services-konton som https://rest.media.azure.net)finns i samma Azure-miljö (till exempel .

   * Resurs-URI för REST Media Services.

       URI:et representerar REST API-slutpunkten (till exempel https://test03.restv2.westus.media.azure.net/api/).

   * Azure AD-programvärden: klient-ID och klienthemlighet.
    
     Information om hur du hämtar värden för dessa parametrar finns i [Använda Azure-portalen för att komma åt Azure AD-autentiseringsinställningar med](media-services-portal-get-started-with-aad.md) hjälp av alternativet autentisering av tjänstens huvudnamn.

2. Azure AD-åtkomsttoken skickas till mellannivån.
4. Den mellandelade nivån skickar begäran till Azure Media REST API med Azure AD-token.
5. Den mellersta nivån hämtar data från Media Services.

Mer information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med hjälp av Media Services .NET-klienten SDK finns i [Använda Azure AD-autentisering för att komma åt Azure Media Services API med .NET](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder Media Services .NET-klienten SDK måste du manuellt skapa en Azure AD-tokenbegäran med hjälp av parametrar som beskrivs i steg 1. Mer information finns i [Så här använder du Azure AD-autentiseringsbiblioteket för att hämta Azure AD-token](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Felsökning

Undantag: "Fjärrservern returnerade ett fel: (401) Obehörig."

Lösning: För att REST-begäran om mediatjänster ska lyckas måste den uppringande användaren vara en deltagar- eller ägarroll i det Media Services-konto som den försöker komma åt. Mer information finns i avsnittet [Åtkomstkontroll.](media-services-use-aad-auth-to-access-ams-api.md#access-control)

## <a name="resources"></a>Resurser

Följande artiklar är översikter över Azure AD-autentiseringsbegrepp: 

- [Autentiseringsscenarier som hanteras av Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Lägga till, uppdatera eller ta bort ett program i Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurera och hantera rollbaserad åtkomstkontroll med hjälp av PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Nästa steg

* Använd Azure-portalen för att [komma åt Azure AD-autentisering för att använda Azure Media Services API](media-services-portal-get-started-with-aad.md).
* Använd Azure AD-autentisering för att [komma åt Azure Media Services API med .NET](media-services-dotnet-get-started-with-aad.md).

