---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-referens | Microsoft-dokument'
description: Det här dokumentet innehåller referensinformation för ADConnectivityTools.psm1 PowerShell-modulen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66473796"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools PowerShell-referens

Följande dokumentation innehåller referensinformation för ADConnectivityTools.psm1 PowerShell-modulen som ingår i Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Bekräfta-DnsConnectivity

### <a name="synopsis"></a>SAMMANFATTNING

Identifierar lokala Dns-problem.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör lokala Dns-anslutningstester.
För att konfigurera Active Directory-kopplingen måste användaren ha både namnmatchning för den skog som de försöker ansluta till och i domänkontrollanterna som är associerade till den här skogen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som ska testas mot.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Ange DC:er att testa mot.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Returnerar resultatet av denna diagnos i form av ett PSObject.
Inte nödvändigt under manuell interaktion med det här verktyget.

```yml
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

## <a name="confirm-forestexists"></a>Bekräfta-ForestExister

### <a name="synopsis"></a>SAMMANFATTNING

Avgör om det finns en angiven skog.

### <a name="syntax"></a>SYNTAX

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Frågar en DNS-server för IP-adresser som är associerade med en skog.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som ska testas mot.

```yml
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

## <a name="confirm-functionallevel"></a>Bekräfta-funktionellnivå

### <a name="synopsis"></a>SAMMANFATTNING

Verifierar AD-skogens funktionalitetsnivå.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount (olikartade)

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>SkogFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Verifierar att AD-skogens funktionalitetsnivå är lika med eller mer än en viss MinAdForestVersion (WindowsServer2003).
Konto (Domän\Användarnamn) och lösenord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPEL 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Målskogen.
Standardvärdet är skogen för den användare som för närvarande är inloggad.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Mål ForestFQDN-objekt.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="confirm-networkconnectivity"></a>Bekräfta-nätverksanslutning

### <a name="synopsis"></a>SAMMANFATTNING

Identifierar problem med lokala nätverksanslutningar.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör lokala nätverksanslutningstester.

För de lokala nätverkstesterna måste AAD Connect kunna kommunicera med de namngivna domänkontrollanterna på portarna 53 (DNS), 88 (Kerberos) och 389 (LDAP) De flesta organisationer som kör DNS på sina domänkontrollanter, vilket är anledningen till att det här testet för närvarande är integrerat.
Port 53 bör hoppas över om en annan DNS-server har angetts.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-dcs"></a>-DCs

Ange DC:er att testa mot.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Om användaren inte använder DNS-tjänster som tillhandahålls av AD Site / Logon DC, då de kanske vill hoppa över kontroll port 53.
Användaren måste fortfarande kunna lösa _.ldap._tcp. \<forestfqdn\> för att Active Directory Connector-konfigurationen ska lyckas.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Returnerar resultatet av denna diagnos i form av ett PSObject.
Inte nödvändigt under manuell interaktion med det här verktyget.

```yml
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

## <a name="confirm-targetsarereachable"></a>Bekräfta-målåterupptagbar

### <a name="synopsis"></a>SAMMANFATTNING

Avgör om en angiven skog och dess associerade domänkontrollanter kan nås.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör "ping"-tester (om en dator kan nå en måldator via nätverket och/eller internet)

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som ska testas mot.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Ange DC:er att testa mot.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Bekräfta-giltigadomäner

### <a name="synopsis"></a>SAMMANFATTNING

Verifiera att domänerna i den erhållna Skogen FQDN kan nås

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount (olikartade)

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>SkogFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Verifiera att alla domäner i den erhållna Skogen FQDN kan nås genom att försöka hämta DomainGuid och DomainDN.
Konto (Domän\Användarnamn) och lösenord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPEL 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Målskogen.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Mål ForestFQDN-objekt.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Bekräfta-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SAMMANFATTNING

Verifierar om en användare har autentiseringsuppgifter för Företagsadministratör.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Söker om den angivna användaren har Enterprise Admin-autentiseringsuppgifter.
Konto (Domän\Användarnamn) och lösenord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="get-domainfqdndata"></a>Hämta-DomainFQDNData

### <a name="synopsis"></a>SAMMANFATTNING

Hämtar en DomainFQDN från en kombination av konto och lösenord.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Försöker hämta ett domainFQDN-objekt från angivna autentiseringsuppgifter.
Om domänFQDN är giltigt returneras ett DomainFQDNName eller RootDomainName, beroende på användarens val.
Konto (Domän\Användarnamn) och lösenord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Önskad typ av data som kommer att hämtas.
För närvarande begränsad till "DomainFQDNName" eller "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Hjälpparameter som används av funktionen Start-NetworkConnectivityDiagnosisTools

```yml
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

## <a name="get-forestfqdn"></a>Hämta SkogFQDN

### <a name="synopsis"></a>SAMMANFATTNING

Hämtar ett ForestFQDN från en konto- och lösenordskombination.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Försöker hämta en ForestFQDN från de angivna autentiseringsuppgifterna.
Konto (Domän\Användarnamn) och lösenord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Målskogen. Standardvärdet är domänen för den inloggade användaren.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="start-connectivityvalidation"></a>Start-anslutningValidation

### <a name="synopsis"></a>SAMMANFATTNING

Huvudfunktion.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör alla tillgängliga mekanismer som verifierar ad-autentiseringsuppgifterna är giltiga.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Målskogen.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

För anpassade installationer: Flagga som är $True om användaren valde "Skapa nytt AD-konto" i AD-skogskontofönstret i AADConnects guide.
$False om användaren valde "Använd befintligt AD-konto".
För Express-installationer: Värdet för denna variabel måste vara $True för Express-installationer.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parameter som förfyller fältet Användarnamn när användarens autentiseringsuppgifter begärs.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SAMMANFATTNING

Huvudfunktion för nätverksanslutningstester.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör lokala nätverksanslutningstester.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger skogsnamn att testa mot.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Autentiseringsuppgifter

Användarnamnet och lösenordet för den användare som kör testet.
Det kräver samma nivå av behörigheter som krävs för att köra Azure AD Connect-guiden.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Anger en plats för en loggfil som ska innehålla utdata för den här funktionen.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Ange DC:er att testa mot.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Flagga som gör att ett meddelande kan visas om syftet med den här funktionen.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Returnerar resultatet av denna diagnos i form av ett PSObject.
Inte nödvändigt att ange under manuell interaktion med det här verktyget.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Anger om autentiseringsuppgifterna som användaren har skrivit är giltiga.
Inte nödvändigt att ange under manuell interaktion med det här verktyget.

```yml
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
