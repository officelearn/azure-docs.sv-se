---
title: 'Azure AD Connect: ADSyncConfig PowerShell-referens | Microsoft-dokument'
description: Det här dokumentet innehåller referensinformation för ADSyncConfig.psm1 PowerShell-modulen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381203"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell-referens
Följande dokumentation innehåller referensinformation för ADSyncConfig.psm1 PowerShell-modulen som ingår i Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Hämta ADSyncADConnectorAccount

### <a name="synopsis"></a>SAMMANFATTNING
Hämtar kontonamnet och domänen som är konfigurerad i varje AD Connector

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>BESKRIVNING
Den här funktionen använder cmdleten Get-ADSyncConnector som finns i AAD Connect för att hämta från anslutningsparametrar en tabell som visar AD-anslutningskontot.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SAMMANFATTNING
Hämtar AD-objekt med behörighetsarv inaktiverat

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Söker i AD från parametern SearchBase och returnerar alla objekt, filtrerade efter parametern ObjectClass, som har ACL-arvet inaktiverat.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>EXEMPEL 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>EXEMPEL 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMETRAR

#### <a name="-searchbase"></a>-SearchBase (Sökbas)
SearchBase för LDAP-frågan som kan vara ett AD-domän distinguishedName eller en FQDN

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

#### <a name="-objectclass"></a>-ObjectClass (Objektklass)
Den klass av objekt som ska sökas och som kan vara '*' (för någon objektklass), "användare", "grupp", "behållare", etc. Som standard söker den här funktionen efter objektklassen "organizationalUnit".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för grundläggande läsbehörigheter.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncBasicReadPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Läsegenskapsåtkomst för alla attribut för alla underordnade datorobjekt
2.
Läsegenskapsåtkomst för alla attribut för alla underordnade enhetsobjekt
3.
Läs egenskapsåtkomst för alla attribut för alla underordnade utrikespolitiska objekt
5.
Läsegenskapsåtkomst för alla attribut för alla underordnade användarobjekt
6.
Läs egenskapsåtkomst för alla attribut för alla underordnade inetorgpersonobjekt
7.
Läsegenskapsåtkomst för alla attribut för alla underordnade gruppobjekt
8.
Läsegenskapsåtkomst för alla attribut för alla underordnade kontaktobjekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Du kan också ange parametern DistinguishedName i ADobjectDN för att ange dessa behörigheter för det AD-objektet (inklusive arv till underobjekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för mål AD-objektet för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för Exchange Hybrid-funktionen.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncExchangeHybridPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Läs-/skrivegenskapsåtkomst för alla attribut för alla underordnade användarobjekt
2.
Läs-/skrivegenskapsåtkomst för alla attribut för alla underordnade inetorgpersonobjekt
3.
Läs-/skrivegenskapsåtkomst för alla attribut för alla underordnade gruppobjekt
4.
Läs-/skrivegenskapsåtkomst för alla attribut för alla underordnade kontaktobjekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Du kan också ange parametern DistinguishedName i ADobjectDN för att ange dessa behörigheter för det AD-objektet (inklusive arv till underobjekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för mål AD-objektet för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera funktionen Active Directory-skog och domän för exchange mail-mapp.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncExchangeMailPublicFolderPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Läs egenskapsåtkomst för alla attribut för alla underordnade offentliga flerfaldigt objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Du kan också ange parametern DistinguishedName i ADobjectDN för att ange dessa behörigheter för det AD-objektet (inklusive arv till underobjekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för mål AD-objektet för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för mS-DS-ConsistencyGuid-funktionen.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncMsDsConsistencyGuidPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Läs-/skrivegenskapsåtkomst på mS-DS-ConsistencyGuid-attribut för alla underordnade användarobjekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Du kan också ange parametern DistinguishedName i ADobjectDN för att ange dessa behörigheter för det AD-objektet (inklusive arv till underobjekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för mål AD-objektet för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för synkronisering av lösenordsh hash.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncPasswordHashSyncPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Replikera katalogändringar
2.
Replikera katalogen ändrar alla

Dessa behörigheter ges till alla domäner i skogen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för återställning av lösenord från Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncPasswordWritebackPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Återställa lösenord för underordnade användarobjekt
2.
Skrivegenskapsåtkomst vid lockoutTime-attribut för alla underordnade användarobjekt
3.
Skrivegenskapsåtkomst på attributet pwdLastSet för alla underordnade användarobjekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Du kan också ange parametern DistinguishedName i ADobjectDN för att ange dessa behörigheter för det AD-objektet (inklusive arv till underobjekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för mål AD-objektet för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Dra åt behörigheter för ett AD-objekt som annars inte ingår i någon AD-skyddad säkerhetsgrupp.
Ett typiskt exempel är AD Connect-kontot (MSOL) som skapas av AAD Connect automatiskt.
Det här kontot har replikerade behörigheter för alla domäner, men kan enkelt komprometteras eftersom det inte är skyddat.

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncRestrictedPermissions kommer att skärpa behörigheterna för kontot som tillhandahålls.
Att skärpa behörigheterna omfattar följande steg:
1.
Inaktivera arv på det angivna objektet
2.
Ta bort alla åtkomstkontrollistor på det specifika objektet, förutom AKU:er som är specifika för SELF.
Vi vill behålla standardbehörigheterna intakta när det gäller SELF.
3.
Tilldela dessa specifika behörigheter:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot vars behörigheter måste skärpas.
Det här är vanligtvis MSOL_nnnnnnnnnn konto eller ett anpassat domänkonto som är konfigurerat i AD Connector.

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

#### <a name="-credential"></a>-Credential
Administratörsautentiseringsuppgifter som har de behörigheter som krävs för att begränsa behörigheterna för ADConnectorAccountDN-kontot. Detta är vanligtvis företags- eller domänadministratören. Använd det fullständigt kvalificerade domännamnet för administratörskontot för att undvika kontosökningsfel.
Exempel: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
När DisableCredentialValidation används, kontrollerar funktionen inte om autentiseringsuppgifterna i -Autentiseringsuppgifterna är giltiga i AD och om kontot som tillhandahålls har de behörigheter som krävs för att begränsa behörigheterna för ADConnectorAccountDN-kontot.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för gruppåterskrivning från Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>AnvändareDomän
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncUnifiedGroupWritebackPermissions ger nödvändiga behörigheter till AD-synkroniseringskontot, som innehåller följande:
1.
Allmän läsning/skrivning, borttagning, borttagningsträd och Skapa\Ta bort underordnad för alla gruppobjekttyper och underobjekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Du kan också ange parametern DistinguishedName i ADobjectDN för att ange dessa behörigheter för det AD-objektet (inklusive arv till underobjekt).
I det här fallet kommer ADobjectDN att vara det unika namnet på behållaren som du vill länka till funktionen GroupWriteback.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för Active Directory-kontot som används eller kommer att användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för mål AD-objektet för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

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

## <a name="show-adsyncadobjectpermissions"></a>Visa-ADSyncADObjectPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Visar behörigheter för ett angivet AD-objekt.

### <a name="syntax"></a>SYNTAX

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Den här funktionen returnerar alla AD-behörigheter som för närvarande har angetts för ett visst AD-objekt som finns i parametern -ADobjectDN.
ADobjectDN måste anges i ett DistinguishedName-format.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fyll adobjectdn beskrivning}}

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

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .
