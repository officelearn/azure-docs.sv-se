---
title: Definiera anpassade attribut i Azure Active Directory B2C | Microsoft Docs
description: Definiera anpassade attribut för ditt program i Azure Active Directory B2C att samla in information om dina kunder.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 53d9aec5a689babb637d2eff6b36ea3b8bd8d97f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173946"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiera anpassade attribut i Azure Active Directory B2C

 Varje kund program har unika krav för den information som behöver samlas in. Azure Active Directory (Azure AD) B2C-klienten levereras med en inbyggd uppsättning information som lagras i attribut, till exempel förnamn, efternamn, stad och postnummer. Med Azure AD B2C kan du utöka uppsättningen attribut som lagras på varje kund-ID. 
 
 Du kan skapa anpassade attribut i den [Azure-portalen](https://portal.azure.com/) och Använd dem i din registrering användarflöden eller registrera dig eller logga in användarflöden profilredigering användarflöden. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Anpassade attribut i Azure AD B2C använder [Azure AD Graph API Directory-schemautökningar](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användarattribut**, och välj sedan **Lägg till**.
5. Ange en **namn** för det anpassade attributet (till exempel ”ShoeSize”)
6. Välj en **datatypen**. Endast **sträng**, **booleskt**, och **Int** är tillgängliga.
7. Alternativt kan du ange en **beskrivning** i informationssyfte. 
8. Klicka på **Skapa**.

Det anpassade attributet är nu tillgänglig i listan över **användarattribut** och för användning i dina användarflöden. Ett anpassat attribut är endast skapas första gången den används i alla användarflödet, men inte när du lägger till den i listan över **användarattribut**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Använd ett anpassat attribut i ditt användarflöde

1. I din Azure AD B2C-klient väljer **användarflöden**.
2. Välj din princip (till exempel ”B2C_1_SignupSignin”) för att öppna den. 
4. Välj **användarattribut** och välj sedan det anpassade attributet (till exempel ”ShoeSize”). Klicka på **Spara**.
5. Välj **Programanspråk** och välj sedan det anpassade attributet. 
6. Klicka på **Spara**.

Du kan använda den **kör användarflödet** -funktionen på användarflödet att verifiera kundupplevelsen. Du bör nu se **ShoeSize** i listan över attribut som samlas in under registrering resa och se hur den token som skickas tillbaka till programmet.

