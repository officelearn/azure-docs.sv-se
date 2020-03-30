---
title: Komma igång med Azure AD-autentisering
description: Lär dig hur du kommer åt Azure Active Directory-autentisering (Azure AD) för att använda Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478258"
---
# <a name="get-credentials-to-access-media-services-api"></a>Hämta autentiseringsuppgifter för att komma åt Api för Media Services  

När du använder Azure AD-autentisering för att komma åt Azure Media Services API har du två autentiseringsalternativ:

- **Autentisering av tjänstens huvudnamn** (rekommenderas)

    Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemontjänster, mellannivåtjänster eller schemalagda jobb: webbappar, funktionsappar, logikappar, API:er eller en mikrotjänst.
- **Användarautentisering**

    Autentisera en person som använder appen för att interagera med Media Services-resurser. Det interaktiva programmet bör först uppmana användaren om autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller livestreaming. 

I den här artikeln beskrivs steg för att få autentiseringsuppgifter för att komma åt Api för Media Services. Välj bland följande flikar.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Om du inte har ett konto börjar du med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [Skapa ett Azure Media Services-konto med hjälp av Azure-portalen](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

### <a name="api-access"></a>API-åtkomst 

Med **API-åtkomstsidan** kan du välja den autentiseringsmetod som du vill använda för att ansluta till API:et. Sidan innehåller också de värden du behöver för att ansluta till API: et.

1. Välj ditt Media Services-konto i [Azure-portalen.](https://portal.azure.com/)
2. Välj hur du ansluter till API:et för medietjänster.
3. Under **Anslut till Api för mediatjänster**väljer du den API-version för Media Services som du vill ansluta till (V3 är den senaste versionen av tjänsten).

### <a name="service-principal-authentication--recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)

Autentiserar en tjänst med hjälp av en Azure Active Directory-app (Azure AD) och hemlig. Detta rekommenderas för alla mellannivåtjänster som anropar API:et för Medietjänster. Exempel är webbappar, funktioner, logikappar, API:er och mikrotjänster. Detta är den rekommenderade autentiseringsmetoden.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Hantera din Azure AD-app och hemlighet

Med **avsnittet Hantera din AAD-app och hemliga** kan du välja eller skapa en ny Azure AD-app och generera en hemlighet. Av säkerhetsskäl kan hemligheten inte visas efter att bladet har stängts. Programmet använder program-ID och hemlighet för autentisering för att hämta en giltig token för medietjänster.

Se till att du har tillräcklig behörighet för att registrera ett program med din Azure AD-klient och tilldela programmet till en roll i din Azure-prenumeration. Mer information finns i [Krävs behörigheter](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Ansluta till API för Medietjänster

**Api:et för Ansluta till mediatjänster** ger dig värden som du använder för att ansluta tjänstens huvudprogram. Du kan hämta textvärden eller kopiera JSON- eller XML-blocken.

### <a name="user-authentication"></a>Användarautentisering

Det här alternativet kan användas för att autentisera en anställd eller medlem i en Azure Active Directory som använder en app för att interagera med Media Services-resurser. Det interaktiva programmet bör först fråga användaren om användarens autentiseringsuppgifter. Den här autentiseringsmetoden bör endast användas för hanteringsprogram.

#### <a name="connect-to-media-services-api"></a>Ansluta till API för Medietjänster

Kopiera dina autentiseringsuppgifter för att ansluta ditt användarprogram från **API:et anslut till mediatjänster.** Du kan hämta textvärden eller kopiera JSON- eller XML-blocken.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md).
