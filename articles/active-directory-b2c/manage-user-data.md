---
title: Hantera användardata i Azure Active Directory B2C | Microsoft Docs
description: Lär dig ta bort eller exportera användardata i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 62846fe744e7295f58902481400ce91770c916da
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798120"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Hantera användardata i Azure Active Directory B2C

 Den här artikeln beskrivs hur du kan hantera användardata i Azure Active Directory (Azure AD) B2C med hjälp av de åtgärder som tillhandahålls av den [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog). Hantera användardata innehåller tas bort eller exportera data från granskningsloggar.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Ta bort användardata

Användarinformationen är lagrad i Azure AD B2C-katalogen och i granskningsloggarna. Alla data för granskning sparas i 30 dagar i Azure AD B2C. Om du vill ta bort användardata i denna 30-dagarsperiod kan du använda den [ta bort en användare](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) igen. En borttagningsåtgärd måste anges för var och en av Azure AD B2C-klienter där data kan finnas. 

Varje användare i Azure AD B2C tilldelas ett objekt-ID. Objekt-ID innehåller en entydiga identifierare som du kan använda för att ta bort användardata i Azure AD B2C. Beroende på din arkitektur, objekt-ID kan vara en användbar korrelationsidentifierare över andra tjänster, till exempel finansiella institutioner marknadsföring och kunddatabaser relation management. 

Det bästa sättet att hämta objekt-ID för en användare är att hämta den som en del av en resa för autentisering med Azure AD B2C. Om du får en giltig begäran om data från en användare genom att använda andra metoder, en offlineprocess, till exempel en sökning efter en kundsupport kan agent, vara nödvändigt att hitta användaren och Observera associerade objekt-ID. 

I följande exempel visas ett möjligt Databorttagning flöde:

1. Användaren loggar in och väljer **ta bort Mina data**.
2. Programmet ger möjlighet att ta bort data i en administrationsavsnittet i programmet.
3. Programmet tvingar en autentisering till Azure AD B2C. Azure AD B2C tillhandahåller en token med objekt-ID för användaren till programmet. 
4. Token tas emot av programmet och det objekt som ID används för att ta bort användarens data via ett anrop till Azure AD Graph API. Azure AD Graph API tar bort alla användardata och returnerar statuskoden 200 OK.
5. Programmet samordnar borttagningen av användardata i andra organisationens behov med hjälp av objekt-ID eller andra identifierare.
6. Programmet bekräftar borttagningen av data och innehåller nästa steg för användaren.

## <a name="export-customer-data"></a>Exportera kunddata

Exportera kunddata från Azure AD B2C liknar borttagningsprocessen.

Azure AD B2C-användardata är begränsat till:

- **Data som lagras i Azure Active Directory**: Du kan hämta data i en användarresa för Azure AD B2C-autentisering med hjälp av objekt-ID eller ett logga in namn, till exempel en e-postadress eller användarnamn. 
- **Händelser för användarspecifika granskningsrapporten**: Du kan indexera data med hjälp av objekt-ID.

I följande exempel visar en export-dataflöde kan stegen som beskrivs som utförs av programmet också utföras av en backend-process eller en användare med administratörsroll i katalogen:

1. Användaren loggar in till programmet. Azure AD B2C använder autentisering med Azure Multi-Factor Authentication vid behov.
2. Programmet använder autentiseringsuppgifter för att anropa en Azure AD Graph API-åtgärd för att hämta användarattributen. Azure AD Graph-API: et tillhandahåller attributdata i JSON-format. Beroende på schemat, kan du ange ID-token innehållet att inkludera alla personliga data om en användare.
3. Programmet hämtar granska användaraktivitet. Azure AD Graph-API: et tillhandahåller händelsedata till programmet.
4. Programmet sammanställer data och gör den tillgänglig för användaren.

## <a name="next-steps"></a>Nästa steg

- Läs hur du hanterar hur användare kommer åt ditt program i [hantera användaråtkomst](manage-user-access.md).




