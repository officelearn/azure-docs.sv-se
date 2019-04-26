---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-referens | Microsoft Docs'
description: Det här dokumentet innehåller information för ADConnectivityTools.psm1 PowerShell-modulen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
origin.date: 10/19/2018
ms.date: 04/09/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40b4be9aca5243b80151afac0ae221f0d44509c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60454650"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect:  ADConnectivityTools PowerShell-referens

Följande dokumentation innehåller information för ADConnectivityTools.psm1 PowerShell-modul som ingår i Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SAMMANFATTNING

Identifierar lokala Dns-problem.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör tester av lokala Dns-anslutning.
För att konfigurera Active Directory-kopplingen, måste användarna ha både namnmatchning för skogen he\she försöker ansluta till samt som domänkontrollanter som är kopplad till den här skogen.

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

Anger namnet på skogen för att testa mot.

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

#### <a name="-dcs"></a>-Domänkontrollanter

Ange domänkontrollanter för att testa mot.

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

Returnerar resultatet av den här diagnos i form av en PSObject.
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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>SAMMANFATTNING

Anger om det finns en angiven skog.

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

Anger namnet på skogen för att testa mot.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-functionallevel"></a>Bekräfta FunctionalLevel

### <a name="synopsis"></a>SAMMANFATTNING

Verifierar AD-skogens funktionsnivå.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Verifierar att AD skogens funktionsnivå är lika med eller mer än en viss MinAdForestVersion (WindowsServer2003).
Kontot (DOMÄN\användarnamn) och lösenordet kan begäras.

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
Standardvärdet är för den inloggade användaren skog.

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

ForestFQDN målobjektet.

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

Funktionen kommer att använda autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället begär anpassade behörigheter från användaren.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SAMMANFATTNING

Identifierar lokala nätverksanslutningsproblem.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör anslutningstester för lokalt nätverk.

För de lokala nätverkstest måste AAD Connect vara kan kommunicera med de namngivna domänkontrollanterna på portarna 53 (DNS), 88 (Kerberos) och 389 (LDAP) de flesta organisationer kör DNS på sina domänkontrollanter, vilket är anledningen till det här testet ingår för närvarande.
Port 53 ska hoppas över om en annan DNS-server har angetts.

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

#### <a name="-dcs"></a>-Domänkontrollanter

Ange domänkontrollanter för att testa mot.

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

Om användaren inte använder DNS-tjänster som tillhandahålls av AD-plats / inloggning DC och sedan he\she kanske vill hoppa över kontrollerar port 53.
Användaren måste fortfarande kunna lösa _.ldap._tcp. \<forestfqdn\> för Active Directory Connector-konfigurationen ska lyckas.

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

Returnerar resultatet av den här diagnos i form av en PSObject.
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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SAMMANFATTNING

Anger om en angiven skogen och dess associerade domänkontrollanter kan nås.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör pinga ”” tester (om en dator kan nå en måldator via nätverket och/eller internet)

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

Anger namnet på skogen för att testa mot.

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

#### <a name="-dcs"></a>-Domänkontrollanter

Ange domänkontrollanter för att testa mot.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validdomains"></a>Bekräfta ValidDomains

### <a name="synopsis"></a>SAMMANFATTNING

Verifiera att det går att nå domänerna i skogen erhållet FQDN

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Verifiera att alla domäner i skogen erhållet FQDN kan nås genom att försöka hämta DomainGuid och DomainDN.
Kontot (DOMÄN\användarnamn) och lösenordet kan begäras.

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

ForestFQDN målobjektet.

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

Funktionen kommer att använda autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället begär anpassade behörigheter från användaren.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SAMMANFATTNING

Kontrollerar om en användare har autentiseringsuppgifter som företagsadministratör.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Söker om användaren har behörighet för företagsadministratör.
Kontot (DOMÄN\användarnamn) och lösenordet kan begäras.

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

Funktionen kommer att använda autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället begär anpassade behörigheter från användaren.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SAMMANFATTNING

Hämtar en DomainFQDN utanför en kombination av konto och lösenord.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Försöker att hämta ett domainFQDN objekt utanför de angivna autentiseringsuppgifterna.
Om domainFQDN är giltig, en DomainFQDNName eller RootDomainName ska returneras, beroende på användarens val.
Kontot (DOMÄN\användarnamn) och lösenordet kan begäras.

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

Önskat typ av data som ska hämtas.
För närvarande begränsad till ”DomainFQDNName” eller ”RootDomainName”.

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

Funktionen kommer att använda autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället begär anpassade behörigheter från användaren.

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

Extra parameter som används av Start-NetworkConnectivityDiagnosisTools funktion

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SAMMANFATTNING

Hämtar en ForestFQDN utanför en kombination av konto och lösenord.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Försöker att hämta en ForestFQDN utanför de angivna autentiseringsuppgifterna.
Kontot (DOMÄN\användarnamn) och lösenordet kan begäras.

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

Funktionen kommer att använda autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället begär anpassade behörigheter från användaren.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SAMMANFATTNING

Huvudfunktionen.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Körningar de tillgängliga mekanismer som verifierar AD-autentiseringsuppgifter är giltiga.

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

För anpassade installationer: Flagga som är $True om användaren väljer ”Skapa nytt AD-konto” i fönstret AD-Skogskontos AADConnect-guiden.
$False om användaren väljer ”Använd befintligt AD-konto”.
För Express-installationer: Värdet för den här variabeln måste vara $True för Express-installationer.

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

Parameter som förväg fylls fältet för användarnamn när användarens autentiseringsuppgifter har begärts.

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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SAMMANFATTNING

Huvudfunktionen för nätverksanslutning testar.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör anslutningstester för lokalt nätverk.

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

Anger namn på skog att testa mot.

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

#### <a name="-credentials"></a>-Credentials

Användarnamnet och lösenordet för den användare som kör testet.
Det kräver samma nivå av behörigheter som krävs för att köra i Azure AD Connect-guiden.

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

Anger en plats på en loggfil som innehåller utdata från den här funktionen.

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

#### <a name="-dcs"></a>-Domänkontrollanter

Ange domänkontrollanter för att testa mot.

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

Flagga som gör att visa ett meddelande om syftet med den här funktionen.

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

Returnerar resultatet av den här diagnos i form av en PSObject.
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

Anger om användaren har angett autentiseringsuppgifterna är giltiga.
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
Mer information finns i about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

<!-- Update_Description: wording update -->