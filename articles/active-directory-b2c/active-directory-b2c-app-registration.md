<properties
    pageTitle="Azure Active Directory B2C: Programregistrering | Microsoft Azure"
    description="Registrera ditt program med Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# Azure Active Directory B2C: Registrera ditt program

## Krav

Om du vill skapa ett program som accepterar registrering och inloggning av konsumenter måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). När du har följt alla steg i artikeln är B2C-funktionsbladet fäst på startsidan.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Gå till B2C-funktionsbladet

Om B2C-funktionsbladet är fäst på startsidan visas bladet så fort du loggar in på [Azure-portalen](https://portal.azure.com/) som global administratör för B2C-klienten.

Du kan också öppna bladet genom att klicka på **Bläddra** och sedan på **Azure AD B2C** i det vänstra navigeringsfönstret på [Azure-portalen](https://portal.azure.com/).

> [AZURE.IMPORTANT] Du måste vara global administratör för B2C-klienten för att kunna komma åt B2C-funktionsbladet. En global administratör från en annan klient eller en användare från en klient kan inte komma åt det.  Du kan växla till din B2C-klient med klientväxlaren i det övre högra hörnet i Azure-portalen.

## Registrera ett program

1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
2. Klicka på **+Lägg till** överst på bladet.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-app”.
4. Om du skriver ett webbaserat program ändrar du växlingsknappen **Ta med webbapp/webb-API**till **Ja**. **Svars-URL:erna** är slutpunkter där Azure AD B2C returnerar de token som ditt program begär. Ange till exempel `https://localhost:44321/`. Om ditt webbprogram också kommer att anropa vissa webb-API som skyddas av Azure AD B2C ska du också skapa en **programhemlighet** genom att klicka på knappen **Generera nyckel**.

    > [AZURE.NOTE] En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.

5. Om du skriver ett mobilt program ändrar du växlingsknappen **Ta med intern klient** till **Ja**. Kopiera den fördefinierade **omdirigerings-URI:n** som skapas automatiskt åt dig.
6. Klicka på **Skapa** för att registrera ditt program.
7. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.

> [AZURE.IMPORTANT] Program som har skapats på B2C-funktionsbladet måste hanteras på samma plats. Om du redigerar B2C-program med hjälp av PowerShell eller en annan portal stöds de inte och kommer troligen inte att fungera med Azure AD B2C.

## Skapa ett snabbstartsprogram

Nu när du har registrerat ett program med Azure AD B2C kan du gå en av våra snabbstartsguider för att komma igång. Här är några rekommendationer:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]



<!--HONumber=sep16_HO1-->


