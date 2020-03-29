---
title: Definiera anpassade attribut i Azure Active Directory B2C | Microsoft-dokument
description: Definiera anpassade attribut för ditt program i Azure Active Directory B2C för att samla in information om dina kunder.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186057"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiera anpassade attribut i Azure Active Directory B2C

 Varje kundinriktad applikation har unika krav på den information som behöver samlas in. Din Azure Active Directory B2C-klient (Azure AD B2C) levereras med en inbyggd uppsättning information som lagras i attribut, till exempel förnamn, efternamn, ort och postnummer. Med Azure AD B2C kan du utöka uppsättningen attribut som lagras på varje kundkonto.

 Du kan skapa anpassade attribut i [Azure-portalen](https://portal.azure.com/) och använda dem i dina registreringsanvändarflöden, registrerings- eller inloggningsanvändarflöden eller profilredigering användarflöden. Du kan också läsa och skriva dessa attribut med hjälp av [Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**.

    ![Växla till Azure AD B2C-klientorganisationen](./media/user-flow-custom-attributes/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![B2C-klient markerad i katalog- och prenumerationsfilter](./media/user-flow-custom-attributes/select-directory.PNG)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Användarattribut**och välj sedan **Lägg till**.
5. Ange ett **namn** för det anpassade attributet (till exempel "ShoeSize")
6. Välj en **datatyp**. Endast **Sträng,** **Booleska**och **Int** är tillgängliga.
7. Du kan också ange en **beskrivning** i informationssyfte.
8. Klicka på **Skapa**.

Det anpassade attributet är nu tillgängligt i listan över **användarattribut** och för användning i dina användarflöden. Ett anpassat attribut skapas bara första gången det används i ett användarflöde och inte när du lägger till det i listan över **användarattribut**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Använda ett anpassat attribut i användarflödet

1. I din Azure AD B2C-klient väljer du **Användarflöden**.
1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **Användarattribut** och välj sedan det anpassade attributet (till exempel "ShoeSize"). Klicka på **Spara**.
1. Välj **Programanspråk** och välj sedan det anpassade attributet.
1. Klicka på **Spara**.

När du har skapat en ny användare med hjälp av ett användarflöde som använder det nyskapade anpassade attributet kan objektet efterfrågas i Utforskaren i [Microsoft Graph.](https://developer.microsoft.com/graph/graph-explorer) Alternativt kan du använda funktionen [Kör användarflöde](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) på användarflödet för att verifiera kundupplevelsen. Du bör nu se **ShoeSize** i listan över attribut som samlats in under registreringsresan och se den i den token som skickas tillbaka till ditt program.
