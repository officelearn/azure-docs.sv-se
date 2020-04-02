---
title: Skapa ett Azure AD-program i Azure Data Explorer
description: Lär dig hur du skapar ett Azure AD-program i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550518"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Skapa en Azure Active Directory-programregistrering i Azure Data Explorer

Azure Active Directory (Azure AD) programautentisering används för program, till exempel en obevakad tjänst eller ett schemalagt flöde, som behöver komma åt Azure Data Explorer utan en användare närvarande. Om du ansluter till en Azure Data Explorer-databas med ett program, till exempel en webbapp, bör du autentisera med hjälp av tjänstens huvudautentisering. I den här artikeln beskrivs hur du skapar och registrerar ett Azure AD-tjänsthuvudnamn och sedan auktoriserar det för att komma åt en Azure Data Explorer-databas.

## <a name="create-azure-ad-application-registration"></a>Skapa registrering av Azure AD-program

Azure AD-programautentisering kräver att du skapar och registrerar ett program med Azure AD. Ett tjänsthuvudnamn skapas automatiskt när programregistreringen skapas i en Azure AD-klientorganisation. 

1. Logga in på [Azure-portalen](https://portal.azure.com) och öppna `Azure Active Directory` bladet

    ![Välj Azure Active Directory på portalmenyn](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Välj **bladet Appregistreringar** och välj **Ny registrering**

    ![Starta en ny appregistrering](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Fyll i följande information: 

    * **Namn** 
    * **Kontotyper som stöds**
    * **Omdirigera URI-webb** > **Web**
        > [!IMPORTANT] 
        > Programtypen ska vara **webb**. URI är valfritt och lämnas tomt i det här fallet.
    * Välj **registrera**

    ![Registrera ny appregistrering](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Välj **bladet Översikt** och kopiera **program-ID: et**.

    > [!NOTE]
    > Du behöver program-ID:et för att auktorisera tjänstens huvudnamn för att komma åt databasen.

    ![Kopiera registrerings-ID för appar](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. I **bladet Certifikat & hemligheter** väljer du **Ny klienthemlighet**

    ![Börja skapa klienthemlighet](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > I den här artikeln beskrivs hur du använder en klienthemlighet för programmets autentiseringsuppgifter.  Du kan också använda ett X509-certifikat för att autentisera ditt program. Välj **Ladda upp certifikat** och följ instruktionerna för att ladda upp den offentliga delen av certifikatet.

1. Ange en beskrivning, förfallodatum och välj **Lägg till**

    ![Ange klienthemliga parametrar](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Kopiera nyckelvärdet.

    > [!NOTE]
    > När du lämnar den här sidan är nyckelvärdet inte tillgängligt.  Du behöver nyckeln för att konfigurera klientautentiseringsuppgifter till databasen.

    ![Kopiera klienthemlignyckelvärde](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Ditt program skapas. Om du bara behöver åtkomst till en auktoriserad Azure Data Explorer-resurs, till exempel i det programmatiska exemplet nedan, hoppar du över nästa avsnitt. Stöd för delegerade behörigheter finns i [konfigurerade delegerade behörigheter för programregistreringen](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Konfigurera delegerade behörigheter för programregistreringen

Om ditt program behöver komma åt Azure Data Explorer med hjälp av autentiseringsuppgifterna för den anropande användaren konfigurerar du delegerade behörigheter för programregistreringen. Om du till exempel skapar ett webb-API för att komma åt Azure Data Explorer och vill autentisera med hjälp av autentiseringsuppgifterna för den användare som *anropar* ditt API.  

1. I **api-behörighetsbladet** väljer du **Lägg till en behörighet**.
1. Välj **API:er som min organisation använder**. Sök efter och välj **Azure Data Explorer**.

    ![Lägga till API-behörighet för Azure Data Explorer](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. Markera rutan **user_impersonation** och **Lägg till behörigheter** i **Delegerade behörigheter**

    ![Välj delegerade behörigheter med användarens personifiering](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Bevilja tjänstens huvudåtkomst till en Azure Data Explorer-databas

Nu när registreringen av tjänstens huvudprogram har skapats måste du bevilja motsvarande tjänsthuvudnamnsåtkomst till din Azure Data Explorer-databas. 

1. Anslut till databasen i [webbgränssnittet](https://dataexplorer.azure.com/)och öppna en frågeflik.

1. Kör följande kommando:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Ett exempel:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Den sista parametern är en sträng som visas som anteckningar när du frågar de roller som är associerade med en databas.
    
    > [!NOTE]
    > När du har skapat programregistreringen kan det ta flera minuter innan Azure Data Explorer kan referera till den. Om du får ett felmeddelande om att programmet inte hittas när du kör kommandot väntar du och försöker igen.

Mer information finns [i behörigheter](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)för hantering av [säkerhetsroller](/azure/kusto/management/security-roles) och inmatning .  

## <a name="using-application-credentials-to-access-a-database"></a>Använda programautentiseringsuppgifter för att komma åt en databas

Använd programautentiseringsuppgifterna för att programmässigt komma åt databasen med hjälp av [Azure Data Explorer-klientbiblioteket](/azure/kusto/api/netfx/about-kusto-data.md).

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Ange program-ID och nyckeln för programregistreringen (tjänstens huvudnamn) som skapats tidigare.

Mer information finns [i autentisera med Azure AD för Azure Data Explorer-åtkomst](/azure/kusto/management/access-control/how-to-authenticate-with-aad) och [använda Azure Key Vault med .NET Core-webbapp](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Felsökning

### <a name="invalid-resource-error"></a>Ogiltigt resursfel

Om ditt program används för att autentisera användare eller program för Azure Data Explorer-åtkomst måste du ställa in delegerade behörigheter för Azure Data Explorer-tjänstprogram. Deklarera ditt program kan autentisera användare eller program för Azure Data Explorer-åtkomst. Om du inte gör det uppstår ett fel som liknar följande när ett autentiseringsförsök görs:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Du måste följa instruktionerna för att [konfigurera delegerade behörigheter för Azure Data Explorer-tjänstprogram](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Aktivera fel för användarens medgivande

Din Azure AD-klientadministratör kan anta en princip som förhindrar klientanvändare från att ge samtycke till program. Den här situationen resulterar i ett fel som liknar följande när en användare försöker logga in på ditt program:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Du måste kontakta din Azure AD-administratör för att bevilja samtycke för alla användare i klienten eller aktivera användarmedgivande för ditt specifika program.

## <a name="next-steps"></a>Nästa steg

* Se [Kusto-anslutningssträngar](/azure/kusto/api/connection-strings/kusto.md) för lista över anslutningssträngar som stöds.
