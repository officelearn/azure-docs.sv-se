---
title: Hantera användar data i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du tar bort eller exporterar användar data i Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 78726620db119abf617be8a30cf03697b04e382b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064070"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Hantera användar data i Azure Active Directory B2C

 Den här artikeln beskriver hur du kan hantera användar data i Azure Active Directory B2C (Azure AD B2C) med hjälp av de åtgärder som tillhandahålls av [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog). Hantering av användar data inkluderar borttagning eller export av data från gransknings loggar.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Ta bort användar data

Användar data lagras i Azure AD B2C-katalogen och i gransknings loggarna. Alla användar gransknings data behålls i 7 dagar i Azure AD B2C. Om du vill ta bort användar data inom den 7-dagars perioden kan du använda åtgärden [ta bort en användare](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) . Det krävs en BORTTAGNINGs åtgärd för var och en av de Azure AD B2C klienter där data kan finnas.

Varje användare i Azure AD B2C tilldelas ett objekt-ID. Objekt-ID ger en entydig identifierare som du kan använda för att ta bort användar data i Azure AD B2C. Beroende på din arkitektur kan objekt-ID: t vara en användbar korrelations identifierare över andra tjänster, till exempel ekonomi-, marknadsförings-och kund Relations hanterings databaser.

Det mest exakta sättet att hämta objekt-ID: t för en användare är att hämta det som en del av en autentiserings resa med Azure AD B2C. Om du får en giltig begäran om data från en användare med hjälp av andra metoder kan en frånkopplad process, till exempel en sökning från en kund tjänst support agent, vara nödvändig för att hitta användaren och anteckna det associerade objekt-ID: t.

I följande exempel visas ett möjligt data borttagnings flöde:

1. Användaren loggar in och väljer **ta bort mina data**.
2. Programmet erbjuder ett alternativ för att ta bort data i ett administrations avsnitt i programmet.
3. Programmet tvingar en autentisering att Azure AD B2C. Azure AD B2C tillhandahåller en token med objekt-ID: t för den användare som är tillbaka till programmet.
4. Token tas emot av programmet och objekt-ID: t används för att ta bort användar data via ett anrop till Azure AD-Graph API. Azure AD-Graph API tar bort användar data och returnerar status koden 200 OK.
5. Programmet dirigerar borttagningen av användar data i andra organisations system efter behov med hjälp av objekt-ID eller andra identifierare.
6. Programmet bekräftar borttagningen av data och ger nästa steg för användaren.

## <a name="export-customer-data"></a>Exportera kund information

Processen för att exportera kund information från Azure AD B2C liknar borttagnings processen.

Azure AD B2C användar data är begränsade till:

- **Data som lagras i Azure Active Directory**: Du kan hämta data i en Azure AD B2C användar resa med hjälp av objekt-ID eller inloggnings namn, till exempel en e-postadress eller ett användar namn.
- **Användarspecifik gransknings händelse rapport**: Du kan indexera data med hjälp av objekt-ID: t.

I följande exempel av ett data flöde för export kan de steg som beskrivs som utförs av programmet också utföras av en server dels process eller en användare med en administratörs roll i katalogen:

1. Användaren loggar in i programmet. Azure AD B2C tvingar autentisering med Azure Multi-Factor Authentication vid behov.
2. Programmet använder användarautentiseringsuppgifter för att anropa en Azure AD Graph API-åtgärd för att hämta användarattribut. Azure AD-Graph API tillhandahåller attributets data i JSON-format. Beroende på schemat kan du ange innehållet i ID-token så att det inkluderar all personlig information om en användare.
3. Programmet hämtar användar gransknings aktiviteten. Azure AD-Graph API tillhandahåller händelse data till programmet.
4. Programmet samlar in data och gör dem tillgängliga för användaren.

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar användares åtkomst till programmet finns i [hantera användar åtkomst](manage-user-access.md).




