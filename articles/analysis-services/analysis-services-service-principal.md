---
title: Automatisera uppgifter för Azure Analysis Services med tjänstens huvudnamn | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 76cadc453a696b8d19788525bfb69cf9cacd353d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448245"
---
# <a name="automation-with-service-principals"></a>Automatisering med tjänstens huvudnamn

Tjänstens huvudnamn är en programresurs för Azure Active Directory som du skapar i din klient för att utföra obevakade åtgärder på resurs- och tjänstnivå. De är en unik form av *användaridentitet* med en program-ID och lösenord eller certifikat. Ett huvudnamn för tjänsten har endast de behörigheterna som krävs för att utföra uppgifter som definieras av roller och behörigheter som den är tilldelad. 

I Analysis Services används tjänstens huvudnamn med Azure Automation, PowerShell obevakat läge, anpassade klientprogram och web apps för att automatisera vanliga uppgifter. Till exempel etablering servrar, distribuerar modeller, datauppdatering, skala upp/ned och pausa/återuppta kan alla automatiseras med hjälp av tjänstens huvudnamn. Behörigheter har tilldelats till tjänstens huvudnamn via rollmedlemskap, ungefär som vanliga Azure AD UPN-konton.

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
 
Tjänstens huvudnamn kan skapas i Azure portal eller med hjälp av PowerShell. Du kan läsa mer här:

[Skapa tjänstens huvudnamn – Azure-portalen](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Store autentiseringsuppgifter och certifikat tillgångar i Azure Automation

Autentiseringsuppgifter för tjänstens huvudnamn och certifikat kan lagras på ett säkert sätt i Azure Automation för runbook-åtgärder. Du kan läsa mer här:

[Inloggningstillgångar i Azure Automation](../automation/automation-credentials.md)   
[Certifikattillgångar i Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Lägga till tjänstens huvudnamn i admin-serverrollen

Innan du kan använda ett huvudnamn för tjänsten för hanteringsåtgärder för Analysis Services-server, måste du lägga till det administratörsrollen. Mer information finns i [lägga till tjänstens huvudnamn till administratörsrollen för servern](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Tjänstens huvudnamn i anslutningssträngar

Tjänstens huvudnamn appID och lösenord eller certifikat kan användas i anslutningen strängar ungefär samma sätt som ett UPN.

### <a name="powershell"></a>PowerShell

När du använder ett huvudnamn för tjänsten för resursen hanteringsåtgärder med den [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) modul, Använd `Login-AzureRmAccount` cmdlet. När du använder ett huvudnamn för tjänsten för åtgärder med den [SQLServer](https://www.powershellgallery.com/packages/SqlServer) modul, Använd `Add-AzureAnalysisServicesAccount` cmdlet. 

I följande exempel används appID och ett lösenord för att utföra en uppdatering för modellen databasåtgärd:

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
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