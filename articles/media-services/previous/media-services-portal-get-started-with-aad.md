---
title: Komma igång med Azure AD-autentisering med hjälp av Azure-portalen| Microsoft-dokument
description: Lär dig hur du använder Azure-portalen för att komma åt Azure Active Directory-autentisering (Azure AD) för att använda Azure Media Services API.
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
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330676"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Kom igång med Azure AD-autentisering med Azure-portalen

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Lär dig hur du använder Azure-portalen för att komma åt Azure Active Directory -autentisering (Azure AD) för att komma åt Azure Media Services API.Learn how to use the Azure portal to access Azure Active Directory (Azure AD) authentication to access the Azure Media Services API.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Om du inte har ett konto börjar du med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [Skapa ett Azure Media Services-konto med hjälp av Azure-portalen](media-services-portal-create-account.md).

När du använder Azure AD-autentisering med Azure Media Services har du två autentiseringsalternativ:

- **Autentisering av tjänstens huvudnamn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemontjänster, mellannivåtjänster eller schemalagda jobb: webbappar, funktionsappar, logikappar, API:er eller en mikrotjänst.
- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services-resurser. Det interaktiva programmet bör först uppmana användaren om autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller livestreaming. 

## <a name="access-the-media-services-api"></a>Åtkomst till Media Services-API:n

På den här sidan kan du välja den autentiseringsmetod som du vill använda för att ansluta till API:et. Sidan innehåller också de värden du behöver för att ansluta till API: et.

1. Välj ditt Media Services-konto i [Azure-portalen.](https://portal.azure.com/)
2. Välj hur du ansluter till API:et för medietjänster.
3. Under **Anslut till Api för mediatjänster**väljer du den API-version för Media Services som du vill ansluta till.

## <a name="service-principal-authentication--recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)

Autentiserar en tjänst med hjälp av en Azure Active Directory-app (Azure AD) och hemlig. Detta rekommenderas för alla mellannivåtjänster som anropar API:et för Medietjänster. Exempel är webbappar, funktioner, logikappar, API:er och mikrotjänster. Detta är den rekommenderade autentiseringsmetoden.

### <a name="manage-your-azure-ad-app-and-secret"></a>Hantera din Azure AD-app och hemlighet

Med **avsnittet Hantera din AAD-app och hemliga** kan du välja eller skapa en ny Azure AD-app och generera en hemlighet. Av säkerhetsskäl kan hemligheten inte visas efter att bladet har stängts. Programmet använder program-ID och hemlighet för autentisering för att hämta en giltig token för medietjänster.

Se till att du har tillräcklig behörighet för att registrera ett program med din Azure AD-klient och tilldela programmet till en roll i din Azure-prenumeration. Mer information finns i [Krävs behörigheter](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Ansluta till API för Medietjänster

**Api:et för Ansluta till mediatjänster** ger dig värden som du använder för att ansluta tjänstens huvudprogram. Du kan hämta textvärden eller kopiera JSON- eller XML-blocken.

## <a name="user-authentication"></a>Användarautentisering

Det här alternativet kan användas för att autentisera en anställd eller medlem i en Azure Active Directory som använder en app för att interagera med Media Services-resurser. Det interaktiva programmet bör först fråga användaren om användarens autentiseringsuppgifter. Den här autentiseringsmetoden bör endast användas för hanteringsprogram.

### <a name="connect-to-media-services-api"></a>Ansluta till API för Medietjänster

Kopiera dina autentiseringsuppgifter för att ansluta ditt användarprogram från **API:et anslut till mediatjänster.** Du kan hämta textvärden eller kopiera JSON- eller XML-blocken.

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-portal-upload-files.md).
