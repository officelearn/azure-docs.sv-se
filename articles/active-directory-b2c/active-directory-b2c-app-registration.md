<properties
    pageTitle="Azure Active Directory B2C Preview: Programregistrering | Microsoft Azure"
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
    ms.date="05/16/2016"
    ms.author="swkrish"/>


# Azure Active Directory B2C Preview: Registrera ditt program

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Krav

Om du vill skapa ett program som accepterar registrering och inloggning av konsumenter måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). När du har följt alla steg i artikeln är B2C-funktionsbladet fäst på startsidan.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Gå till B2C-funktionsbladet

Du kan gå till B2C-funktionsbladet från Azure-portalen eller den klassiska Azure-portalen.

### 1. Åtkomst på Azure-portalen

Om B2C-funktionsbladet är fäst på startsidan visas bladet så fort du loggar in på [Azure-portalen](https://portal.azure.com/) som global administratör för B2C-klienten.

Du kan också öppna bladet genom att klicka på **Bläddra** och sedan på **Azure AD B2C** i det vänstra navigeringsfönstret på [Azure-portalen](https://portal.azure.com/).

Du kan också komma åt bladet direkt genom att gå till [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) där **{klient}** ska ersättas med det namn som användes när klienten skapades (till exempel contosob2c). Du kan spara den här länken som ett bokmärke för framtida användning.

    > [AZURE.IMPORTANT]
    Du måste vara global administratör för B2C-klienten för att kunna komma åt B2C-funktionsbladet. En global administratör från en annan klient eller en användare från en klient kan inte komma åt det.

### 2. Åtkomst till den klassiska Azure-portalen

Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) som administratör för prenumerationen. (Det här är samma arbets- eller skolkonto eller samma Microsoft-konto som du använde när du registrerade dig för Azure.) Gå till Active Directory-tillägget till vänster och klicka på din B2C-klient. På fliken **Snabbstart** (den första fliken som öppnas) klickar du på **Hantera B2C-inställningar** under **Administrera**. När du gör det öppnas B2C-funktionsbladet i ett nytt webbläsarfönster eller på en ny flik.

Du hittar även länken **Hantera B2C inställningar** i avsnittet **B2C-administration** på fliken **Konfigurera**.

## Registrera ett program

1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
2. Klicka på **+Lägg till** överst på bladet.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-app”.
4. Om du skriver ett webbaserat program ändrar du växlingsknappen **Ta med webbapp/webb-API**till **Ja**. **Svars-URL:erna** är slutpunkter där Azure AD B2C returnerar de token som ditt program begär. Ange till exempel `https://localhost:44321/`. Om ditt program innehåller en komponent på serversidan (API) som måste skyddas skapar du (och kopierar) även en **programhemlighet** genom att klicka på knappen **Generera nyckel**.

    > [AZURE.NOTE]
En     **programhemlighet** är en viktig säkerhetsuppgift för autentisering.

5. Om du skriver ett mobilt program ändrar du växlingsknappen **Ta med intern klient** till **Ja**. Kopiera den fördefinierade **omdirigerings-URI:n** som skapas automatiskt åt dig.
6. Klicka på **Skapa** för att registrera ditt program.
7. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.

## Skapa ett snabbstartsprogram

Nu när du har registrerat ett program med Azure AD B2C kan du gå en av våra snabbstartsguider för att komma igång. Här är några rekommendationer:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]



<!--HONumber=Jun16_HO2-->


