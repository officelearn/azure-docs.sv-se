---
title: 'Azure AD Connect: Felsökning av anslutningsproblem för SQL | Microsoft Docs'
description: Beskriver hur du kan felsöka anslutningsproblem för SQL som inträffar med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: edc15f573017fe57db0540da63eff7f2ed723dd7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187484"
---
# <a name="troubleshoot-sql-connectivity-issues-with-azure-ad-connect"></a>Felsöka SQL anslutningsproblem med Azure AD Connect
Den här artikeln förklarar hur du felsöker problem med nätverksanslutningen mellan Azure AD Connect och SQL Server. 

Följande skärmbild visar ett typiskt fel, om SQL Server inte kan hittas.

![SQL-fel](./media/tshoot-connect-tshoot-sql-connectivity/sql1.png)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Öppna ett powershell-fönster och importera ADSyncTools Powershell-modulen

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools.psm1" 
```

>[!NOTE]
>Install-Module behöver uppdateras till [PowerShell 5.0 (WMF 5.0)](https://www.microsoft.com/download/details.aspx?id=50395) eller senare.  
Eller installera [PackageManagement PowerShell-moduler Preview – mars 2016 för PowerShell 3.0/4.0](https://www.microsoft.com/download/details.aspx?id=51451) 

- **Visa alla kommandon**: `Get-Command -Module AdSyncTools` 
- **Köra powershell-funktionen**: `Connect-ADSyncDatabase` med följande parametrar
    - Server. Namnet på SQL Server.
    - Instans. (Valfritt) SQL Server-instans namn och eventuellt portnummer som du vill använda. Ange inte den här parametern om du vill använda standardinstansen.
    - Användarnamnet. (Valfritt) Användarkontot kan ansluta till. Om det lämnas tomt används den inloggade användaren. Om du ansluter till en fjärransluten SQL Server som det bör vara anpassade service-konto som du har skapat för Azure ADConnect SQL-anslutning. Azure AD Connect använder Azure AD Connect-synkroniseringstjänstkontot att autentisera till en fjärransluten SQLServer.
    - Lösenordet. (Valfritt) Lösenordet för det tillhandahållna användarnamnet.

Den här powershell-funktionen ska försöker binda till den angivna SQL Server och instans med de autentiseringsuppgifter som angavs i eller Använd autentiseringsuppgifter för den aktuella användaren. Om SQL Server inte går att hitta skriptet ska försöka ansluta till SQL Browser-tjänsten för att fastställa aktiverade protokoll och portar.

Exempel med bara ett servernamn:
```
PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> import-module .\AdSyncTools.psm1

PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> Connect-AdSyncDatabase -Server SQL1
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1 using a TCP binding for the default instance.
   Data Source=tcp:SQL1\;Integrated Security=True.ConnectionString
   Successfully connected.


StatisticsEnabled                : False
AccessToken                      : 
ConnectionString                 : Data Source=tcp:SQL1\;Integrated Security=True
ConnectionTimeout                : 15
Database                         : master
DataSource                       : tcp:SQL1\
PacketSize                       : 8000
ClientConnectionId               : 23e06ef2-0a38-4f5f-9291-da931de40375
ServerVersion                    : 13.00.4474
State                            : Open
WorkstationId                    : SQL1
Credential                       : 
FireInfoMessageEventOnUserErrors : False
Site                             : 
Container                        : 




PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> 
```

Exempel med en instans och Port nummer som inte finns:

```
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow 
remote connections. (provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
Get-ADSyncSQLBrowserInstances : Unable to read the SQL Server Browser configuration. An existing connection was forcibly closed by the remote host. 
Ensure port 1434 (UDP) is open on SQL1 and the SQL Server Browser service is running. 
At C:\Program Files\Microsoft Azure Active Directory Connect\tools\AdSyncTools.psm1:1717 char:18
+     $instances = Get-ADSyncSQLBrowserInstances $Server
+                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Get-ADSyncSQLBrowserInstances
 


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an inbound firewall rule on SQL1 to allow connection. Enable the SQL Se
rver Browser service temporarily on the SQL server and run this cmdLet again to further troubleshoot the issue. Alternatively use the SQL Server Configur
ation Manager on SQL1 to verify the instance name and TCP/IP port assignment manually. 

You must specify both the instance name and the port to connect when the SQL Server Browser service is not running. An inbound firewall rule on SQL1 is required for the associated port.
    Example: 'MySQLInstance,1234' where 1234 has a matching firewall rule.

PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> 
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1,99 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1,99;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: TCP Provider, error: 0 - The remote computer refused the network connection.)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
SQL browser response contained 2 instances.
Verifying protocol bindings and port connectivity.
MSSQLSERVER     : Enabled - port 1433 is assigned and reachable through the firewall
INSTANCE1       : Blocked - the inbound firewall rule for port 58379 is missing or disabled


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an
inbound firewall rule on SQL1 to allow connection. Review the TcpStatus field
for each instance and take corrective action.


InstanceName : MSSQLSERVER
tcp          : 1433
TcpStatus    : Enabled - port 1433 is assigned and reachable through the firewall

InstanceName : INSTANCE1
tcp          : 58379
TcpStatus    : Blocked - the inbound firewall rule for port 58379 is missing or disabled




PS C:\Program Files\Microsoft Azure Active Directory Connect\tools>  
```

## <a name="next-steps"></a>Nästa steg
- [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
-  [Azure AD-anslutning med Azure AD Connect](tshoot-connect-connectivity.md)
