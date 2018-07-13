---
title: Använd en Windows VM-MSI för att få åtkomst till Azure SQL
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c3e2af8c6864204a4c373ac4e1c12090389ac32
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007434"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Använd en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure SQL

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du använder en hanterad tjänstidentitet (MSI) för en Windows virtuell dator (VM) för att få åtkomst till en Azure SQL server. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en Windows VM 
> * Ge din VM-åtkomst till en Azure SQL server
> * Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att fråga en Azure SQL-server

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en Windows-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny virtuell Windows-dator.  Du kan också aktivera MSI på en befintlig virtuell dator.

1.  Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgrupp** där du kan skapa den virtuella datorn, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Behåll standardinställningarna på sidan Inställningar och klickar på **OK**.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En VM MSI kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. När du aktiverar MSI meddelar Azure att skapa en hanterad identitet för den virtuella datorn. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI i Azure Resource Manager.

1.  Välj den **VM** att du vill aktivera MSI på.  
2.  I det vänstra navigeringsfältet klickar du på **Configuration**. 
3.  Du ser **hanterad tjänstidentitet**. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4.  Se till att du klickar på **spara** att spara konfigurationen.  
    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera och vilka tillägg som finns på den här virtuella datorn klickar du på **tillägg**. Om MSI aktiveras sedan **ManagedIdentityExtensionforWindows** visas i listan.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Ge din VM-åtkomst till en databas i en Azure SQL server

Nu kan du ge din åtkomst till virtuell dator till en databas i en Azure SQL server.  Du kan använda en befintlig SQLServer eller skapa ett nytt lösenord för det här steget.  Följ den här för att skapa en ny server och databas med Azure portal, [Snabbstart för Azure SQL](~/articles/sql-database/sql-database-get-started-portal.md). Det finns även snabbstarter som använder Azure CLI och Azure PowerShell i den [dokumentation om Azure SQL](~/articles/sql-database/index.yml).

Det finns tre steg för att ge din åtkomst till virtuell dator till en databas:
1.  Skapa en grupp i Azure AD och göra VM MSI medlem i gruppen.
2.  Aktivera Azure AD-autentisering för SQLServer.
3.  Skapa en **innesluten användare** i databasen som representerar Azure AD-gruppen.

> [!NOTE]
> Normalt skapar du en innesluten användare som mappar direkt till den Virtuella datorns MSI.  Azure SQL tillåter för närvarande inte Azure AD-tjänstobjekt som representerar VM MSI mappas till en innesluten användare.  Som en lösning som stöds kan du göra VM MSI medlem i en Azure AD-grupp och sedan skapa en innesluten användare i databasen som representerar gruppen.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Skapa en grupp i Azure AD och göra VM MSI medlem i gruppen

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.  

