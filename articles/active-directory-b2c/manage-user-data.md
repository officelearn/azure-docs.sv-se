---
title: Hantera användardata i Azure AD B2C | Microsoft Docs
description: Lär dig mer om att ta bort eller exportera användardata i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.openlocfilehash: 414221c3e4942801b5792835d520ec936c8c4bbb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Hantera användardata i Azure AD B2C

 Den här artikeln innehåller information om hur du kan hantera användardata i Azure Active Directory (AD) B2C med hjälp av de åtgärder som tillhandahålls av den [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). Hantera användardata omfattar möjligheten att ta bort data eller exportera data från-granskningsloggarna.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Ta bort användardata

Användarinformationen är lagrad i Azure AD B2C-katalogen och i granskningsloggarna. Alla användare granskningsdata sparas i 30 dagar data finns kvar i Azure AD B2C. Om du vill ta bort användardata inom de 30 dagarna kan du använda den [tar bort en användare](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) igen. En borttagningsåtgärd måste anges för varje Azure AD B2C-klienter där data kan finnas. 

Varje användare i Azure AD B2C tilldelas ett objekt-ID. Objekt-ID innehåller en entydigt identifierare som du kan använda för att ta bort användardata i Azure AD B2C.  Beroende på arkitekturen, objekt-ID kan vara användbart korrelationsidentifierare över andra tjänster som finansiella, marknadsföring och customer relationship management databaser.  

Det bästa sättet att hämta objekt-ID för en användare är att hämta det som en del av en resa för autentisering med Azure AD B2C.  Om en giltig begäran om data tas emot från en användare som använder andra metoder, en offlineprocess, till exempel en sökning efter en kundsupport kan agent, vara nödvändigt att hitta användaren och Observera associerade objekt-ID. 

I följande exempel visas en möjlig dataflöde för borttagning:

1. Användaren loggar in och väljer **ta bort Mina data**.
2. Programmet ger möjlighet att ta bort data i en administrationsavsnittet i programmet.
3. Programmet tvingar en Azure AD B2C-autentisering. Azure AD B2C ger en token med objekt-ID för användaren till programmet. 
4. Token som tas emot av programmet och ID: T används för att ta bort användardata via ett anrop till Azure AD Graph API-objektet. Azure AD Graph API tar bort användardata och returnerar en statuskod 200 OK.
5. Programmet samordnar borttagning av användardata i andra organisatoriska efter behov med objekt-ID eller andra identifierare.
6. Programmet bekräftar borttagningen av data och förser användaren med nästa steg.

## <a name="export-customer-data"></a>Exportera kundinformation

Processen för att exportera kunddata från Azure AD B2C liknar borttagningsprocessen.

Azure AD B2C-användardata är begränsad till:

- **Data som lagras i Azure Active Directory** -Data hämtas i en Azure AD B2C autentisering användaren resa med objekt-ID eller ett namn, till exempel användarnamn eller e-post.  
- **Händelser för användarspecifika kontrollrapport** -Data indexeras med hjälp av objekt-ID.

I följande exempel i en export dataflödet kan stegen som utförs av programmet också utföras av en backend-process eller av en användare med administratörsroll i katalogen:

1. Användaren loggar in på programmet. Azure AD B2C kräver autentisering med multifaktor-auktorisering om det behövs.
2. Programmet använder användarens autentiseringsuppgifter för att anropa en Azure AD Graph API-åtgärd för att hämta användarattribut. Azure AD Graph API ger attributdata i JSON-format. Beroende på schemat, kan du ange id token innehållet så att alla personliga data om en användare.
3. Programmet hämtar slutanvändarens Granska aktivitet. Azure AD Graph API ger händelsedata till programmet.
4. programmet samlar in data och gör den tillgänglig för användaren.

## <a name="next-steps"></a>Nästa steg

- Lär dig att hantera hur användare kan komma åt ditt program i [hantera användarnas åtkomst](manage-user-access.md)




