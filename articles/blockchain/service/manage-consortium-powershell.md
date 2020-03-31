---
title: Hantera konsortiemedlemmar i Azure Blockchain Service – PowerShell
description: Lär dig hur du hanterar Azure Blockchain Service-konsortiemedlemmar med hjälp av Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505976"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Hantera konsortiemedlemmar i Azure Blockchain-tjänsten med PowerShell

Du kan använda PowerShell för att hantera blockchain-konsortiemedlemmar för din Azure Blockchain-tjänst. Medlemmar som har administratörsbehörighet kan bjuda in, lägga till, ta bort och ändra roller för alla deltagare i blockchain-konsortiet. Medlemmar som har användarbehörighet kan visa alla deltagare i blockchain-konsortiet och ändra sitt medlemsvisningsnamn.

## <a name="prerequisites"></a>Krav

* Skapa en blockchain-medlem med hjälp av [Azure-portalen](create-member.md).
* Mer information om konsortier, medlemmar och noder finns i [Azure Blockchain Service consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt interaktivt skal som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Du kan också öppna Cloud Shell i en separat webbläsarflik genom att gå till [shell.azure.com/powershell](https://shell.azure.com/powershell). Välj **Kopiera** om du vill kopiera kodblocken, klistra in den i Cloud Shell och välj **Retur** för att köra den.

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Installera Microsoft.AzureBlockchainService.ConsortiumManagement.PS-paketet från PowerShell-galleriet.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Ange informationsinställningar

Du kan få mer information när du kör cmdlets genom att ställa in variabeln för informationsinställningar. Som standard är *$InformationPreference* inställt på *Tyst Kontinuerlig*.

Om du vill ha mer detaljerad information från cmdlets anger du inställningarna i PowerShell enligt följande:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Upprätta en Web3-anslutning

Om du vill hantera konsortiemedlemmar upprättar du en Web3-anslutning till slutpunkten för Blockchain-tjänsten. Du kan använda det här skriptet för att ange globala variabler för att anropa konsortiehanterings cmdlets.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Ersätt * \<lösenord\> för medlemskontot* med det medlemskontolösenord som du använde när du skapade medlemmen.

Hitta de andra värdena i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **standardsidan för översiktssidan för** Blockchain-tjänstmedlem.

    ![Medlemsöversikt](./media/manage-consortium-powershell/member-overview.png)

    Ersätt * \<medlemskonto\> * och * \<\> RootContract-adress* med värdena från portalen.

1. För slutpunktsadressen väljer du **Transaktionsnoder**och väljer sedan **standardtransaktionsnoden**. Standardnoden har samma namn som blockchain-medlemmen.
1. Välj **Anslutningssträngar**.

    ![Anslutningssträngar](./media/manage-consortium-powershell/connection-strings.png)

    Ersätt * \<slutpunktsadress\> * med värdet från **HTTPS (Access-tangent 1)** eller HTTPS **(Access-tangent 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Hantera nätverket och smarta kontrakt

Använd nätverks- och smarta kontraktskort för att upprätta en anslutning till blockchain-slutpunktens smarta kontrakt som ansvarar för konsortiehantering.

### <a name="import-consortiummanagementcontracts"></a>Import-konsortiekontraktKontrakt

Använd den här cmdleten för att ansluta till konsortieledningens smarta kontrakt. Dessa kontrakt används för att hantera och genomdriva medlemmar inom konsortiet.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| RootContractAddress | Rotkontraktsadress för konsortieledningens smarta kontrakt | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importera-Web3Konto

Använd den här cmdleten om du vill skapa ett objekt för att lagra informationen för en fjärrnods hanteringskonto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Kontoadress för Blockchain-medlem | Ja |
| ManagedAccountPassword | Lösenord för kontoadress | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Ny-Web3Anslutning

Använd den här cmdleten för att upprätta en anslutning till RPC-slutpunkten för en transaktionsnod.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Slutpunktsadress för Blockchain-medlem | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Hantera konsortiemedlemmarna

Använd konsortiemedlemshanterings-cmdlets för att hantera medlemmar inom konsortiet. Vilka åtgärder som är tillgängliga beror på din konsortieroll.

### <a name="get-blockchainmember"></a>Få-BlockchainMember

Använd den här cmdleten för att få medlemsinformation eller listmedlemmar i konsortiet.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Namn | Namnet på den Blockchain Service-medlem som du vill hämta information om. När ett namn anges returneras medlemmens information. När ett namn utelämnas returneras en lista över alla konsortiemedlemmar. | Inga |
| Medlemmar | Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection variabel.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Exempel på utdata

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Ta bort BlockchainMember

Använd den här cmdleten för att ta bort en blockchain-medlem.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Namn | Medlemsnamn som ska tas bort | Ja |
| Medlemmar | Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Konto | Web3Account-objekt som hämtats från Import-Web3Account | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection- och $MemberAccount-variabler.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Använd den här cmdleten för att ange blockchain-medlemsattribut, inklusive visningsnamnet och konsortierollen.

Konsortieadministratörer kan ange **DisplayName** och **roll** för alla medlemmar. En konsortiummedlem med användarrollen kan bara ändra sin egen medlems visningsnamn.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Namn | Namn på blockchain-medlemmen | Ja |
| DisplayName | Nytt visningsnamn | Inga |
| KontoAdress | Kontoadress | Inga |
| Medlemmar | Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Konto | Web3Account-objekt som hämtats från Import-Web3Account | Ja |
| Web3Client (Olikartade) |  Web3Client-objekt som erhållits från New-Web3Connection| Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection- och $MemberAccount-variabler.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Hantera konsortiemedlemmarnas inbjudningar

Använd konsortiemedlemsinbjudans hanterings-cmdlets för att hantera konsortiemedlemmarnas inbjudningar. Vilka åtgärder som är tillgängliga beror på din konsortieroll.

### <a name="new-blockchainmemberinvitation"></a>Ny blockchainMemberInvitation

Använd den här cmdleten för att bjuda in nya medlemmar till konsortiet.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in | Ja |
| Roll | Konsortiets roll. Värden kan vara ADMIN eller USER. ADMIN är konsortieadministratörsrollen. USER är konsortiemedlemsrollen. | Ja |
| Medlemmar | Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Konto | Web3Account-objekt som hämtats från Import-Web3Account | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection- och $MemberAccount-variabler.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Få-BlockchainMemberInvitation

Använd den här cmdleten för att hämta eller lista en konsortiemedlems inbjudningsstatus.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in. Om prenumerations-ID:et anges returneras information om prenumerations-ID. Om prenumerations-ID:et utelämnas returneras en lista över alla medlemsinbjudningar. | Inga |
| Medlemmar | Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection variabel.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Exempel på utdata

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Ta bort-BlockchainMemberInvitation

Använd den här cmdleten för att återkalla en konsortiemedlems inbjudan.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att återkalla | Ja |
| Medlemmar | Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Konto | Web3Account-objekt som hämtats från Import-Web3Account | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection- och $MemberAccount-variabler.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Använd den här cmdleten för att ange **rollen** för en befintlig inbjudan. Endast konsortieadministratörer kan ändra inbjudningar.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in | Ja |
| Roll | Ny konsortieroll för inbjudan. Värden kan vara **USER** eller **ADMIN**. | Ja |
| Medlemmar |  Medlemmar objekt som erhållits från Import-ConsortiumManagementContracts | Ja |
| Web3Konto | Web3Account-objekt som hämtats från Import-Web3Account | Ja |
| Web3Client (Olikartade) | Web3Client-objekt som erhållits från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection- och $MemberAccount-variabler.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Nästa steg

Mer information om konsortier, medlemmar och noder finns i [Azure Blockchain Service-konsortiet](consortium.md)
