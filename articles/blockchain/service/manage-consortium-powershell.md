---
title: Azure Blockchain Service consortium-hantering med hjälp av PowerShell
description: Så här hanterar du Azure Blockchain Service consortium medlemmar med hjälp av PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028237"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Hantera consortium medlemmar i Azure Blockchain-tjänsten med hjälp av PowerShell

Du kan använda PowerShell för att hantera blockchain consortium medlemmar för din Azure Blockchain-tjänsten. Medlemmar med en administratörsbehörighet kan bjuda in, lägga till, ta bort, och ändrar roller för alla deltagare i blockchain-consortium. Medlemmar med behörigheten användare kan visa alla deltagare i blockchain-consortium och ändra deras visningsnamn för medlemmen.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa en blockchain-medlem med Azure portal](create-member.md)
* Läs mer om konsortium, medlemmar och noder [Azure Blockchain Service consortium](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="install-powershell-module"></a>Installera PowerShell-modulen

Installera paketet Microsoft.AzureBlockchainService.ConsortiumManagement.PS från PowerShell-galleriet.

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Upprätta en anslutning för Web3

För att hantera consortium medlemmar, måste du ansluta till din Azure Blockchain medlem tjänstslutpunkt Web3. Du kan använda det här skriptet för att ange globala variabler som kan användas när du anropar consortium management-cmdletar.

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Ersätt \<medlem kontolösenordet\> med medlem kontolösenordet som används när du skapade medlemmen.

Hitta de andra värdena i Azure portal:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din Azure Blockchain Service standardmedlemmen **översikt** sidan.

    ![Översikt över medlem](./media/manage-consortium-powershell/member-overview.png)

    Ersätt \<medlemskonto\>, och \<RootContract adress\> med värden från portalen.

1. Slutpunktsadress, Välj **transaktion noder** och välj en transaktion.
1. Välj **anslutningssträngar**.

    ![Anslutningssträngar](./media/manage-consortium-powershell/connection-strings.png)

    Ersätt \<slutpunktsadress\> med värdet från **HTTPS (åtkomstnyckel 1)** eller **HTTPS (åtkomstnyckel 2)**.

## <a name="network-and-smart-contract-management"></a>Nätverks- och smarta kontrakt

Använda Nätverks- och smarta kontrakt-cmdletar för att upprätta en anslutning till dina blockkedjor endpoint smarta kontrakt ansvarar för hanteringen av consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Ansluter till de consortium management smarta kontrakt, som används för att hantera och tillämpa medlemmar i consortium.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| RootContractAddress | Kontrakt-adressen för rot för consortium management smarta kontrakt | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Använd denna cmdlet för att skapa ett objekt för att lagra information fjärrnoden management-konto.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain medlem kontoadress | Ja |
| ManagedAccountPassword | Kontolösenord för IP-adress | Ja |

**Exempel**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Ny Web3Connection

Upprättar en anslutning till RPC-slutpunkt för en transaktion-nod.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain medlem slutpunktsadress | Ja |


**Exempel**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Hantering av Consortium medlem

Använda consortium medlem management-cmdletar för att hantera medlemmar i consortium. Tillgängliga åtgärder beror på vilken roll du consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Hämtar medlemsinformation eller lista medlemmarna i consortium.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Namn | Namnet på medlemmen Azure Blockchain-tjänsten som du vill hämta information på. Om du anger ett medlemsnamn kan returneras information om medlemmen. Om namnet utelämnas, returneras en lista över alla consortium medlemmar. | Nej |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Exempel på utdata**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Tar bort en blockchain-medlem.

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Namn | Medlemsnamn för att ta bort | Ja |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Anger blockchain Medlemsattribut, bland annat visa namn och consortium rollen.

Consortium administratörer kan ange **DisplayName** och **rollen** för alla medlemmar. Consortium medlem med användarrollen kan bara ändra sina egna medlemmens visningsnamn.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Namn | Namnet på medlemmen blockchain | Ja |
| DisplayName | Nytt namn | Nej |
| AccountAddress | Kontoadress | Nej |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client |  Web3Client objekt hämtas från New-Web3Connection| Ja |

**Exempel**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Hantering av Consortium medlem inbjudan

Använda consortium medlem inbjudan management-cmdletar för att hantera consortium medlem inbjudningar. Tillgängliga åtgärder beror på vilken roll du consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Bjuda in nya medlemmar till consortium.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för inbjudna medlemmar | Ja |
| Roll | Consortium roll. Värden kan vara administratör eller användare. ADMINISTRATÖREN kan en administratör för consortium. ANVÄNDAREN är medlemsroller consortium. | Ja |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Hämtar eller listar consortium medlemsstatusen inbjudan.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för inbjudna medlemmar. Om prenumerations-ID har angetts och returneras information om inbjudan för prenumerations-ID. Om SubscriptionID utelämnas, returneras en lista över alla medlem inbjudningar. | Nej |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Exempel på utdata**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Återkallar en consortium medlem inbjudan.

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure prenumerations-ID för att kunna återkalla | Ja |
| Medlemmar | Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Anger den **rollen** för en befintlig inbjudan. Endast consortium administratörer kan ändra inbjudningar.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för inbjudna medlemmar | Ja |
| Roll | Ny consortium roll för inbjudan. Värdena kan vara **användaren** eller **ADMIN** | Ja |
| Medlemmar |  Medlemmar objekt hämtas från Import ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account objekt hämtas från Import Web3Account | Ja |
| Web3Client | Web3Client objekt hämtas från New-Web3Connection | Ja |

**Exempel**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Nästa steg

Mer information om konsortium, medlemmar och noder finns:

> [!div class="nextstepaction"]
> [Azure Blockchain Service consortium](consortium.md)