Installera först den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulen. Logga sedan in med `Connect-AzureAD`, och kör följande kommando för att skapa gruppen och spara den i en variabel:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Utdata ser ut som följande, vilket också undersöker värdet för variabeln:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Lägg sedan till den Virtuella datorns MSI i gruppen.  Du behöver MSI **ObjectId**, som du kan få med Azure PowerShell.  Hämta först [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Logga sedan in med `Connect-AzureRmAccount`, och kör följande kommandon för att:
- Kontrollera din sessionskontexten är inställd på den önskade Azure-prenumerationen om du har flera.
- Lista över de tillgängliga resurserna i Azure-prenumerationen, verifiera i rätt resursgrupp och namn på virtuella datorer.
- Hämta MSI VM-egenskaper, med lämpliga värden för `<RESOURCE-GROUP>` och `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Utdata ser ut som följande, vilket också undersöker service principal objekt-ID för den Virtuella datorns MSI:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Lägga till VM MSI i gruppen.  Du kan bara lägga till ett huvudnamn för tjänsten till en grupp med Azure AD PowerShell.  Kör följande kommando:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Om du även undersöka gruppmedlemskapet när allt är klart, ser utdata ut så här:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Aktivera Azure AD-autentisering för SQLServer

Nu när du har skapat gruppen och har lagts till VM MSI i medlemskap, kan du [konfigurera Azure AD-autentisering för SQLServer](~/articles/sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) med följande steg:

1.  I Azure-portalen väljer du **SQL-servrar** från det vänstra navigeringsfältet.
2.  Klicka på den SQL servern som ska aktiveras för Azure AD-autentisering.
3.  I den **inställningar** avsnittet på bladet, klickar du på **Active Directory-administratör**.
4.  I kommandofältet klickar du på **konfigurera administratör**.
5.  Välj en Azure AD-användarkonto för att upprätta en administratör för servern och klickar på **Välj.**
6.  I kommandofältet klickar du på **spara.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Skapa en innesluten användare i databasen som representerar Azure AD-grupp

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Innan du börjar, kan det också vara bra att granska följande artiklar för bakgrundsinformation om Azure AD-integrering:

- [Universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](~/articles/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](~/articles/sql-database/sql-database-aad-authentication-configure.md)

1.  Starta SQL Server Management Studio.
2.  I den **Anslut till Server** dialogrutan, ange din SQLServer-namn i den **servernamn** fält.
3.  I den **autentisering** väljer **Active Directory - Universal med stöd för MFA**.
4.  I den **användarnamn** fältet, anger du namnet på Azure AD-kontot som du anger som serveradministratör exempelvis helen@woodgroveonline.com
5.  Klicka på **Alternativ**.
6.  I den **Anslut till databas** fältet, anger du namnet på den andra databasen som du vill konfigurera.
7.  Klicka på **Anslut**.  Slutför inloggningsprocessen.
8.  I den **Object Explorer**, expandera den **databaser** mapp.
9.  Högerklicka på en användardatabas och på **ny fråga**.
10.  I frågefönstret anger du följande rad och klicka på **kör** i verktygsfältet:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Kommandot bör slutföras utan problem, skapa inneslutna användare för gruppen.
11.  Rensa frågefönstret, ange följande rad och på **kör** i verktygsfältet:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     Kommandot bör slutföras utan problem, beviljar innesluten användare möjlighet att läsa hela databasen.

Kod som körs i den virtuella datorn kan nu få en token från MSI och använda token för att autentisera till SQLServer.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att anropa Azure SQL 

Azure SQL har inbyggt stöd för Azure AD-autentisering, så att den kan acceptera åtkomsttoken direkt hämtas med MSI.  Du använder den **åtkomsttoken** metod för att skapa en anslutning till SQL.  Detta är en del av Azure SQL-integrering med Azure AD och skiljer sig från att tillhandahålla autentiseringsuppgifter i anslutningssträngen.

Här är ett .net kodexempel för att öppna en anslutning till SQL med hjälp av en åtkomsttoken.  Den här koden måste köras på den virtuella datorn för att kunna komma åt VM MSI-slutpunkten.  **.NET framework 4.6** eller högre krävs för att använda åtkomstmetoden-token.  Ersätt värdena för AZURE-SQL-servernamn och databasen i enlighet med detta.  Tänk på resurs-ID för Azure SQL är ”https://database.windows.net/”.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
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

Du kan också ett snabbt sätt att testa från slutpunkt till slutpunkt-installationsprogrammet utan att behöva skriva och distribuera en app på den virtuella datorn med hjälp av PowerShell.

1.  I portalen navigerar du till **virtuella datorer** och gå till din Windows-dator och i den **översikt**, klickar du på **Connect**. 
2.  Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3.  Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn, öppna **PowerShell** i fjärrsessionen. 
4.  Med hjälp av PowerShell- `Invoke-WebRequest`, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Konvertera svaret från ett JSON-objekt till ett PowerShell-objekt. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrahera åtkomsttoken från svaret.
    
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

    Sedan skapar och skickar en fråga till servern.  Kom ihåg att ersätta värdet för tabellen.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Kontrollera värdet på `$DataSet.Tables[0]` att visa resultatet av frågan.  Grattis, du har frågat databasen med en VM-MSI och utan att behöva ange autentiseringsuppgifter!

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Läs mer om [Azure SQL-stöd för Azure AD-autentisering](~/articles/sql-database/sql-database-aad-authentication.md).
- Läs mer om [konfigurera Azure SQL-stöd för Azure AD-autentisering](~/articles/sql-database/sql-database-aad-authentication-configure.md).
- Läs mer om [autentiserings- och åtkomsthantering i SQLServer](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
