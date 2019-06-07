---
title: Hantera medlemmar i en Azure Blockchain Service konsortium med hjälp av Azure PowerShell
description: Lär dig hur du hanterar Azure Blockchain Service consortium medlemmar med hjälp av Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493633"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Hantera consortium medlemmar i Azure Blockchain-tjänsten med hjälp av PowerShell

Du kan använda PowerShell för att hantera blockchain consortium medlemmar för din Azure Blockchain-tjänsten. Medlemmar som har administratörsbehörighet kan bjuda in, lägga till, ta bort och ändra roller för alla deltagare i blockchain-consortium. Medlemmar som har användarbehörigheter som kan visa alla deltagare i blockchain-consortium och ändra deras visningsnamn för medlemmen.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa medlem blockchain med hjälp av den [Azure-portalen](create-member.md).
* Läs mer om konsortium, medlemmar och noder [Azure Blockchain Service consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Du kan också öppna Cloud Shell i en separat webbläsarflik genom att gå till [shell.azure.com/powershell](https://shell.azure.com/powershell). Välj **kopia** klistra in den i Cloud Shell för att kopiera kodblock, och välj **RETUR** att köra den.

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Installera paketet Microsoft.AzureBlockchainService.ConsortiumManagement.PS från PowerShell-galleriet.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Ange inställning för information

Du kan få mer information när du kör cmdletarna genom att ange inställningsvariabeln information. Som standard *$InformationPreference* är inställd på *SilentlyContinue*.

Mer utförlig information från cmdlets anger du inställningen i PowerShell på följande sätt:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Upprätta en anslutning för Web3

För att hantera consortium medlemmar, ansluta Web3 till Blockchain Service medlem slutpunkten. Du kan använda det här skriptet för att ange globala variabler för att anropa consortium management-cmdletar.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Ersätt *\<medlem kontolösenordet\>* med lösenordet för det medlemmen som du använde när du skapade medlemmen.

Hitta de andra värdena i Azure portal:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till Blockchain Service standardmedlem **översikt** sidan.

    ![Översikt över medlem](./media/manage-consortium-powershell/member-overview.png)

    Ersätt *\<medlemskonto\>* och *\<RootContract adress\>* med värden från portalen.

1. Slutpunktsadress, Välj **transaktion noder**, och välj sedan den **transaktion standardnoden**. Standardnoden har samma namn som den blockchain-medlemmen.
1. Välj **anslutningssträngar**.

    ![Anslutningssträngar](./media/manage-consortium-powershell/connection-strings.png)

    Ersätt *\<slutpunktsadress\>* med värdet från **HTTPS (åtkomstnyckel 1)** eller **HTTPS (åtkomstnyckel 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Hantera nätverk och smarta kontrakt

Använda Nätverks- och smarta kontrakt-cmdletar för att upprätta en anslutning till slutpunkten blockchain smarta kontrakt ansvarar för hanteringen av consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Använd denna cmdlet för att ansluta till hanteringen consortium smarta kontrakt. Dessa kontrakt används för att hantera och tillämpa medlemmar i consortium.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| RootContractAddress | Rot kontrakt-adressen för consortium management smarta kontrakt | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Använd denna cmdlet för att skapa ett objekt för att lagra informationen för en fjärrnoden hanteringskontot.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain medlem kontoadress | Ja |
| ManagedAccountPassword | Kontolösenord för IP-adress | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Ny Web3Connection

Använd denna cmdlet för att upprätta en anslutning till RPC-slutpunkt för en transaktion-nod.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain medlem slutpunktsadress | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Hantera consortium-medlemmar

Använda consortium medlem management-cmdletar för att hantera medlemmar i consortium. Åtgärderna som är tillgängliga beror på vilken roll du consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Använd denna cmdlet för att hämta medlemsinformation eller lista över medlemmarna i consortium.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| Namn | Namnet på den Blockchain Service-medlemmen som du vill hämta information om. När ett namn har angetts returnerar medlemmens information. När ett namn utelämnas, returneras en lista över alla consortium medlemmar. | Nej |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

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

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Ta bort medlem blockchain med hjälp av denna cmdlet.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| Namn | Medlemsnamn för att ta bort | Ja |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Använd denna cmdlet för att ställa in blockchain Medlemsattribut, inklusive visningsnamnet och rollen consortium.

Consortium administratörer kan ange **DisplayName** och **rollen** för alla medlemmar. Consortium medlemmar med rollen kan ändra enbart sina egna medlemmens visningsnamn.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| Namn | Namnet på medlemmen blockchain | Ja |
| displayName | Nytt namn | Nej |
| AccountAddress | Kontoadress | Nej |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client |  Web3Client objekt hämtas från New-Web3Connection| Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Hantera consortium medlemmar inbjudningar

Använda consortium medlem inbjudan management-cmdletar för att hantera consortium medlemmar inbjudningar. Åtgärderna som är tillgängliga beror på vilken roll du consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Använd denna cmdlet för att bjuda in nya medlemmar till consortium.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in | Ja |
| Roll | Rollen consortium. Värden kan vara administratör eller användare. ADMINISTRATÖREN kan en administratör för consortium. ANVÄNDAREN är medlemsroller consortium. | Ja |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Använd denna cmdlet för att hämta eller visa status för medlem consortium inbjudan.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| SubscriptionId | ID för medlemmen att bjuda in Azure-prenumeration. Om prenumerationen ID har angetts returnerar prenumerationen ID information om inbjudan. Om prenumerations-ID utelämnas, returnerar en lista över alla medlem inbjudningar. | Nej |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Exempel på utdata

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Använd denna cmdlet för att återkalla inbjudan för medlem consortium.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen som ska återkalla | Ja |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Använd denna cmdlet för att ange den **rollen** för en befintlig inbjudan. Endast consortium administratörer kan ändra inbjudningar.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beskrivning | Obligatoriskt |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in | Ja |
| Roll | Ny consortium roll för inbjudan. Värdena kan vara **användaren** eller **ADMIN**. | Ja |
| Medlemmar |  Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Nästa steg

Mer information om konsortium, medlemmar och noder finns:

> [!div class="nextstepaction"]
> [Azure Blockchain Service consortium](consortium.md)
