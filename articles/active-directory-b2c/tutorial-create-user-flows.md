---
title: Självstudie – skapa användarflöden - Azure Active Directory B2C
description: Lär dig hur du skapar användarflöden i Azure portal för att aktivera logga in, logga in och användaren profilredigering för dina program i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056337"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Självstudier: Skapa användarflöden i Azure Active Directory B2C

Du kan ha i dina program [användarflöden](active-directory-b2c-reference-policies.md) som användarna kan registrera, logga in eller hanterar sina profiler. Du kan skapa flera användarflöden av olika typer i Azure Active Directory (Azure AD) B2C-klienten och använder dem i dina program efter behov. Användarflöden kan återanvändas i program.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett användarflöde för registrering och logga in
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Den här självstudien visar hur du skapar vissa rekommenderade användarflöden med hjälp av Azure portal. Om du letar efter information om hur du ställer in en resurs ägare lösenordsinformation (ROPC) flow i ditt program, se [konfigurera lösenordsinformation för resurs-ägare för flow i Azure AD B2C](configure-ropc.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

[Registrera ditt program](tutorial-register-applications.md) som ingår i användarflöden som du vill skapa.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användarflöde för registrering och logga in

Registrera dig och logga in användarflödet hanterar registrering och inloggning upplevelser med en enda konfiguration. Användare av ditt program leds av rätt väg beroende på kontext.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.

    ![Växla till prenumerationskatalogen](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. I den vänstra menyn under **principer**väljer **användarflöden (principer)** , och välj sedan **nytt användarflöde**.

    ![Välj nytt användarflöde](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. På den **rekommenderas** fliken den **logga och logga in** användarflödet.

    ![Välj användarflödet som registrera dig och logga in](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitetsprovidrar**väljer **e-postregistrering**.

    ![Ange egenskaper för flow](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. För **användarattribut och anspråk**, Välj anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj exempelvis **visa fler**, och välj sedan attribut och anspråk för **Land/Region**, **visningsnamn**, och **postnummer**. Klicka på **OK**.

    ![Välj de attribut och anspråk](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicka på **skapa** att lägga till användarflödet. Prefixet *B2C_1* automatiskt läggas till i namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. Välj användarflödet som du skapade för att öppna dess översiktssidan och välj sedan **kör användarflödet**.
1. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
1. Klicka på **kör användarflödet**, och välj sedan **registrera dig nu**.

    ![Kör användarflödet](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Ange en giltig e-postadress, klickar du på **skicka verifieringskoden**, ange verifieringskoden som du får och välj sedan **verifiera kod**.
1. Ange ett nytt lösenord och bekräfta lösenordet.
1. Välj land och region, anger du namnet som ska visas, ange ett postnummer och klicka sedan på **skapa**. Token returneras till `https://jwt.ms` och ska visas för dig.
1. Du kan nu köra användarflödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller anspråk som du har valt land/region, namn och postnummer.

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna kan redigera sin profil i ditt program, kan du använda en profilredigering användarflödet.

1. I den vänstra menyn på översiktssidan för Azure AD B2C-klient väljer **användarflöden (principer)** , och välj sedan **nytt användarflöde**.
1. Välj den **profilredigering** användarflödet på fliken rekommenderas.
1. Ange ett **Namn** för användarflödet. Till exempel *profileediting1*.
1. För **identitetsprovidrar**väljer **inloggning från lokalt konto**.
1. För **användarattribut**, Välj de attribut som du vill att kunden ska kunna redigera i sina profiler. Välj exempelvis **visa fler**, och välj sedan både attribut och anspråk för **visningsnamn** och **befattning**. Klicka på **OK**.
1. Klicka på **skapa** att lägga till användarflödet. Prefixet *B2C_1* automatiskt läggas till i namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. Välj användarflödet som du skapade för att öppna dess översiktssidan och välj sedan **kör användarflödet**.
1. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
1. Klicka på **kör användarflödet**, och logga sedan in med det konto som du skapade tidigare.
1. Nu har du möjlighet att ändra Visa namn och befattning för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill aktivera appanvändarna att återställa sina lösenord, kan du använda ett användarflöde för återställning av lösenord.

1. I den vänstra menyn väljer du **användarflöden (principer)** , och välj sedan **nytt användarflöde**.
1. Välj den **lösenordsåterställning** användarflödet på fliken rekommenderas.
1. Ange ett **Namn** för användarflödet. Till exempel *passwordreset1*.
1. För **identitetsprovidrar**, aktivera **Återställ lösenord med e-postadress**.
1. Under Programanspråk, klickar du på **visa fler** och välj de anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet. Välj till exempel **Användarobjekt-id**.
1. Klicka på **OK**.
1. Klicka på **skapa** att lägga till användarflödet. Prefixet *B2C_1* automatiskt läggas till i namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. Välj användarflödet som du skapade för att öppna dess översiktssidan och välj sedan **kör användarflödet**.
1. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
1. Klicka på **kör användarflödet**, kontrollera e-postadressen för det konto som du tidigare har skapat och välj **Fortsätt**.
1. Nu har du möjlighet att ändra lösenordet för användaren. Ändra lösenordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa ett användarflöde för registrering och logga in
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Lärt dig hur du lägger till identitetsleverantörer dina program att aktivera användaren logga in med leverantörer som Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft eller Twitter.

> [!div class="nextstepaction"]
> [Lägg till Identitetsproviders i dina program >](tutorial-add-identity-providers.md)