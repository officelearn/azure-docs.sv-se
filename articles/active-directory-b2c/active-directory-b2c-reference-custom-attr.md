---
title: Definiera anpassade attribut i Azure Active Directory B2C | Microsoft Docs
description: Definiera anpassade attribut för ditt program i Azure Active Directory B2C att samla in information om dina kunder.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968782"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiera anpassade attribut i Azure Active Directory B2C

 Varje kund program har unika krav för den information som behöver samlas in. Azure Active Directory (Azure AD) B2C-klienten levereras med en inbyggd uppsättning information som lagras i attribut, till exempel förnamn, efternamn, stad och postnummer. Med Azure AD B2C kan du utöka uppsättningen attribut som lagras på varje kund-ID. 
 
 Du kan skapa anpassade attribut i den [Azure-portalen](https://portal.azure.com/) och använda dem i din registreringsprinciper, registrerings-eller logga in eller profilredigeringsprinciper. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Anpassade attribut i Azure AD B2C använder [Azure AD Graph API Directory-schemautökningar](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
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

Det anpassade attributet är nu tillgänglig i listan över **användarattribut** och för användning i dina principer. Ett anpassat attribut är endast skapas första gången den används i princip, men inte när du lägger till den i listan över **användarattribut**.

## <a name="use-a-custom-attribute-in-your-policy"></a>Använd ett anpassat attribut i din princip

1. I din Azure AD B2C-klient väljer **registrerings-eller logga in**.
2. Välj din princip (till exempel ”B2C_1_SignupSignin”) för att öppna den. 
3. Klicka på **Redigera**.
4. Välj **registreringsattribut** och välj sedan det anpassade attributet (till exempel ”ShoeSize”). Klicka på **OK**.
5. Välj **Programanspråk** och välj sedan det anpassade attributet. Klicka på **OK**.
6. Klicka på **Spara**.

Du kan använda den **kör nu** funktionen på principen för att verifiera kundupplevelsen. Du bör nu se **ShoeSize** i listan över attribut som samlas in under registrering resa och se hur den token som skickas tillbaka till programmet.

