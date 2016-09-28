<properties
    pageTitle="Skaffa en Azure AD-klient | Microsoft Azure"
    description="Hur du skaffar en Azure Active Directory-klient för registrering och utveckling av program."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>


# Skaffa en Azure Active Directory-klient

I Azure Active Directory (Azure AD) representerar en [klient](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) en organisation.  Det är en dedikerad instans av Azure AD-tjänsten som en organisation tilldelas och äger när den registrerar sig för en Microsoft-molntjänst som Azure, Microsoft Intune och Office 365.  Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter.  

En klient inrymmer användarna på ett företag och informationen om dem – deras lösenord, användarprofildata, behörigheter och så vidare.  Den innehåller också grupper, program och annan information som hör till en organisation och dess säkerhet.

Om du vill tillåta att Azure AD-användare loggar in till ditt program måste du registrera programmet i en klient som du äger.  Det är **helt kostnadsfritt** att publicera ett program i en Azure AD-klient.  De flesta utvecklare skapar i själva verket flera klienter och program för experimentering, utveckling, mellanlagring och testning.  Organisationer som registrera sig för och använder ditt program kan välja att köpa licenser om de vill utnyttja avancerade katalogfunktioner.

Så, hur skaffar du en Azure AD-klient?  Hur processen ser ut beror delvis på om:

- [du har en befintlig Office 365-prenumeration](#use-an-existing-office-365-subscription)
- [du har en befintlig Azure-prenumeration som är associerad med ett Microsoft-konto](#use-an-msa-azure-subscription)
- [du har en befintlig Azure-prenumeration som är associerad med ett organisationskonto](#use-an-organizational-azure-subscription)
- [du inte har något av ovanstående och vill börja från början.](#start-from-scratch)

## Använda en befintlig Office 365-prenumeration
Om du har en befintlig prenumeration på Office 365, men inte har någon Azure-prenumeration (och inte kan logga in på [Azure-hanteringsportalen](https://manage.windowsazure.com)) följer du [dessa anvisningar](https://technet.microsoft.com/library/dn832618.aspx) för att få tillgång till din Azure AD-klient.

## Använda en MSA Azure-prenumeration
Om du redan har registrerat dig för en Azure-prenumeration med ditt individuella Microsoft-konto har du redan en klient!  På [Azure-hanteringsportalen](https://manage.windowsazure.com) letar du upp en klient med namnet ”Standardinnehavare” eller ”Standardklient” som visas under ”Alla objekt” och ”Active Directory”.  Du kan använda den här innehavaren om det är lämpligt, men du kanske vill skapa ett administratörskonto för din organisation.

I så fall följer du dessa steg.  Alternativt kanske du vill skapa en ny klient och skapa en administratör i den klienten med hjälp av en liknande process.

1.  Logga in på [Azure-hanteringsportalen](https://manage.windowsazure.com) med ditt individuella konto
2.  Gå till avsnittet ”Active Directory” på portalen (i det vänstra navigeringsfältet)
3.  Välj posten ”Standardkatalog” i listan över tillgängliga kataloger
4.  Klicka på länken Användare överst på sidan.  Du ser en enstaka användare i listan med värdet ”Microsoft-konto” i kolumnen Ursprung
5.  Klicka på ”Lägg till användare” längst ned på sidan
6.  Ange följande information i formuläret Lägg till användare:
    - Typ av användare: Ny användare i din organisation
    - Användarnamn: (välj ett användarnamn för den här administratören)
    - Förnamn/Efternamn/Visningsnamn: (välj lämpliga värden)
    - Roll: Global administratör
    - Alternativ e-postadress: (ange lämpliga värden)
    - Valfritt: Aktivera Multi-Factor Authentication
    - Avsluta med att klicka på den gröna knappen ”Skapa” för att slutföra användargenereringen (och visa det tillfälliga lösenordet).
7.  När du har fyllt i formuläret Lägg till användare och fått det tillfälliga lösenordet för den nya administrativa användaren är det viktigt att du kommer ihåg lösenordet eftersom du måste logga in med den nya användaren för att kunna ändra lösenordet. Du kan också skicka lösenordet direkt till användaren med hjälp av en alternativ e-postadress.
8.  Om du vill ändra det tillfälliga lösenordet loggar du in på https://login.microsoftonline.com med det nya användarkontot och ändrar lösenordet när du uppmanas att göra det.


## Använda en Azure-prenumeration för en organisation
Om du redan har registrerat dig för en Azure-prenumeration med ditt organisationskonto har du redan en klient.  På [Azure-hanteringsportalen](https://manage.windowsazure.com) bör du hitta en klient under ”Alla objekt” och ”Active Directory”.  Du kan använda den här klienten om du vill.  Du kan också skapa en ny klient med hjälp av knappen ”Ny” i det nedre vänstra hörnet på portalen.


## Börja från början
Oroa dig inte om inget av alternativen ovan passar dig.  Gå bara till [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) och registrera dig för Azure med en ny organisation.  När du har slutfört processen har du din egen Azure AD-klient med det domännamn som du valde när du registrerade dig.  På [Azure-hanteringsportalen](https://manage.windowsazure.com) hittar du din klient genom att navigera till ”Active Directory” i det vänstra navigeringsfältet.

Som en del av registreringen i Azure måste du ange kreditkortsinformation.  Oroa dig inte: du debiteras inte när du publicerar program i Azure AD eller skapar nya klienter.



<!--HONumber=Sep16_HO3-->


