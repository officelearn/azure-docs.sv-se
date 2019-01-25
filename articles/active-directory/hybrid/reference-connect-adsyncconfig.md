---
title: 'Azure AD Connect: ADSyncConfig PowerShell-referens | Microsoft Docs'
description: Det här dokumentet innehåller information för ADSyncConfig.psm1 PowerShell-modulen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 723a02c529718ea17750fa72fe568aec6e963118
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901300"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  ADSyncConfig PowerShell-referens
Följande dokumentation innehåller information för ADSyncConfig.psm1 PowerShell-modul som ingår i Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SAMMANFATTNING
Hämtar de kontonamn och en domän som har konfigurerats i varje AD-koppling

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>BESKRIVNING
Den här funktionen används cmdleten ”Get-ADSyncConnector” som finns i AAD Connect för att hämta en tabell som visar koppling(ar) för AD-konto från anslutningen parametrar.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SAMMANFATTNING
Hämtar AD-objekt med ärvd behörighet har inaktiverats

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Söker i AD med början från parametern sökbas och returnerar alla objekt, filtreras efter ObjectClass parametern, som har ACL-arv för närvarande inaktiverad.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled - sökbas ”Contoso”

#### <a name="example-2"></a>EXEMPEL 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled - sökbas ”Contoso” - ObjectClass ”användare”

#### <a name="example-3"></a>EXEMPEL 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled - sökbas OU = AzureAD, DC = Contoso, DC = com - objektklassen ' *'

### <a name="parameters"></a>PARAMETRAR

#### <a name="-searchbase"></a>-Sökbas
Sökbas för LDAP-frågan som kan vara en DistinguishedName för AD-domän eller ett fullständigt domännamn

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

#### <a name="-objectclass"></a>-ObjectClass
Klassen för objekt som ska söka som kan vara ”*” (för alla objektklasser), ”användare”, ”grupp”, ”behållare” osv. Som standard söker den här funktionen efter 'organizationalUnit' objektklassen.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för grundläggande läsbehörigheter.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
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
Funktionen Set-ADSyncBasicReadPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Läs egenskapen åtkomst på alla attribut för alla underordnade datorobjekt
2.
Läs egenskapen åtkomst på alla attribut för alla underordnade enhetsobjekt
3.
Läs egenskapen åtkomst på alla attribut för alla underordnade foreignsecurityprincipal objekt
5.
Läs egenskapen åtkomst på alla attribut för alla underordnade objekt
6.
Läs egenskapen åtkomst på alla attribut för alla underordnade inetorgperson-objekt
7.
Läs egenskapen åtkomst på alla attribut för alla objekt för underordnad grupp
8.
Läs egenskapen åtkomst på alla attribut för alla underordnade kontakta objekt

Dessa behörigheter som tillämpas på alla domäner i skogen.
Du kan också ange ett unikt namn i ADobjectDN-parametern för att ange dessa behörigheter för AD objektet endast (inklusive arv till underordnade objekt).

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
Namnet på det Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för målobjektet AD för att ange behörigheter (valfritt)

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
Valfri parameter som anger om AdminSDHolder behållaren inte ska uppdateras med dessa behörigheter

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för Exchange-Hybrid-funktionen.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
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
Funktionen Set-ADSyncExchangeHybridPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Läs/Skriv-egenskapen åtkomst på alla attribut för alla underordnade objekt
2.
Läs/Skriv-egenskapen åtkomst på alla attribut för alla underordnade inetorgperson-objekt
3.
Läs/Skriv-egenskapen åtkomst på alla attribut för alla objekt för underordnad grupp
4.
Läs/Skriv-egenskapen åtkomst på alla attribut för alla underordnade kontakta objekt

Dessa behörigheter som tillämpas på alla domäner i skogen.
Du kan också ange ett unikt namn i ADobjectDN-parametern för att ange dessa behörigheter för AD objektet endast (inklusive arv till underordnade objekt).

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
Namnet på det Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för målobjektet AD för att ange behörigheter (valfritt)

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
Valfri parameter som anger om AdminSDHolder behållaren inte ska uppdateras med dessa behörigheter

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för offentlig mapp för Exchange-e-post-funktion.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
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
Funktionen Set-ADSyncExchangeMailPublicFolderPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Läs egenskapen åtkomst på alla attribut för alla underordnade publicfolder objekt

Dessa behörigheter som tillämpas på alla domäner i skogen.
Du kan också ange ett unikt namn i ADobjectDN-parametern för att ange dessa behörigheter för AD objektet endast (inklusive arv till underordnade objekt).

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
Namnet på det Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för målobjektet AD för att ange behörigheter (valfritt)

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
Valfri parameter som anger om AdminSDHolder behållaren inte ska uppdateras med dessa behörigheter

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för mS-DS-ConsistencyGuid funktion.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
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
Funktionen Set-ADSyncMsDsConsistencyGuidPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Läs/Skriv-egenskapen åtkomst på mS-DS-ConsistencyGuid attribut för alla underordnade objekt

