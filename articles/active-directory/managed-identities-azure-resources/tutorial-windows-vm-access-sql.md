---
title: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure SQL
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure SQL.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57905b3d3c062c299a0f414ae6110dd0b6249198
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848038"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig att komma åt en Azure SQL-server med en systemtilldelad identitet för en virtuell Windows-dator. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. Lär dig att:

> [!div class="checklist"]
> * Ge din virtuella dator åtkomst till en Azure SQL-server
> * Aktivera Azure AD-autentisering för SQL-servern
> * Skapa en innesluten användare i databasen som representerar den virtuella datorns systemtilldelade identitet
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använda den för att köra frågor mot en Azure SQL-server

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Ge din virtuella dator åtkomst till en databas i en Azure SQL-server

Du kan använda en befintlig SQL-server eller skapa en ny om du vill bevilja en databas i Azure SQL Server åtkomst till din virtuella dator. Om du vill skapa en ny server och en databas med hjälp av Azure-portalen följer du den här [Azure SQL-snabbstarten](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Det finns även snabbstarter som använder Azure CLI och Azure PowerShell i [Azure SQL-dokumentationen](https://docs.microsoft.com/azure/sql-database/).

Det finns två steg för att ge den virtuella datorn åtkomst till en databas:

1. Aktivera Azure AD-autentisering för SQL-servern.
2. Skapa en **innesluten användare** i databasen som representerar den virtuella datorns systemtilldelade identitet.

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Aktivera Azure AD-autentisering för SQL-servern

[Konfigurera Azure AD-autentisering för SQL Server](/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

1.  I Azure-portalen väljer du **SQL-servrar** från det vänstra navigeringsfältet.
2.  Klicka på den SQL server som ska aktiveras för Azure AD-autentisering.
3.  I avsnittet **Inställningar** avsnittet på bladet klickar du på **Active Directory-administratör**.
4.  I kommandofältet klickar du på **Konfigurera administratör**.
5.  Välj ett Azure AD-användarkonto som ska bli administratör för servern och klicka på **Välj**.
6.  I kommandofältet klickar du på **Spara**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>Skapa en innesluten användare i databasen som representerar den virtuella datorns systemtilldelade identitet

För nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Innan du börjar kan det också vara bra att granska följande artiklar för att få bakgrundsinformation om Azure AD-integrering:

- [Universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB kräver unika AAD-visningsnamn. Med detta måste AAD-konton, till exempel användare, grupper och tjänstens huvudnamn (program) och namn på virtuella datorer som aktiverats för hanterad identitet definieras unikt i AAD om sina visningsnamn. SQL DB kontrollerar AAD visningsnamn under T-SQL-skapandet av dessa användare och om det inte är unikt, misslyckas kommandot begär att ange ett unikt AAD-visningsnamn för en viss konto.

1. Starta SQL Server Management Studio.
2. I dialogrutan **Anslut till server** anger du SQL-servernamnet i fältet **Servernamn**.
3. I fältet **Autentisering** väljer du **Active Directory – Universell med stöd för MFA**.
4. I fältet **Användarnamn** anger du namnet på det Azure AD-konto som du anger som serveradministratör, till exempel helen@woodgroveonline.com
5. Klicka på **Alternativ**.
6. I fältet **Anslut till databas** anger du namnet på den icke-systembaserade databas som du vill konfigurera.
7. Klicka på **Anslut**. Slutför inloggningsprocessen.
8. I **Object Explorer** expanderar du mappen **Databaser**.
9. Högerklicka på en användardatabas och klicka på **Ny fråga**.
10. I frågefönstret anger du följande rad och klickar på **Kör** i verktygsfältet:

    > [!NOTE]
    > `VMName` i följande kommando är namnet på den virtuella datorn som du har aktiverat systemtilldelad identitet på i avsnittet om förutsättningar.
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    Kommandot bör slutföras utan problem och skapa den inneslutna användaren för den virtuella datorns systemtilldelade identitet.
11. Rensa frågefönstret, ange följande rad och klicka på **Kör** i verktygsfältet:

    > [!NOTE]
    > `VMName` i följande kommando är namnet på den virtuella datorn som du har aktiverat systemtilldelad identitet på i avsnittet om förutsättningar.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Kommandot bör slutföras utan problem och bevilja den inneslutna användaren möjligheten att läsa hela databasen.

Kod som körs i den virtuella datorn kan nu få en token från den systemtilldelade hanterade identiteten och använda token för att autentisera till SQL-servern.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns systemtilldelade hanterade identitet och använd den för att anropa Azure SQL

Azure SQL har inbyggt stöd för Azure AD-autentisering, vilket gör att åtkomsttoken som hämtas med hanterade identiteter för Azure-resurser kan accepteras direkt. Du använder metoden med **åtkomsttoken** för att skapa en anslutning till SQL. Detta är en del av integreringen av Azure SQL med Azure AD, och skiljer sig från att ange autentiseringsuppgifter i anslutningssträngen.

Här är ett .NET kodexempel för att öppna en anslutning till SQL med hjälp av en åtkomsttoken. Koden måste köras på den virtuella datorn om du vill komma åt slutpunkten för den virtuella datorns systemtilldelade hanterade identitet. **.NET framework 4.6** eller högre krävs för att använda åtkomstmetoden-token. Ersätt värdena för AZURE-SQL-SERVERNAME och DATABASE i enlighet med detta. Tänk på resurs-ID för Azure SQL är `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Du kan snabbt testa konfigurationen av slutpunkt till slutpunkt utan att behöva skriva och distribuera en app på den virtuella datorn med hjälp av PowerShell.

1.  I portalen går du till **Virtuella datorer** och sedan till den virtuella Windows-datorn. Under **Översikt** klickar du på **Anslut**.
2.  Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn.
3.  Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du **PowerShell** i fjärrsessionen.
4.  Använd PowerShells `Invoke-WebRequest` och skicka en begäran till den lokala hanterade identitetens slutpunkt för att hämta en åtkomsttoken för Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Konvertera svaret från ett JSON-objekt till ett PowerShell-objekt. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrahera åtkomsttoken från svaret.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. Öppna en anslutning till SQL-servern. Kom ihåg att ersätta värdena för AZURE-SQL-SERVERNAME och DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Sedan skapar du och skickar en fråga till servern. Kom ihåg att ersätta värdet för TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Kontrollera värdet på `$DataSet.Tables[0]` för att visa resultatet av frågan.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för att få åtkomst till Azure SQL Server. Läs mer om Azure SQL Server här:

> [!div class="nextstepaction"]
> [Azure SQL Database-tjänsten](/azure/sql-database/sql-database-technical-overview)
