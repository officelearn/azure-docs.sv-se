---
ms.assetid: ''
title: Azure Key Vault - hur du använder mjuk borttagning med PowerShell
description: Använda fallet exempel för mjuk borttagning med PowerShell-kodstycken
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2017
ms.author: bryanla
ms.openlocfilehash: 93105210267ebadf4273db56e2e147b1b34485e3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298139"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Hur du använder Key Vault mjuk borttagning med PowerShell

Azure Key Vault mjuk borttagning med funktionen kan återställning av borttagna valv och valv objekt. Mer specifikt mjuk borttagning adresser följande scenarier:

- Stöd för återställa borttagningen av ett nyckelvalv
- Stöd för återställa borttagningen av nyckelvalvobjekt. nycklar, hemligheter och certifikat

## <a name="prerequisites"></a>Förutsättningar

- Azure PowerShell 4.0.0 eller senare - om du inte redan det här installationsprogrammet, installera Azure PowerShell och koppla den till din Azure-prenumeration, i [hur du installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Det finns en inaktuell version av våra Key Vault PowerShell-utdata formatering filen som **kan** att läsas in i din miljö i stället för att rätt version. Vi att en uppdaterad version av PowerShell som innehåller nödvändiga korrigeringen för formatering av utdata och uppdaterar det här avsnittet vid den tidpunkten. Den aktuella lösningen bör du stöter på problemet formatering är:
> - Använd följande fråga om du märker du inte ser mjuk borttagning aktiverat egenskapen som beskrivs i det här avsnittet: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Key Vault specifika refernece information för PowerShell finns i [Azure Key Vault PowerShell-referens](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Åtgärder för Key Vault hanteras separat via rollbaserade behörigheter för åtkomstkontroll (RBAC) på följande sätt:

| Åtgärd | Beskrivning | Användarbehörighet |
|:--|:--|:--|
|Visa lista|Listor bort nyckelvalv.|Microsoft.KeyVault/deletedVaults/read|
|Återställ|Återställer ett borttaget nyckelvalv.|Microsoft.KeyVault/vaults/write|
|Rensa|Tar permanent bort ett borttaget nyckelvalv och allt dess innehåll.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Mer information om behörighet och åtkomstkontroll finns i [säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Att aktivera mjuk borttagning

Om du vill kunna återställa ett borttaget nyckelvalv eller objekt som lagras i ett nyckelvalv måste du först aktivera mjuk borttagning för det nyckelvalvet.

### <a name="existing-key-vault"></a>Befintlig key vault

Aktivera mjuk borttagning för ett befintligt nyckelvalv med namnet ContosoVault på följande sätt. 

>[!NOTE]
>För närvarande måste du använda Azure Resource Manager resource manipulering för att skriva direkt i *enableSoftDelete* egenskapen till Key Vault-resursen.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nytt nyckelvalv

Att aktivera mjuk borttagning för ett nytt nyckelvalv görs vid tidpunkten för skapandet genom att aktivera mjuk borttagning flagga för att lägga till din skapa-kommando.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Kontrollera aktivering för mjuk borttagning

Kontrollera att ett nyckelvalv har aktivera mjuk borttagning med den *hämta* kommandot och leta efter den ”ej permanent ta bort aktiverad”? attribut och dess inställning, true eller false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Tar bort ett nyckelvalv som skyddas av mjuk borttagning

Kommandot för att ta bort (eller ta bort) key vault är densamma, men dess beteende ändras beroende på om du har aktiverat mjuk borttagning eller inte.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>Om du kör föregående kommando för ett nyckelvalv som inte har aktiverat mjuk borttagning tar permanent bort den här key vault och allt dess innehåll utan några alternativ för återställning.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar dina nyckelvalv

Aktiverad med mjuk borttagning:

- När ett nyckelvalv tas bort, den tas bort från en resursgrupp och placeras i ett reserverat namnområde som endast är kopplade till den plats där den skapades. 
- Objekt i en borttagen nyckel valvet, t.ex. nycklar, hemligheter och certifikat, är tillgängliga och förblir det medan sina som innehåller nyckelvalvet är i tillståndet deleted. 
- DNS-namnet för ett nyckelvalv i ett borttaget tillstånd är fortfarande reserverade så det inte går att skapa ett nytt nyckelvalv med samma namn.  

Du kan visa tillståndet deleted nyckelvalv, som är associerade med din prenumeration med hjälp av följande kommando:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

Den *resurs-ID* i utdata som refererar till den ursprungliga resurs-ID för det här valvet. Eftersom det här nyckelvalvet är nu i ett borttaget tillstånd, det finns ingen resurs med resurs-ID. Den *Id* fältet kan användas för att identifiera resursen när återställa eller rensa. Den *schemalagda Rensa datum* fältet anges när valvet tas bort permanent (rensas) om ingen åtgärd utförs för den här Borttaget valv. Standardkvarhållningsperioden som används för att beräkna den *schemalagda Rensa datum*, är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställa ett nyckelvalv

Om du vill återställa ett nyckelvalv måste du ange namn på key vault, resursgrupp och plats. Notera platsen och resursgruppen för borttagna nyckelvalvet som du behöver dem för ett nyckelvalv återställningsprocessen.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

När ett nyckelvalv återställs är resultatet en ny resurs med key vault ursprungliga resurs-ID. Om resursgruppen där nyckelvalvet fanns har tagits bort, måste en ny resursgrupp med samma namn skapas innan nyckelvalvet kan återställas.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault-objekt och Mjuk borttagning

För en nyckel, ContosoFirstKey, i en nyckelvalvets med namnet 'ContosoVault' med mjuk borttagning aktiverad här hur du tar bort nyckeln.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Med nyckelvalvet som aktiverats för mjuk borttagning, visas en borttagen nyckel fortfarande som tas den bort utom, när du uttryckligen lista eller hämta borttagna nycklar. De flesta åtgärder på en nyckel i tillståndet deleted misslyckas förutom lista en borttagen nyckel, återställs eller rensa den. 

Till exempel om du vill begära att lista bort nycklar i key vault använder du följande kommando:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Övergångstillstånd 

När du tar bort en nyckel i key vault med mjuk borttagning aktiverat kan det ta några sekunder för övergången att slutföra. Under den här övergångstillstånd verka det att nyckeln inte är i aktivt läge eller Borttaget tillstånd. Det här kommandot visar en lista över alla borttagna nycklar i nyckelvalvet med namnet ”ContosoVault”.

```powershell
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Använda mjuk borttagning med key vault-objekt

Precis som nyckelvalv, en borttagen nyckel hemlighet eller certifikat finns kvar i Borttaget tillstånd i upp till 90 dagar såvida du inte återställa den eller ta bort den. 

#### <a name="keys"></a>Nycklar

För att återställa en borttagen nyckel:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Ta permanent bort en nyckel:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>Rensa en nyckel tar permanent bort den, vilket innebär att den inte kan återställas.

Den **återställa** och **Rensa** åtgärder har sina egna behörigheter som är associerade i en åtkomstprincip för nyckelvalvet. För en användare eller tjänstens huvudnamn för att kunna köra en **återställa** eller **Rensa** åtgärd som de måste ha behörigheten respektive för objektet (nyckel eller hemlighet) i nyckelvalvets åtkomstprincip. Som standard den **Rensa** behörighet läggs inte till en nyckelvalvets åtkomstprincip när genvägen till ”alla” används för att ge alla behörigheter till en användare. Du måste uttryckligen bevilja **Rensa** behörighet. Till exempel följande kommando ger user@contoso.com behörighet att utföra flera åtgärder med nycklar i *ContosoVault* inklusive **Rensa**.

#### <a name="set-a-key-vault-access-policy"></a>Ange en åtkomstprincip för nyckelvalvet

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Om du har ett befintligt nyckelvalv som har bara aktivera mjuk borttagning, kanske du inte har **återställa** och **Rensa** behörigheter.

#### <a name="secrets"></a>Hemligheter

T.ex. nycklar drivs hemligheter i key vault på med sina egna kommandon. Följa, är kommandon för att ta bort, lista, återställa och rensa hemligheter.

- Ta bort en hemlighet med namnet SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Lista över alla borttagna hemligheter i key vault: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Återställa en hemlighet i tillståndet deleted: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Rensa en hemlighet i Borttaget tillstånd: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>Rensa en hemlighet tar permanent bort den, vilket innebär att den inte kan återställas.

## <a name="purging-and-key-vaults"></a>Ovanstående och viktiga valv

### <a name="key-vault-objects"></a>Nyckelvalv-objekt

Rensa en nyckel tar hemlighet eller certifikat permanent bort den, vilket innebär att den inte kan återställas. Nyckelvalvet som innehåller det borttagna objektet förblir dock intakta liksom alla andra objekt i nyckelvalvet. 

### <a name="key-vaults-as-containers"></a>Nyckelvalv som behållare
När ett nyckelvalv tas bort tas allt innehåll, inklusive nycklar, hemligheter och certifikat, bort permanent. Om du vill ta bort ett nyckelvalv, använda den `Remove-AzureRmKeyVault` med alternativet `-InRemovedState` och genom att ange platsen för borttagna nyckelvalvet med den `-Location location` argumentet. Du kan hitta platsen för ett borttaget valv med hjälp av kommandot `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Rensning av key vault tar permanent bort den, vilket innebär att den inte kan återställas.

### <a name="purge-permissions-required"></a>Rensa behörigheter som krävs
- Om du vill rensa ett borttaget nyckelvalv så att valvet och allt dess innehåll tas bort permanent, användaren behöver RBAC-behörighet att utföra en *Microsoft.KeyVault/locations/deletedVaults/purge/action* igen. 
- Om du vill visa nyckeln har tagits bort valvet en användare behöver RBAC-behörighet att utföra *Microsoft.KeyVault/deletedVaults/read* behörighet. 
- Som standard har bara en administratör för prenumerationen dessa behörigheter. 

### <a name="scheduled-purge"></a>Schemalagda Rensa

Visa en lista över dina Borttaget nyckelvalvobjekt visas när de är schedled tas bort av Key Vault. Den *schemalagda Rensa datum* fältet visas när ett nyckelvalv objekt tas bort permanent, om ingen åtgärd utförs. Som standard är kvarhållningsperioden för ett borttaget nyckelvalv objekt 90 dagar.

>[!NOTE]
>En borttagen valvobjekt som utlöses av dess *schemalagda Rensa datum* fältet, bort permanent. Det går inte att återställa.

## <a name="other-resources"></a>Andra resurser

- En översikt över funktionen för mjuk borttagning för Key Vault finns i [Azure Key Vault mjuk borttagning översikt](key-vault-ovw-soft-delete.md).
- En allmän översikt över Azure Key Vault fungerar, se [Kom igång med Azure Key Vault](key-vault-get-started.md).

