---
title: Hantera användardata i Azure Active Directory B2C | Microsoft-dokument
description: Lär dig hur du tar bort eller exporterar användardata i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e245b58449ab773914fc60be056082b82f05035a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184493"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Hantera användardata i Azure Active Directory B2C

 I den här artikeln beskrivs hur du kan hantera användardata i Azure Active Directory B2C (Azure AD B2C) med hjälp av de åtgärder som tillhandahålls av [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api). Hantering av användardata omfattar att ta bort eller exportera data från granskningsloggar.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Ta bort användardata

Användardata lagras i Azure AD B2C-katalogen och i granskningsloggarna. Alla användargranskningsdata lagras i 7 dagar i Azure AD B2C. Om du vill ta bort användardata inom den 7-dagarsperioden kan du använda åtgärden [Ta bort en användare.](https://docs.microsoft.com/graph/api/user-delete) En DELETE-åtgärd krävs för var och en av Azure AD B2C-klienter där data kan finnas.

Varje användare i Azure AD B2C tilldelas ett objekt-ID. Objekt-ID:et ger en entydig identifierare som du kan använda för att ta bort användardata i Azure AD B2C. Beroende på din arkitektur kan objekt-ID vara en användbar korrelationsidentifierare för andra tjänster, till exempel databaser för ekonomi-, marknadsförings- och kundrelationshantering.

Det mest exakta sättet att få objekt-ID för en användare är att hämta det som en del av en autentiseringsresa med Azure AD B2C. Om du får en giltig begäran om data från en användare med andra metoder kan en offlineprocess, till exempel en sökning av en kundtjänstsupportagent, vara nödvändig för att hitta användaren och notera det associerade objekt-ID:t.

I följande exempel visas ett möjligt dataextringsflöde:

1. Användaren loggar in och väljer **Ta bort mina data**.
2. Programmet erbjuder en möjlighet att ta bort data inom en administration avsnitt av programmet.
3. Programmet tvingar en autentisering till Azure AD B2C. Azure AD B2C tillhandahåller en token med användarens objekt-ID tillbaka till programmet.
4. Token tas emot av programmet och objekt-ID används för att ta bort användardata via ett anrop till Microsoft Graph API. Microsoft Graph API tar bort användardata och returnerar en statuskod på 200 OK.
5. Programmet dirigerar borttagningen av användardata i andra organisationssystem efter behov med hjälp av objekt-ID eller andra identifierare.
6. Programmet bekräftar radering av data och ger nästa steg till användaren.

## <a name="export-customer-data"></a>Exportera kunddata

Processen att exportera kunddata från Azure AD B2C liknar borttagningsprocessen.

Azure AD B2C-användardata är begränsad till:

- **Data som lagras i Azure Active Directory:** Du kan hämta data i en Azure AD B2C-autentiseringsanvändarresa med hjälp av objekt-ID eller inloggningsnamn, till exempel en e-postadress eller ett användarnamn.
- **Användarspecifik granskningshändelser rapport**: Du kan indexera data med hjälp av objekt-ID.

I följande exempel på ett exportdataflöde kan de steg som beskrivs som utförs av programmet också utföras av antingen en serverdprocess eller en användare med administratörsroll i katalogen:

1. Användaren loggar in på programmet. Azure AD B2C tillämpar autentisering med Azure Multi-Factor Autentisering om det behövs.
2. Programmet använder användarautentiseringsuppgifterna för att anropa en Microsoft Graph API-åtgärd för att hämta användarattributen. Microsoft Graph API tillhandahåller attributdata i JSON-format. Beroende på schemat kan du ange att ID-tokeninnehållet ska innehålla alla personuppgifter om en användare.
3. Programmet hämtar användargranskningsaktiviteten. Microsoft Graph API tillhandahåller händelsedata till programmet.
4. Programmet sammanställer data och gör den tillgänglig för användaren.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar hur användare kommer åt ditt program finns i [Hantera användaråtkomst](manage-user-access.md).
