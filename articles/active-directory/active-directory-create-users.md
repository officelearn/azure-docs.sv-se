<properties
    pageTitle="Lägga till nya användare i Azure Active Directory | Microsoft Azure"
    description="Beskriver hur du lägger till nya användare eller ändrar användarinformation i Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>


# Lägga till nya användare eller användare med Microsoft-konton i Azure Active Directory

Lägg till användare i din katalog. Den här artikeln förklarar hur du lägger till nya användare i din organisation och hur du lägger till användare som har Microsoft-konton. Mer information om hur du lägger till användare från andra kataloger i Azure Active Directory eller hur du lägger till användare från partnerföretag finns i [Lägga till användare från andra kataloger eller partnerföretag i Azure Active Directory](active-directory-create-users-external.md). Tillagda användare har inte administratörsbehörighet som standard, men du kan tilldela roller till dem när som helst.

## Lägga till en användare

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Active Directory** och välj sedan namnet på din organisationskatalog.
3. Välj fliken **Användare** och sedan **Lägg till användare** i kommandofältet.
4. På sidan **Berätta om den här användaren** under **Typ av användare** väljer du antingen:

    - **Ny användare i din organisation** – Lägger till ett nytt konto till din katalog.
    - **Användare med ett befintligt Microsoft-konto** – Lägger till ett befintligt Microsoft-konsumentkonto till din katalog (till exempel ett Outlook-konto)

5. Beroende på **typen av användare** anger du ett användarnamn (för en ny användare) eller en e-postadress (för en användare med ett Microsoft-konto).
6. På användarens **profilsida** anger du ett för- och efternamn, ett användarvänligt namn och en användarroll från listan **Roller**. Mer information om användar- och administratörsroller finns i [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles.md). Ange om du vill **aktivera Multi-Factor Authentication** för användaren.
7. På sidan **Skaffa tillfälligt lösenord** väljer du **Skapa**.

> [AZURE.IMPORTANT] Om din organisation använder mer än en domän bör du vara medveten om följande problem när du lägger till ett användarkonto:
>
> - Om du vill lägga till användarkonton med samma UPN (användarens huvudnamn) i olika domäner lägger du **först** till exempelvis geoffgrisso@contoso.onmicrosoft.com, **följt av** geoffgrisso@contoso.com.
> - Lägg **inte** till geoffgrisso@contoso.com innan du lägger till geoffgrisso@contoso.onmicrosoft.com. Den här ordningen är viktig och kan vara krånglig att ångra.

## Ändra användarinformation

Du kan ändra valfria användarattribut utom objekt-ID:t.

1. Öppna din katalog.
2. Välj fliken **Användare** och välj sedan visningsnamnet för den användare som du vill ändra.
3. Slutför ändringarna och klicka sedan på **Spara**.

Om användaren som du ändrar synkroniseras med din lokala Active Directory-tjänst kan du inte ändra användarinformationen med den här proceduren. Om du vill ändra användaren använder du dina lokala Active Directory-hanteringsverktyg.

## Hantering av och begränsningar för gästanvändare

Gästkonton är användare från andra kataloger som bjudits in till din katalog för att få åtkomst till SharePoint-dokument, program eller andra Azure-resurser. Det underliggande UserType-attributet för ett gästkonto i din katalog är ”Gäst”. Vanliga användare (särskilt medlemmar i din katalog) har UserType-attributet ”Medlem”.

Gäster har en begränsad uppsättning rättigheter i katalogen. Dessa rättigheter begränsar möjligheten för gäster att identifiera information om andra användare i katalogen. Gästanvändare kan dock fortfarande interagera med användare och grupper som är associerade med de resurser som de arbetar med. Gästanvändare kan:

- Visa andra användare och grupper som är associerade med en Azure-prenumeration som de tilldelats.
- Visa medlemmarna i grupper som de tillhör.
- Leta upp andra användare i katalogen om de känner till användarens fullständiga e-postadress.
- Visa en begränsad uppsättning attribut för de användare som de letar upp, begränsat till visningsnamn, e-postadress, användarens huvudnamn (UPN) och miniatyrfoto.
- Hämta en lista över verifierade domäner i katalogen.
- Samtycka till program som ger dem samma åtkomst som medlemmar har i din katalog.

## Ange åtkomstprinciper för gästanvändare

Fliken **Konfigurera** för en katalog innehåller alternativ som du kan använda för att kontrollera åtkomsten för gästanvändare. Dessa alternativ kan bara ändras på den klassiska Azure-portalen av en global katalogadministratör. För närvarande finns det ingen PowerShell- eller API-metod.

Öppna fliken **Konfigurera** på den klassiska Azure-portalen genom att välja **Active Directory** och sedan namnet på katalogen.

![Konfigurera fliken i Azure Active Directory][1]

Sedan kan du redigera alternativen för att kontrollera gästanvändarnas åtkomst.

![Alternativ för åtkomstkontroll för gästanvändare][2]


## Nästa steg

- [Lägga till användare från andra kataloger eller partnerföretag i Azure Active Directory](active-directory-create-users-external.md)
- [Administrera Azure AD](active-directory-administer.md)
- [Hantera lösenord i Azure AD](active-directory-manage-passwords.md)
- [Hantera grupper i Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png



<!--HONumber=Sep16_HO3-->


