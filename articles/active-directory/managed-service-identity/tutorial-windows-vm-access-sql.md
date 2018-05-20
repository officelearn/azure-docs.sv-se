---
title: Använda en Windows VM-MSI för åtkomst till Azure SQL
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows VM hanterade tjänsten identitet (MSI) för att få åtkomst till Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 27c4f35cbd8cc69e689b26078d5a07c8558d8e2a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Självstudier: Använda en Windows VM hanterade tjänsten identitet (MSI) för att komma åt Azure SQL

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här kursen visar hur du använder en hanterad tjänst identitet (MSI) för en Windows-dator (VM) åtkomst till en Azure SQL-servern. Hanterade Tjänsteidentiteter hanteras automatiskt av Azure och gör att du kan autentisera tjänster som stöder Azure AD-autentisering utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en Windows VM 
> * Ge dina VM-åtkomst till en Azure SQL-server
> * Hämta en åtkomst-token med VM-identitet och använda den för att fråga en Azure SQL-server

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Windows virtuell dator.  Du kan också aktivera MSI på en befintlig virtuell dator.

1.  Klicka på knappen **Skapa en resurs** längst upp till vänster i Azure Portal.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgruppen** som du vill skapa den virtuella datorn, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Behåll standardinställningarna på sidan Inställningar och klickar på **OK**.

    ![ALT bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En VM MSI kan du få åtkomst-token från Azure AD utan att du behöver publicera autentiseringsuppgifter i koden. Aktivera MSI visar Azure för att skapa en hanterad identitet för den virtuella datorn. Under försättsbladen, aktivera MSI gör två saker: registrerar den virtuella datorn med Azure Active Directory för att skapa hanterade identitet och konfigurerar identiteten på den virtuella datorn.

1.  Välj den **virtuella** som du vill aktivera MSI på.  
2.  Klicka på det vänstra navigeringsfältet **Configuration**. 
3.  Du ser **hanterade tjänstidentiteten**. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4.  Se till att du klickar på **spara** att spara konfigurationen.  
    ![ALT bildtext](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Ge din VM-åtkomst till en databas i en Azure SQL-server

Nu kan du ge din VM-åtkomst till en databas i en Azure SQL-server.  Du kan använda en befintlig SQLServer eller skapa en ny för det här steget.  Om du vill skapa en ny server och databas med Azure-portalen, följer du detta [SQL Azure quickstart](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Det finns också Snabbstart som använder Azure CLI och Azure PowerShell i den [dokumentationen till SQL Azure](https://docs.microsoft.com/azure/sql-database/).

Det finns tre steg för att ge dina VM-åtkomst till en databas:
1.  Skapa en grupp i Azure AD och göra VM MSI medlem i gruppen.
2.  Aktivera Azure AD-autentisering för SQLServer.
3.  Skapa en **innesluten användare** i databasen som representerar Azure AD-grupp.

> [!NOTE]
> Normalt skapar du en innesluten användare som mappar direkt till den virtuella datorn MSI.  Azure SQL tillåter för närvarande inte Azure AD tjänstens huvudnamn som representerar VM MSI mappas till en innesluten användare.  Som en lösning som stöds du gör VM MSI medlem av en Azure AD-grupp och sedan skapa en innesluten användare i databasen som representerar gruppen.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Skapa en grupp i Azure AD och göra VM MSI medlem i gruppen

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.  

Installera först den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Logga sedan in med `Connect-AzureAD`, och kör följande kommando för att skapa gruppen och spara det i en variabel:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Det ser ut som följande, vilket också undersöker värdet för variabeln utdata:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Lägg sedan till den virtuella datorn MSI i gruppen.  Du måste MSI- **ObjectId**, som du kan hämta med hjälp av Azure PowerShell.  Hämta först [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Logga sedan in med `Connect-AzureRmAccount`, och kör följande kommandon:
- Kontrollera din sessionskontexten är den önskade Azure-prenumerationen om du har flera.
- Visa en lista med de tillgängliga resurserna i din Azure-prenumeration, kontrollera i rätt resursgrupp och VM-namn.
- Hämta VM MSI-egenskaper, med lämpliga värden för `<RESOURCE-GROUP>` och `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Det ser ut som följande, vilket också undersöker service principal objekt-ID för den virtuella datorn MSI utdata:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Lägga till VM MSI i gruppen.  Du kan bara lägga till ett huvudnamn för tjänsten i en grupp med Azure AD PowerShell.  Kör följande kommando:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Om du även undersöka gruppmedlemskap efteråt, ut utdata på följande sätt:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Aktivera Azure AD-autentisering för SQLServer

Nu när du har skapat gruppen och lägga till VM MSI medlemskap, kan du [konfigurera Azure AD-autentisering för SQLServer](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) med följande steg:

1.  Välj i Azure-portalen **SQL-servrar** från det vänstra navigeringsfönstret.
2.  Klicka på SQLServer måste vara aktiverat för Azure AD-autentisering.
3.  I den **inställningar** avsnitt i bladet, klickar du på **Active Directory-administratör**.
4.  I kommandofältet klickar du på **ange admin**.
5.  Markera ett användarkonto i Azure AD att göras en administratör på servern och klicka på **Välj.**
6.  I kommandofältet klickar du på **spara.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Skapa en innesluten användare i databasen som representerar Azure AD-grupp

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Innan du börjar kan det också vara bra att granska följande artiklar för bakgrunden på Azure AD-integrering:

- [Universal autentisering med SQL Database och SQL Data Warehouse (SSMS stöd för MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  Starta SQL Server Management Studio.
2.  I den **Anslut till Server** dialogrutan, ange din SQLServer-namnet i den **servernamn** fältet.
3.  I den **autentisering** väljer **Active Directory - Universal med stöd för MFA**.
4.  I den **användarnamn** , ange namnet på Azure AD-kontot som du anger som serveradministratören, t.ex. helen@woodgroveonline.com
5.  Klicka på **Alternativ**.
6.  I den **Anslut till databas** , ange namnet på databasen för icke-system som du vill konfigurera.
7.  Klicka på **Anslut**.  Slutföra inloggningen.
8.  I den **Object Explorer**, expandera den **databaser** mapp.
9.  Högerklicka på en användardatabas och på **ny fråga**.
10.  Ange följande rad i frågefönstret och klicka på **Execute** i verktygsfältet:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Kommandot ska slutföras, skapa innesluten användare för gruppen.
11.  Rensa frågefönstret, ange följande rad och på **Execute** i verktygsfältet:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     Kommandot ska slutföras, bevilja innesluten användare möjlighet att läsa hela databasen.

Kod som körs i den virtuella datorn kan nu hämta en token från MSI och använda token för autentisering till SQLServer.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Hämta en åtkomst-token med VM-identitet och använda den för att anropa Azure SQL 

Azure SQL inbyggt stöd för Azure AD-autentisering, så den kan acceptera åtkomsttoken direkt hämtas med hjälp av MSI.  Du använder den **åtkomsttoken** metod för att skapa en anslutning till SQL.  Detta är en del av Azure SQL-integrering med Azure AD och skiljer sig från att tillhandahålla autentiseringsuppgifter i anslutningssträngen.

Här är ett .net-kodexempel för att öppna en anslutning till SQL med hjälp av en åtkomst-token.  Den här koden måste köras på den virtuella datorn för att kunna komma åt VM MSI-slutpunkten.  **.NET framework 4.6** eller högre krävs för att använda åtkomstmetoden-token.  Ersätt värdena för AZURE-SQL-servernamn och databasen i enlighet med detta.  Observera resurs-ID för Azure SQL är ”https://database.windows.net/”.

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
    // Call MSI endpoint.
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

Du kan också ett snabbt sätt att testa slutpunkt till slutpunkt-installationsprogrammet utan att behöva skriva och distribuera en app på den virtuella datorn med PowerShell.

1.  I portalen, går du till **virtuella datorer** och gå till din Windows-dator och i den **översikt**, klickar du på **Anslut**. 
2.  Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3.  Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn, öppna **PowerShell** i fjärrsessionen. 
4.  Med hjälp av Powershell's `Invoke-WebRequest`, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomst-token för Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Konvertera svaret från ett JSON-objekt till en PowerShell-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrahera den åtkomst-token från svaret.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Öppna en anslutning till SQLServer. Kom ihåg att ersätta värdena för AZURE-SQL-servernamn och databasen.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Sedan skapa och skicka en fråga till servern.  Kom ihåg att ersätta värdet för tabellen.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Kontrollera värdet på `$DataSet.Tables[0]` att visa resultatet av frågan.  Grattis, du har efterfrågas i databasen med en VM MSI och utan att behöva ange autentiseringsuppgifter!

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
- Lär dig mer om [Azure SQL-stöd för Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).
- Lär dig mer om [konfigurera Azure SQL-stöd för Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).
- Lär dig mer om [autentisering och i SQLServer](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