Dessa behörigheter som tillämpas på alla domäner i skogen.
Du kan också ange ett unikt namn i ADobjectDN-parametern för att ange dessa behörigheter för AD objektet endast (inklusive arv till underordnade objekt).

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
Namnet på det Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för målobjektet AD för att ange behörigheter (valfritt)

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
Valfri parameter som anger om AdminSDHolder behållaren inte ska uppdateras med dessa behörigheter

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för synkronisering av lösenordshash.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Unikt namn
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncPasswordHashSyncPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Replikera katalogändringar
2.
Replikera ändringar av alla

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
Namnet på det Active Directory-konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för tillbakaskrivning av lösenord från Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
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
Funktionen Set-ADSyncPasswordWritebackPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Återställa lösenord för underordnade objekt
2.
Egenskapen skrivbehörighet på lockoutTime attribut för alla underordnade objekt
3.
Egenskapen skrivbehörighet på pwdLastSet attribut för alla underordnade objekt

Dessa behörigheter som tillämpas på alla domäner i skogen.
Du kan också ange ett unikt namn i ADobjectDN-parametern för att ange dessa behörigheter för AD objektet endast (inklusive arv till underordnade objekt).

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
Namnet på det Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för målobjektet AD för att ange behörigheter (valfritt)

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
Valfri parameter som anger om AdminSDHolder behållaren inte ska uppdateras med dessa behörigheter

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Öka behörigheter för en AD-objekt som annars inte ingår i någon AD-säkerhetsgruppen skyddade.
Ett typexempel är det AD Connect-konto (MSOL) som skapas automatiskt av AAD Connect.
Det här kontot har replikera behörigheter på alla domäner, men lätt kan äventyras som inte skyddas.

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncRestrictedPermissions kommer öka behörigheter säkerhetskopiering det konto som anges.
Skärpa behörigheter omfattar följande steg:
1.
Inaktivera arv på det angivna-objektet
2.
Ta bort alla åtkomstkontrollposter på specifika objekt, utom åtkomstkontrollposter som är specifika för SJÄLVBETJÄNINGSPORTALEN.
Vi vill hålla standardbehörigheterna intakt när det gäller att själv.
3.
Tilldela specifika behörigheter:

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
Unikt namn för den Active Directory-konto vars behörigheter måste höjas.
Detta är vanligtvis MSOL_nnnnnnnnnn-konto eller en anpassad domän-konto som har konfigurerats i AD-koppling.

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
Administratörsautentiseringsuppgifter som har rätt behörighet för att begränsa behörigheten för kontot ADConnectorAccountDN. Detta är vanligtvis Enterprise- eller domänadministratören. Det fullständigt kvalificerade domännamnet för administratörskontot Undvik att använda lookup kontofel.
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
När DisableCredentialValidation används, funktionen kommer inte att kontrollera om de angivna autentiseringsuppgifterna i - autentiseringsuppgifterna är giltiga i AD och om det tillhandahållna kontot har rätt behörighet för att begränsa behörigheten för kontot ADConnectorAccountDN.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory-skog och domän för tillbakaskrivning av grupp från Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
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
Funktionen Set-ADSyncUnifiedGroupWritebackPermissions ger behörigheter som krävs för AD-synkronisering, bland annat följande:
1.
Allmän Läs/Skriv, ta bort, ta bort trädet och Create\Delete underordnade för alla gruppen objekttyper och underobjekt

Dessa behörigheter som tillämpas på alla domäner i skogen.
Du kan också ange ett unikt namn i ADobjectDN-parametern för att ange dessa behörigheter för AD objektet endast (inklusive arv till underordnade objekt).
I det här fallet blir ADobjectDN det unika namnet på den behållare som du önskar att länka med funktionen GroupWriteback.

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
Namnet på det Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Domänen för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för Active Directory-konto som är eller ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

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
Unikt namn för målobjektet AD för att ange behörigheter (valfritt)

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
Valfri parameter som anger om AdminSDHolder behållaren inte ska uppdateras med dessa behörigheter

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Visar behörigheterna för ett angivet AD-objekt.

### <a name="syntax"></a>SYNTAX

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Den här funktionen returnerar alla AD-behörigheter som ställts in för ett angivet AD-objekt som angetts i parametern - ADobjectDN.
ADobjectDN måste anges i formatet DistinguishedName.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fyll ADobjectDN beskrivning}}

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).