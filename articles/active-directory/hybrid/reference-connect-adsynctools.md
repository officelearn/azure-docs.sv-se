---
title: 'Azure AD Connect: ADSyncTools PowerShell-referens | Microsoft-dokument'
description: Det här dokumentet innehåller referensinformation för ADSyncTools.psm1 PowerShell-modulen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60454667"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell-referens
Följande dokumentation innehåller referensinformation för ADSyncTools.psm1 PowerShell-modulen som ingår i Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Rensa mS-Ds-ConsistencyGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Rensa värdet i mS-Ds-ConsistencyGuid för mål-AD-användaren

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
Rikta in användaren i AD för att ställa in

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Bekräfta-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i Synopsis}}

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

{{ Lägg till exempelbeskrivning här }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i Synopsis}}

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

{{ Lägg till exempelbeskrivning här }}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-database"></a>-Databas
{{Fyllningsdatabasbeskrivning}}

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
{{Beskrivning av fyllningsinstans}}

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
{{Beskrivning av fyllningslösenord}}

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
{{Beskrivning av fyllningsserver}}

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
{{Beskrivning av användarnamn för fyllning}}

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMMANFATTNING
Export konsekvensguidrapport

### <a name="syntax"></a>SYNTAX

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Genererar en ConsistencyGuid-rapport baserad på en import-CSV-fil från Import-ADSyncToolsImmutableIdMigration

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
Använd alternativt inloggnings-ID (e-post)

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
UserPrincipalName

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
Oföränderligidguid

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
Filnamn för CSV- och LOG-filer

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsyncsqlbrowserinstances"></a>Få-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i Synopsis}}

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

{{ Lägg till exempelbeskrivning här }}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-hostName
{{Beskrivning av värdnamn för fyllning}}

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
Returnerar ett AD-objekt ATT GÖRA: Stöd för flera skogar

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
Rikta in användaren i AD för att ställa in ConsistencyGuid

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Hämta mS-Ds-ConsistencyGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar värdet i attributet mS-Ds-ConsistencyGuid för mål-AD-användaren i GUID-format

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
Rikta in användaren i AD för att ställa in

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolsobjectguid"></a>Hämta ADSyncToolsObjectGuid

### <a name="synopsis"></a>SAMMANFATTNING
Hämta ObjectGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar värdet i ObjectGUID-attributet för mål-AD-användaren i GUID-format

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
Rikta in användaren i AD för att ställa in

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolsrunhistory"></a>Hämta ADSyncToolsRunHistory

