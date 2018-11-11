---
title: 'Azure AD Connect: ADSyncTools PowerShell-referens | Microsoft Docs'
description: Det här dokumentet innehåller information för ADSyncTools.psm1 PowerShell-modulen.
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 734387e7ce5fc27cb88c73601bdfd0f35d04b693
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288687"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell-referens
Följande dokumentation innehåller information för ADSyncTools.psm1 PowerShell-modul som ingår i Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Rensa ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Rensa mS-Ds-ConsistencyGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Rensa värdet i mS-Ds-ConsistencyGuid för målets AD-användare

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Målanvändaren i AD för att ställa in

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Bekräfta ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyller du i sammanfattning}}

### <a name="syntax"></a>SYNTAX

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

## <a name="connect-adsyncdatabase"></a>Ansluta AdSyncDatabase

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyller du i sammanfattning}}

### <a name="syntax"></a>SYNTAX

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-database"></a>-Databasen
{{Fyll databasen beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instans
{{Fyll instans beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fyll lösenord beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fyll Serverbeskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fyll användarnamn beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMMANFATTNING
Exportera ConsistencyGuid rapport

### <a name="syntax"></a>SYNTAX

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Genererar en ConsistencyGuid rapport som baseras på en CSV-fil för import från Import ADSyncToolsImmutableIdMigration

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Använda alternativa inloggnings-ID (e-post)

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
userPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Utdata
Resulterande filnamn för CSV- och LOGGFILER

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyller du i sammanfattning}}

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-Värdnamn
{{Fyll värdnamn beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SAMMANFATTNING
Hämta användare från AD

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar ett AD-objekt genom att göra: stöd för flera skogar

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Målanvändaren i AD för att ange ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Hämta mS-Ds-ConsistencyGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar värdet i attributet mS-Ds-ConsistencyGuid för målanvändaren AD i GUID-format

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Målanvändaren i AD för att ställa in

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SAMMANFATTNING
Hämta ObjectGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar värdet i ObjectGUID-attributet för målets AD-användaren i GUID-format

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Målanvändaren i AD för att ställa in

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SAMMANFATTNING
Hämta historiken för AAD-Connnect kör

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktion som returnerar Körningshistorik för AAD Connnect i XML-format

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>EXEMPEL 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-days"></a>-Dagar
{{Fyll dagar beskrivning}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SAMMANFATTNING
Hämta användare med SourceAnchor ändrats fel

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen frågar AAD Connnect Körningshistorik och exporterar alla användare som rapporterar felet: ”SourceAnchor-attribut har ändrats”.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
#Required Parameters
```

$sourcePath = Read-Host - fråga ”ange din loggfilssökväg med filnamnet” # ”\<källsökväg\>” $outputPath = Read-Host-Prompt ”ange din out filsökväg med filnamnet” # ”\<Out_Path\>”
 
 Get-ADSyncToolsUsersSourceAnchorChanged - sourcePath $sourcePath - outputPath $outputPath

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-sourcepath"></a>-sourcePath
{{Fyll sourcePath beskrivning}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Fyll outputPath beskrivning}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Importera ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SAMMANFATTNING
Importera ImmutableID från AAD

### <a name="syntax"></a>SYNTAX

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Genererar en fil med alla Synchronized för Azure AD-användare som innehåller värdet ImmutableID i GUID-format krav: MSOnline PowerShell-modulen

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-output"></a>-Utdata
CSV-filen för utdata

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Hämta synkroniserade användare från Azure AD-Papperskorgen

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Anropa AdSyncDatabaseQuery

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyller du i sammanfattning}}

### <a name="syntax"></a>SYNTAX

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-query"></a>-Fråga
{{Fyll beskrivning av fråga}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fyll SqlConnection beskrivning}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Ta bort ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SAMMANFATTNING
Skript för att ta bort utgångna certifikat från UserCertificate-attributet

### <a name="syntax"></a>SYNTAX

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Det här skriptet hämtar alla objekt från målet organisationsenhet i Active Directory-domänen – filtreras efter objektklassen (användare/dator) och tar bort alla utgångna certifikat finns i UserCertificate-attributet.
Som standard (BackupOnly läge) kommer endast att säkerhetskopiera utgångna certifikat till en fil och göra inte några ändringar i AD.
Om du använder - BackupOnly $false så att alla har upphört att gälla-certifikat finns i UserCertificate-attributet för de här objekten tas bort från AD efter att kopieras till filen.
Varje certifikat säkerhetskopieras till en avgränsas filename: ObjectClass_ObjectGUID_CertThumprint.cer skriptet skapas också en loggfil i CSV-format som visar alla användare med certifikat som antingen är giltigt eller har upphört att gälla, inklusive den faktiska åtgärd () Överhoppade/exporterade/har tagits bort).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Ta bort ADSyncToolsExpiredCertificates-{TargetOU ”OU = användare, OU = Corp, DC = Contoso, DC = com” - ObjectClass användare

#### <a name="example-2"></a>EXEMPEL 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Ta bort ADSyncToolsExpiredCertificates-{TargetOU ”Organisationsenhet = datorer, OU = Corp, DC = Contoso, DC = com” - ObjectClass dator - BackupOnly $false

### <a name="parameters"></a>PARAMETRAR

#### <a name="-targetou"></a>{TargetOU-
Target OU-sökning för AD-objekt

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly kommer inte att ta bort certifikat från AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Filter för objekt-klass

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Reparera ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SAMMANFATTNING
Kort beskrivning

### <a name="syntax"></a>SYNTAX

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>BESKRIVNING
Lång beskrivning

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Lös ADSyncHostAddress

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyller du i sammanfattning}}

### <a name="syntax"></a>SYNTAX

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-Värdnamn
{{Fyll värdnamn beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Återställ ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SAMMANFATTNING
(FÖR ATT UTFÖRA) Återställer AD UserCertificate-attributet från en certicate-fil

### <a name="syntax"></a>SYNTAX

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>BESKRIVNING
Lång beskrivning

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Ange mS-Ds-ConsistencyGuid för AD-användare

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Ange ett värde i attributet mS-Ds-ConsistencyGuid för målets AD-användare

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Målanvändaren i AD för att ange ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Värde
ImmutableId (Byte-matris, GUID, GUID sträng eller Base64-sträng)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyller du i sammanfattning}}

### <a name="syntax"></a>SYNTAX

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-Värdnamn
{{Fyll värdnamn beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Fyll port beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Spåra ADSyncToolsADImport

### <a name="synopsis"></a>SAMMANFATTNING
Skapar en spårningsfil från och AD-Import steg

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Spårningar kör alla ldap-frågor för en AAD Connect AD Import från en viss AD vattenstämpel kontrollpunkt (partition cookie). Skapar en spårningsfil '.\ADimportTrace_yyyyMMddHHmmss.log' på den aktuella mappen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fyll ADConnectorXML beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
XML-filen av AD Connector exportera

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-RotDN
Måldomänkontrollanten

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
Skogens rot DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typer av AD-objekt att spåra \> * = alla typer av objekt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Om du redan körs som domänadministratör finns du behöver inte ange autentiseringsuppgifter för AD.
Manuell inmatning av vattenstämpel, i stället för XML-filen t.ex. $ADwatermark = ”TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)”

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Spåra ADSyncToolsLdapQuery

### <a name="synopsis"></a>SAMMANFATTNING
Kort beskrivning

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Lång beskrivning

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-context"></a>-Kontexten
Param1 hjälp beskrivning

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Param2 hjälp beskrivning

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Param2 hjälp beskrivning

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Param2 hjälp beskrivning

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Uppdatera ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMMANFATTNING
Uppdaterar användare med den nya ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAX

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Uppdateringar användare med det nya värdet för ConsistencyGuid (ImmutableId) kommer från ConsistencyGuid rapporten den här funktionen stöder växeln WhatIf Obs: ConsistencyGuid rapporten måste importeras med fliken Demiliter

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>EXEMPEL 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-distinguishedname"></a>-DistinguishedName
Unikt namn

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Åtgärd

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Utdata
Namnet på utdatafilen för loggfiler

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanas du att bekräfta innan du kör cmdlet: en.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
