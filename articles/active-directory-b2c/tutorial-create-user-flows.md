---
title: Självstudiekurs - Skapa användarflöden - Azure Active Directory B2C
description: Lär dig hur du skapar användarflöden i Azure-portalen för att aktivera registrering, inloggning och redigering av användarprofiler för dina program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264250"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Självstudiekurs: Skapa användarflöden i Azure Active Directory B2C

I dina program kan du ha [användarflöden](user-flow-overview.md) som gör det möjligt för användare att registrera sig, logga in eller hantera sin profil. Du kan skapa flera användarflöden av olika typer i din Azure Active Directory B2C-klientorganisation (Azure AD B2C) och använda dem i dina program efter behov. Användarflöden kan återanvändas över program.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett användarflöde för registrering och inloggning
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Den här självstudien visar hur du skapar några rekommenderade användarflöden med hjälp av Azure-portalen. Om du letar efter information om hur du konfigurerar ett ROPC-flöde (Resource Owner Password Credentials) i ditt program läser du [Konfigurera flödet för lösenord för resursägare i Azure AD B2C](configure-ropc.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

[Registrera dina program](tutorial-register-applications.md) som ingår i de användarflöden som du vill skapa.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användarflöde för registrering och inloggning

Användarflödet för registrering och inloggning hanterar både registrerings- och inloggningsupplevelser med en enda konfiguration. Användare av ditt program leds på rätt väg beroende på sammanhanget.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.

    ![B2C-klient-, katalog- och prenumerationsfönstret, Azure-portalen](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Under **Principer**väljer du **Användarflöden (principer)** och väljer sedan **Nytt användarflöde**.

    ![Sidan Användarflöden i portalen med knappen Nytt användarflöde markerad](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. På fliken **Rekommenderad** väljer du **användarflödet Registrera dig och logga in.**

    ![Välj en sida för användarflöde med Registrera dig och logga in flödet markerat](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitetsleverantörer**väljer du **E-postsignup**.

    ![Skapa användarflödessida i Azure-portalen med egenskaper markerade](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. För **användarattribut och anspråk**väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer**och välj sedan attribut och anspråk för **land/region,** **visningsnamn**och **postnummer**. Klicka på **OK**.

    ![Attribut och anspråk urvalssida med tre anspråk markerade](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicka på **Skapa** om du vill lägga till användarflödet. Ett prefix *med B2C_1* läggs automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. Välj det användarflöde som du skapade för att öppna översiktssidan och välj sedan **Kör användarflöde**.
1. För **Ansökan**väljer du webbprogrammet *webapp1* som du tidigare har registrerat. **Svars-URL:en** ska visa `https://jwt.ms`.
1. Klicka på **Kör användarflöde**och välj sedan **Registrera dig nu**.

    ![Kör användarflödessida i portalen med knappen Kör användarflöde markerad](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Ange en giltig e-postadress, klicka på **Skicka verifieringskod,** ange verifieringskoden som du får och välj sedan **Verifiera kod**.
1. Ange ett nytt lösenord och bekräfta lösenordet.
1. Välj land och region, ange det namn som du vill visa, ange ett postnummer och klicka sedan på **Skapa**. Token returneras och `https://jwt.ms` ska visas för dig.
1. Du kan nu köra användarflödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller de anspråk som du har valt för land/region, namn och postnummer.

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna ska kunna redigera sin profil i programmet använder du ett användarflöde för profilredigering.

1. Välj **Användarflöden (principer)** på menyn på översiktssidan för Azure AD B2C-klienten och välj sedan **Nytt användarflöde**.
1. Välj användarflödet **för profilredigering** på fliken **Rekommenderad.**
1. Ange ett **Namn** för användarflödet. *Profileratit1*.
1. För **identitetsleverantörer**väljer du **Lokalt kontotecken.**
1. För **användarattribut**väljer du de attribut som du vill att kunden ska kunna redigera i sin profil. Välj till exempel **Visa mer**och välj sedan både attribut och anspråk för **Visningsnamn** och **Befattning .** Klicka på **OK**.
1. Klicka på **Skapa** om du vill lägga till användarflödet. Ett prefix *med B2C_1* läggs automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. Välj det användarflöde som du skapade för att öppna översiktssidan och välj sedan **Kör användarflöde**.
1. För **Ansökan**väljer du webbprogrammet *webapp1* som du tidigare har registrerat. **Svars-URL:en** ska visa `https://jwt.ms`.
1. Klicka på **Kör användarflöde**och logga sedan in med det konto som du tidigare skapade.
1. Nu har du möjlighet att ändra användarens visningsnamn och befattning. Klicka på **Fortsätt**. Token returneras och `https://jwt.ms` ska visas för dig.

## <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill att användarna av programmet ska kunna återställa sitt lösenord använder du ett användarflöde för återställning av lösenord.

1. På översiktsmenyn för Azure AD B2C-klienter väljer du **Användarflöden (principer)** och väljer sedan **Nytt användarflöde**.
1. Välj användarflödet för återställning **av lösenord** på fliken **Rekommenderad.**
1. Ange ett **Namn** för användarflödet. Till exempel *passwordreset1*.
1. För **identitetsleverantörer**aktiverar du **Återställ lösenord med e-postadress**.
1. Under Programanspråk klickar du på **Visa mer** och väljer de anspråk som du vill ska returneras i auktoriseringstoken som skickas tillbaka till ditt program. Välj till exempel **Användarobjekt-id**.
1. Klicka på **OK**.
1. Klicka på **Skapa** om du vill lägga till användarflödet. Ett prefix *med B2C_1* läggs automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. Välj det användarflöde som du skapade för att öppna översiktssidan och välj sedan **Kör användarflöde**.
1. För **Ansökan**väljer du webbprogrammet *webapp1* som du tidigare har registrerat. **Svars-URL:en** ska visa `https://jwt.ms`.
1. Klicka på **Kör användarflöde,** verifiera e-postadressen för det konto som du tidigare skapade och välj **Fortsätt**.
1. Nu har du möjlighet att ändra lösenordet för användaren. Ändra lösenordet och välj **Fortsätt**. Token returneras och `https://jwt.ms` ska visas för dig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa ett användarflöde för registrering och inloggning
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Läs sedan om hur du lägger till identitetsleverantörer i dina program för att aktivera användarinloggning med leverantörer som Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft eller Twitter.

> [!div class="nextstepaction"]
> [Lägg till identitetsleverantörer i dina program >](tutorial-add-identity-providers.md)