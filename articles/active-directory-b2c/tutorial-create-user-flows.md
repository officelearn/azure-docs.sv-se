---
title: Självstudie – skapa användarflöden - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du skapar användarflöden för dina program i Azure Active Directory B2C med Azure portal.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e12b3cbcb8f7a433b37c450c84bd34745f68a22d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711520"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Självstudier: Skapa användarflöden i Azure Active Directory B2C

I dina program och du kan ha [användarflöden](active-directory-b2c-reference-policies.md) som användarna kan registrera, logga in eller hanterar sina profiler. Du kan skapa flera användarflöden av olika typer i Azure Active Directory (Azure AD) B2C-klienten och använder dem i dina program efter behov. Användarflöden kan återanvändas i program.

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
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.

    ![Växla till prenumerationskatalogen](./media/tutorial-create-user-flows/switch-directories.png)

3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. I den vänstra menyn väljer du **användarflöden**, och välj sedan **nytt användarflöde**.

    ![Välj nytt användarflöde](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Välj den **registrering och inloggning** användarflödet på fliken rekommenderas.

    ![Välj användarflödet som registrera dig och logga in](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
7. För **identitetsprovidrar**väljer **e-postregistrering**.

    ![Ange egenskaper för flow](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. För **användarattribut och anspråk**, Välj anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj exempelvis **visa fler**, och välj sedan **Land/Region**, **visningsnamn**, och **postnummer**. Klicka på **OK**.

    ![Välj de attribut och anspråk](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Klicka på **skapa** att lägga till användarflödet. Prefixet *B2C_1* automatiskt läggas till i namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. På sidan översikt av användarflöde som du har skapat väljer **kör användarflödet**.
2. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **kör användarflödet**, och välj sedan **registrera dig nu**.

    ![Kör användarflödet](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Ange en giltig e-postadress, klickar du på **skicka verifieringskoden**, och sedan ange verifieringskoden som visas.
5. Ange ett nytt lösenord och bekräfta lösenordet.
6. Ange namnet som ska visas, Välj land och region, ange ett postnummer och klicka sedan på **skapa**. Token returneras till `https://jwt.ms` och ska visas för dig.
7. Du kan nu köra användarflödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller anspråk som du har markerat namn, land och postnummer.

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna kan redigera sin profil i ditt program, kan du använda en profilredigering användarflödet.

1. I den vänstra menyn väljer du **användarflöden**, och välj sedan **nytt användarflöde**.
2. Välj den **profilredigering** användarflödet på fliken rekommenderas.
3. Ange ett **Namn** för användarflödet. Till exempel *profileediting1*.
4. För **identitetsprovidrar**väljer **inloggning från lokalt konto**.
5. För **användarattribut**, Välj de attribut som du vill att kunden ska kunna redigera i sina profiler. Välj exempelvis **visa fler**, och välj sedan **visningsnamn** och **befattning**. Klicka på **OK**.
6. Klicka på **skapa** att lägga till användarflödet. Prefixet *B2C_1* automatiskt läggas till i namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. På sidan översikt av användarflöde som du har skapat väljer **kör användarflödet**.
2. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **kör användarflödet**, och logga sedan in med det konto som du skapade tidigare.
4. Nu har du möjlighet att ändra Visa namn och befattning för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Det är möjligt om du vill aktivera användare av ditt program för att återställa sina lösenord om det behövs. Om du vill aktivera återställning av lösenord, kan du använda ett användarflöde för återställning av lösenord.

1. I den vänstra menyn väljer du **användarflöden**, och välj sedan **nytt användarflöde**.
2. Välj den **lösenordsåterställning** användarflödet på fliken rekommenderas.
3. Ange ett **Namn** för användarflödet. Till exempel *passwordreset1*.
4. För **identitetsprovidrar**, aktivera **Återställ lösenord med e-postadress**.
5. Under Programanspråk, klickar du på **visa fler** och välj de anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet. Välj till exempel **Användarobjekt-id**.
6. Klicka på **OK**.
7. Klicka på **skapa** att lägga till användarflödet. Prefixet *B2C_1* automatiskt läggas till i namnet.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. På sidan översikt av användarflöde som du har skapat väljer **kör användarflödet**.
2. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **kör användarflödet**, och logga sedan in med det konto som du skapade tidigare.
4. Nu har du möjlighet att ändra lösenordet för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa ett användarflöde för registrering och logga in
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

> [!div class="nextstepaction"]
> [Lägg till Identitetsproviders i dina program i Azure Active Directory B2C](tutorial-add-identity-providers.md)