---
title: Definiera anpassade attribut i Azure Active Directory B2C | Microsoft Docs
description: Definiera anpassade attribut för ditt program i Azure Active Directory B2C om du vill samla in information om dina kunder.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2233dbd7b0e669c23397b4bc6a84f2bfdc208391
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952819"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiera anpassade attribut i Azure Active Directory B2C

 Varje kundriktad app har unika krav för den information som behöver samlas in. Din Azure Active Directory B2C (Azure AD B2C) har en inbyggd uppsättning information som lagras i attribut, t. ex. namn, efter namn, stad och post nummer. Med Azure AD B2C kan du utöka uppsättningen med attribut som lagras på varje kund konto.

 Du kan skapa anpassade attribut i [Azure Portal](https://portal.azure.com/) och använda dem i dina registrering användar flöden, registrering eller inloggnings användar flöden eller profil redigering av användar flöden. Du kan också läsa och skriva attributen med hjälp av [Microsoft Graph-API: et](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**.

    ![Växla till Azure AD B2C-klientorganisationen](./media/user-flow-custom-attributes/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![B2C-klient har marker ATS i katalog-och prenumerations filter](./media/user-flow-custom-attributes/select-directory.PNG)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användarattribut och välj sedan** **Lägg till**.
5. Ange ett **namn** för det anpassade attributet (till exempel "ShoeSize")
6. Välj en **datatyp**. Endast **String**, **Boolean** och **int** är tillgängliga.
7. Alternativt kan du ange en **Beskrivning** i informations syfte.
8. Klicka på **Skapa**.

Det anpassade attributet är nu tillgängligt i listan över **användarattribut** och för användning i dina användar flöden. Ett anpassat attribut skapas bara första gången det används i alla användar flöden och inte när du lägger till **det i listan med användarattribut**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Använd ett anpassat attribut i ditt användar flöde

1. Välj **användar flöden** i Azure AD B2C klient.
1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **användarattribut och välj** sedan det anpassade attributet (till exempel "ShoeSize"). Klicka på **Spara**.
1. Välj **program anspråk** och välj sedan det anpassade attributet.
1. Klicka på **Spara**.

När du har skapat en ny användare med hjälp av ett användar flöde som använder det nyligen skapade anpassade attributet, kan du frågas om objektet i [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Du kan också använda funktionen [Kör användar flöde](./tutorial-create-user-flows.md) i användar flödet för att verifiera kund upplevelsen. Nu bör du se **ShoeSize** i listan över attribut som samlats in under registreringen och se den i den token som skickas tillbaka till ditt program.