### <a name="synopsis"></a>SAMMANFATTNING
Få AAD Connect Run Historik

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktion som returnerar AAD Connect Run History i XML-format

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
{{Beskrivning av fyllningsdagar}}

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SAMMANFATTNING
Få användare med SourceAnchor ändrade fel

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionsfrågor AAD Connect Run Historik och exporterar alla användare som rapporterar felet: "SourceAnchor attributet has changed."

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
#Required Parameters
```

$sourcePath = Läsvärd -Fråga "Ange sökvägen till loggfilen med\<filnamnet"\>#" Source_Path " $outputPath = Läs-host -Fråga "Ange\<din utefilsökväg med filnamn" #" Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-sourcepath"></a>-sourcePath
{{Beskrivning av fyllnings sourcePath}}

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

#### <a name="-outputpath"></a>-utdatapath
{{Fyllning av utdatapathbeskrivning}}

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SAMMANFATTNING
Importera OföränderligtID från AAD

### <a name="syntax"></a>SYNTAX

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Genererar en fil med alla Azure AD-synkroniserade användare som innehåller ImmutableID-värdet i GUID-format Krav: MSOnline PowerShell-modul

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
Utdata-CSV-fil

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
Hämta synkroniserade användare från Azure AD-papperskorgen

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .


## <a name="invoke-adsyncdatabasequery"></a>Anropa-AdSyncDatabaseQuery

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i Synopsis}}

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

{{ Lägg till exempelbeskrivning här }}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-query"></a>-Fråga
{{Fyllningsfrågabeskrivning}}

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
{{Fyll sqlconnection-beskrivning}}

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Ta bort-ADSyncToolsExpirerade certifikat

### <a name="synopsis"></a>SAMMANFATTNING
Skript för att ta bort utgångna certifikat från UserCertificate-attribut

### <a name="syntax"></a>SYNTAX

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Det här skriptet tar alla objekt från en målorganisationsenhet i Active Directory-domänen – filtrerade efter objektklass (användare/dator) och tar bort alla utgångna certifikat som finns i attributet UserCertificate.
Som standard (BackupOnly-läge) säkerhetskopierar den endast utgångna certifikat till en fil och gör inga ändringar i AD.
Om du använder -BackupOnly $false tas alla utgångna certifikat som finns i UserCertificate-attributet för dessa objekt bort från AD efter att ha kopierats till filen.
Varje certifikat säkerhetskopieras till ett avgränsat filnamn: ObjectClass_ObjectGUID_CertThumprint.cer Skriptet skapar också en loggfil i CSV-format som visar alla användare med certifikat som antingen är giltiga eller utgångna, inklusive den faktiska åtgärden som vidtagits (Skipped/Exported/Deleted).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Ta bort-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass-användare

#### <a name="example-2"></a>EXEMPEL 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Ta bort-ADSyncToolsExpiredCertificates -TargetOU "OU=Datorer,OU=Corp,DC=Contoso,DC=com" -ObjectClass-dator -BackupOnly $false

### <a name="parameters"></a>PARAMETRAR

#### <a name="-targetou"></a>-TargetOU
Målou att söka efter AD-objekt

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
BackupOnly tar inte bort några certifikat från AD

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

#### <a name="-objectclass"></a>-ObjectClass (Objektklass)
Filter för objektklass

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="repair-adsynctoolsautoupgradestate"></a>Reparation-ADSyncToolsAutoUpgradeState

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

## <a name="resolve-adsynchostaddress"></a>Lös-ADSyncHostAddress

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i Synopsis}}

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

{{ Lägg till exempelbeskrivning här }}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-hostName
{{Beskrivning av värdnamn för fyllning}}

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

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates Restore-ADSyncToolsExpiredCertificates Restore-ADSyncToolsExpiredCertificates Restore-

### <a name="synopsis"></a>SAMMANFATTNING
(ATT GÖRA) Återställer AD UserCertificate-attribut från en certifikatfil

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
Ange mS-Ds-ConsistencyGuid på AD-användare

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Ange ett värde i attributet mS-Ds-ConsistencyGuid för ad-användaren

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
Rikta in användaren i AD för att ställa in ConsistencyGuid

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
ImmutableId (byte array, GUID, GUID sträng eller Base64 sträng)

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i Synopsis}}

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

{{ Lägg till exempelbeskrivning här }}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-hostName
{{Beskrivning av värdnamn för fyllning}}

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
{{Beskrivning av fyllningsport}}

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

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SAMMANFATTNING
Skapar en spårningsfil från och AD-importsteg

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Spårar alla ldap-frågor för en AAD Connect AD Import som körs från en viss AD-vattenstämpelkontrollpunkt (partitionscookie). Skapar en spårningsfil '.\ADimportTrace_yyyyMMddHHmmss.log' i den aktuella mappen.

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
{{Fyll adconnectorxml-beskrivning}}

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

#### <a name="-dc"></a>-dc (st)
XML-fil för AD-anslutningsexport

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

#### <a name="-rootdn"></a>-rootDN
Måldomänkontrollant

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
Skogsrot DN

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
Typer av AD-objekt som ska spåras \> * = alla objekttyper

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
Om du redan kör som domänadministratör behöver du inte ange AD-autentiseringsuppgifter.
Manuell ingång av vattenstämpel, i stället för XML-fil t.ex. $ADwatermark = "TVNEUwMAAAAAXyK9ir1zSAQAAAAAAAAAAAa(...)"

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

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

#### <a name="-context"></a>-Sammanhang
Informationsbeskrivning för Param1

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
Informationsbeskrivning för Param2

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
Informationsbeskrivning för Param2

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
Informationsbeskrivning för Param2

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Uppdatering-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMMANFATTNING
Uppdaterar användare med den nya ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAX

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Uppdaterar användare med det nya ConsistencyGuid-värdet (ImmutableId) som hämtats från ConsistencyGuid-rapporten Den här funktionen stöder WhatIf-växeln Obs: ConsistencyGuid-rapporten måste importeras med Tab Demiliter

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
Oföränderligidguid

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

#### <a name="-action"></a>-Åtgärd
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
Filnamn för UTdata för LOGGfiler

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
Uppmanar dig att bekräfta innan du kör cmdleten.

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
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .
