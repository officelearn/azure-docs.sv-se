---
title: Automatisera Azure Analysis Services-uppgifter med tjänsthuvudnamn | Microsoft-dokument
description: Lär dig hur du skapar ett tjänsthuvudnamn för automatisering av administrativa Azure Analysis Services-uppgifter.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266148"
---
# <a name="automation-with-service-principals"></a>Automatisering med tjänstens huvudnamn

Tjänstens huvudnamn är en programresurs för Azure Active Directory som du skapar i din klient för att utföra obevakade åtgärder på resurs- och tjänstnivå. De är en unik typ av *användaridentitet* med ett program-ID och lösenord eller certifikat. Ett tjänsthuvudnamn har bara de behörigheter som krävs för att utföra uppgifter som definierats av de roller och behörigheter som det har tilldelats. 

I Analysis Services används tjänstobjektnamn med Azure Automation, Obevakat läge i PowerShell, anpassade klientprogram och webbappar för att automatisera vanliga uppgifter. Till exempel kan etablering av servrar, distribuera modeller, datauppdatering, skala upp/ned och pausa/återupptas automatiseras med hjälp av tjänstens huvudnamn. Behörigheter tilldelas tjänstobjektnamn via rollmedlemskap, ungefär som vanliga Azure AD UPN-konton.

Analysis Services stöder också åtgärder som utförs av hanterade identiteter med hjälp av tjänsthuvudnamn. Mer information finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) och [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).  

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
 
Tjänsthuvudnamn kan skapas i Azure-portalen eller med hjälp av PowerShell. Du kan läsa mer här:

[Skapa tjänstens huvudnamn - Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Lagra autentiseringsuppgifter och certifikatresurser i Azure Automation

Tjänstens huvudautentiseringsuppgifter och certifikat kan lagras säkert i Azure Automation för runbook-åtgärder. Du kan läsa mer här:

[Autentiseringsuppgifter i Azure Automation](../automation/automation-credentials.md)   
[Certifikattillgångar i Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Lägga till tjänsthuvudnamn i serveradministratörsrollen

Innan du kan använda ett tjänsthuvudnamn för Analysis Services-serverhanteringsåtgärder måste du lägga till det i rollen serveradministratörer. Mer information finns i [Lägga till ett tjänsthuvudnamn i serveradministratörsrollen](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Tjänstens huvudnamn i anslutningssträngar

Tjänstens huvudappID och lösenord eller certifikat kan användas i anslutningssträngar på samma sätt som ett UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Använda modulen Az.AnalysisServices

När du använder ett huvudnamn för resurshantering med modulen [Az.AnalysisServices](/powershell/module/az.analysisservices) använder du `Connect-AzAccount` cmdlet. 

I följande exempel används appID och ett lösenord för att utföra kontrollplansåtgärder för synkronisering till skrivskyddade repliker och skala upp/ut:

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

#### <a name="using-sqlserver-module"></a>Använda SQLServer-modul

I följande exempel används appID och ett lösenord för att utföra en uppdatering av modelldatabasen:

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

När du ansluter till klientprogram och webbappar, [AMO och ADOMD klientbibliotek](analysis-services-data-providers.md) version 15.0.2 och högre installerbara paket `app:AppID` från `cert:thumbprint`NuGet support tjänsthuvudnamn i anslutningssträngar med hjälp av följande syntax: och lösenord eller . 

I följande exempel `appID` och `password` a används för att utföra en modelldatabasuppdateringsåtgärd:

```csharp
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
[Lägga till ett tjänsthuvudnamn i serveradministratörsrollen](analysis-services-addservprinc-admins.md)   
