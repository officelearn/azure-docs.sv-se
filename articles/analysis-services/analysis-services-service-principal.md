---
title: Automatisera uppgifter för Azure Analysis Services med tjänstens huvudnamn | Microsoft Docs
description: Lär dig hur du skapar tjänstens huvudnamn för att automatisera uppgifter för Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a440494b183d18c1d888b5d39836eb4317190d02
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708313"
---
# <a name="automation-with-service-principals"></a>Automatisering med tjänstens huvudnamn

Tjänstens huvudnamn är en programresurs för Azure Active Directory som du skapar i din klient för att utföra obevakade åtgärder på resurs- och tjänstnivå. De är en unik form av *användaridentitet* med en program-ID och lösenord eller certifikat. Ett huvudnamn för tjänsten har endast de behörigheterna som krävs för att utföra uppgifter som definieras av roller och behörigheter som den är tilldelad. 

I Analysis Services används tjänstens huvudnamn med Azure Automation, PowerShell obevakat läge, anpassade klientprogram och web apps för att automatisera vanliga uppgifter. Till exempel etablering servrar, distribuerar modeller, datauppdatering, skala upp/ned och pausa/återuppta kan alla automatiseras med hjälp av tjänstens huvudnamn. Behörigheter har tilldelats till tjänstens huvudnamn via rollmedlemskap, ungefär som vanliga Azure AD UPN-konton.

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
 
Tjänstens huvudnamn kan skapas i Azure portal eller med hjälp av PowerShell. Du kan läsa mer här:

[Skapa tjänstens huvudnamn – Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Store autentiseringsuppgifter och certifikat tillgångar i Azure Automation

Autentiseringsuppgifter för tjänstens huvudnamn och certifikat kan lagras på ett säkert sätt i Azure Automation för runbook-åtgärder. Du kan läsa mer här:

[Inloggningstillgångar i Azure Automation](../automation/automation-credentials.md)   
[Certifikattillgångar i Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Lägga till tjänstens huvudnamn i admin-serverrollen

Innan du kan använda ett huvudnamn för tjänsten för hanteringsåtgärder för Analysis Services-server, måste du lägga till det administratörsrollen. Mer information finns i [lägga till tjänstens huvudnamn till administratörsrollen för servern](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Tjänstens huvudnamn i anslutningssträngar

Tjänstens huvudnamn appID och lösenord eller certifikat kan användas i anslutningen strängar ungefär samma sätt som ett UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />Med Az.AnalysisServices-modulen

När du använder ett huvudnamn för tjänsten för resursen hanteringsåtgärder med den [Az.AnalysisServices](/powershell/module/az.analysisservices) modul, Använd `Connect-AzAccount` cmdlet. 

I följande exempel används appID och ett lösenord för att utföra kontrollplanåtgärder tills synkroniseringen är skrivskyddade repliker och skala upp/ut:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Med hjälp av SQLServer-modulen

I följande exempel används appID och ett lösenord för att utföra en uppdatering för modellen databasåtgärd:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO och ADOMD 

När du ansluter med klientprogram och web apps [AMO och ADOMD-klientbiblioteken](analysis-services-data-providers.md) version 15.0.2 och högre installerbart paket från NuGet stöd för tjänstens huvudnamn i anslutningssträngar med följande syntax: `app:AppID` och lösenord eller `cert:thumbprint`. 

I följande exempel `appID` och en `password` används för att utföra en uppdatering för modellen databasåtgärd:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Nästa steg
[Logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Lägga till tjänstens huvudnamn till administratörsrollen för servern](analysis-services-addservprinc-admins.md)   
