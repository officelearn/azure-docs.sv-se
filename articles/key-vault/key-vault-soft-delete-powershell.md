---
title: Azure Key Vault - hur du använder mjuk borttagning med PowerShell
description: Använda fallet exempel för mjuk borttagning med PowerShell-kodstycken
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: bryanla
ms.openlocfilehash: 48c471e17fb28843bf61f1591faafc119eb8dec8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002347"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Hur du använder Key Vault mjuk borttagning med PowerShell

Azure Key Vault mjuk borttagning med funktionen kan återställning av borttagna valv och valv objekt. Mer specifikt mjuk borttagning adresser följande scenarier:

- Stöd för återställa borttagningen av ett nyckelvalv
- Stöd för återställa borttagningen av nyckelvalvobjekt. nycklar, hemligheter och certifikat

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 eller senare - om du inte redan det här installationsprogrammet, installera Azure PowerShell och koppla den till din Azure-prenumeration, i [hur du installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Det finns en inaktuell version av våra Key Vault PowerShell-utdata formatering filen som **kan** att läsas in i din miljö i stället för att rätt version. Vi att en uppdaterad version av PowerShell som innehåller nödvändiga korrigeringen för formatering av utdata och uppdaterar det här avsnittet vid den tidpunkten. Den aktuella lösningen bör du stöter på problemet formatering är:
> - Använd följande fråga om du märker du inte ser mjuk borttagning aktiverat egenskapen som beskrivs i det här avsnittet: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Key Vault specifik referensinformation för PowerShell, se [Azure Key Vault PowerShell-referens](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Åtgärder för Key Vault hanteras separat via rollbaserade behörigheter för åtkomstkontroll (RBAC) på följande sätt:

| Åtgärd | Beskrivning | Användarbehörighet |
|:--|:--|:--|
|Visa lista|Listor bort nyckelvalv.|Microsoft.KeyVault/deletedVaults/read|
|Återställ|Återställer ett borttaget nyckelvalv.|Microsoft.KeyVault/vaults/write|
|Rensa|Tar permanent bort ett borttaget nyckelvalv och allt dess innehåll.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Mer information om behörighet och åtkomstkontroll finns i [säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Att aktivera mjuk borttagning

Du aktiverar ”mjuk borttagning” att tillåta återställning av borttagna key vault eller objekt som lagras i ett nyckelvalv.

> [!IMPORTANT]
> Aktiverar ”mjuk borttagning' på key vault är irreversibel. När egenskapen mjuk borttagning har ställts in på ”true”, inte kan ändras eller tas bort.  

### <a name="existing-key-vault"></a>Befintlig key vault

Aktivera mjuk borttagning för ett befintligt nyckelvalv med namnet ContosoVault på följande sätt. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nytt nyckelvalv

Att aktivera mjuk borttagning för ett nytt nyckelvalv görs vid tidpunkten för skapandet genom att aktivera mjuk borttagning flagga för att lägga till din skapa-kommando.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Kontrollera aktivering för mjuk borttagning

Kontrollera att ett nyckelvalv har aktivera mjuk borttagning med den *visa* kommandot och leta efter den ”ej permanent ta bort aktiverad”? attribut:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Tar bort en mjuk borttagning skyddade nyckelvalv

Kommandot för att ta bort ett nyckelvalv ändringar i beteende, beroende på om mjuk borttagning är aktiverat.

> [!IMPORTANT]
>Om du kör följande kommando för ett nyckelvalv som inte har aktiverat mjuk borttagning, ska du ta bort det här nyckelvalvet och allt dess innehåll med inga alternativ för återställning!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar dina nyckelvalv

Aktiverad med mjuk borttagning:

- Ett borttaget nyckelvalv tas bort från en resursgrupp och placeras i ett reserverat namnområde, som är associerade med den plats där den skapades. 
- Borttagna objekt som nycklar, hemligheter och certifikat, är tillgängliga så länge som deras som innehåller nyckelvalvet är i tillståndet deleted. 
- DNS-namnet för ett borttaget nyckelvalv reserveras förhindrar att ett nytt nyckelvalv med samma namn som skapas.  

Du kan visa tillståndet deleted nyckelvalv, som är associerade med din prenumeration med hjälp av följande kommando:

```powershell
PS C:\> Get-AzKeyVault -InRemovedState 
```

- *ID* kan användas för att identifiera resursen när återställa eller rensa. 
- *Resurs-ID* är den ursprungliga resurs-ID för det här valvet. Eftersom det här nyckelvalvet är nu i ett borttaget tillstånd, det finns ingen resurs med resurs-ID. 
- *Schemalagd Rensa datum* är när valvet tas bort permanent, om ingen åtgärd utförs. Standardkvarhållningsperioden som används för att beräkna den *schemalagda Rensa datum*, är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställa ett nyckelvalv

Om du vill återställa ett nyckelvalv måste ange du nyckelvalvsnamn, resursgrupp och plats. Notera platsen och resursgruppen för det borttagna nyckelvalvet som du behöver dem för återställningen.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

När ett nyckelvalv återställs skapas en ny resurs med key vault ursprungliga resurs-ID. Om den ursprungliga resursgruppen tas bort, måste en skapas med samma namn innan du försöker återställning.

## <a name="deleting-and-purging-key-vault-objects"></a>Ta bort och rensa nyckelvalvobjekt

Följande kommando tar bort nyckeln 'ContosoFirstKey' i key vault med namnet ”ContosoVault” som har aktiverat mjuk borttagning:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Med nyckelvalvet som aktiverats för mjuk borttagning, visas en borttagen nyckel fortfarande att tas bort, såvida inte du uttryckligen ange borttagna nycklar. De flesta åtgärder på en nyckel i tillståndet deleted misslyckas förutom lista, återställa, rensa en nyckel som har tagits bort. 

Följande kommando visar till exempel har tagits bort nycklar i nyckelvalvet 'ContosoVault':

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Övergångstillstånd 

När du tar bort en nyckel i key vault med mjuk borttagning aktiverat kan det ta några sekunder för övergången att slutföra. Under den här ändringen kommer verka det att nyckeln inte är i aktivt läge eller Borttaget tillstånd. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Använda mjuk borttagning med key vault-objekt

Precis som nyckelvalv, en borttagna nyckeln eller hemligheten certifikat, finns kvar i Borttaget tillstånd i upp till 90 dagar, såvida du inte återställa den eller ta bort den. 

#### <a name="keys"></a>Nycklar

För att återställa en ej permanent borttagna nyckel:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Att permanent ta bort (även kallat Rensa) en ej permanent borttagna nyckel:

> [!IMPORTANT]
> Rensa en nyckel tar permanent bort den och kommer inte att återställa! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Den **återställa** och **Rensa** åtgärder har sina egna behörigheter som är associerade i en åtkomstprincip för nyckelvalvet. För en användare eller tjänstens huvudnamn för att kunna köra en **återställa** eller **Rensa** åtgärd, de måste ha behörigheten respektive för den här nyckeln eller hemligheten. Som standard **Rensa** inte läggs till en nyckelvalvets åtkomstprincip, när genvägen till ”alla” används för att ge alla behörigheter. Du måste uttryckligen beviljar **Rensa** behörighet. 

#### <a name="set-a-key-vault-access-policy"></a>Ange en åtkomstprincip för nyckelvalvet

Följande kommando ger user@contoso.com behörighet att använda flera åtgärder med nycklar i *ContosoVault* inklusive **Rensa**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Om du har ett befintligt nyckelvalv som har bara aktivera mjuk borttagning, kanske du inte har **återställa** och **Rensa** behörigheter.

#### <a name="secrets"></a>Hemligheter

T.ex. nycklar, som hemligheter hanteras med sina egna kommandon:

- Ta bort en hemlighet med namnet SQLPassword: 
```powershell
Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Lista över alla borttagna hemligheter i key vault: 
```powershell
Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Återställa en hemlighet i tillståndet deleted: 
```powershell
Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Rensa en hemlighet i Borttaget tillstånd: 

  > [!IMPORTANT]
  > Rensa en hemlighet tar permanent bort den och kommer inte att återställa!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Rensa en mjuk borttagning skyddade nyckelvalv

> [!IMPORTANT]
> Rensa key vault eller något av de ingående objekten tas bort permanent den, vilket innebär att den inte kan återställas!

Rensa funktion används för att ta bort ett nyckelvalv-objekt eller en hel nyckelvalv, som tidigare togs bort för mjuk permanent. Som visas i föregående avsnitt, kan objekt som lagras i ett nyckelvalv med mjuk borttagning funktionen är aktiverad, gå igenom olika tillstånd:
- **Aktiva**: före borttagningen.
- **Ej permanent borttagna**: efter borttagningen kan visas och återställas till aktivt läge.
- **Permanent bort**: efter att rensa, kunde inte återställas.


Detsamma gäller för nyckelvalvet. För att permanent ta bort ej permanent borttagna nyckelvalvet och dess innehåll, måste du ta bort själva nyckelvalvet.

### <a name="purging-a-key-vault"></a>Rensa ett nyckelvalv

När ett nyckelvalv tas bort tas hela dess innehåll bort permanent, inklusive nycklar, hemligheter och certifikat. Om du vill rensa ett ej permanent borttagna nyckelvalvet, använda den `Remove-AzKeyVault` med alternativet `-InRemovedState` och genom att ange platsen för borttagna nyckelvalvet med den `-Location location` argumentet. Du kan hitta platsen för ett borttaget valv med hjälp av kommandot `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Rensa behörigheter som krävs
- Om du vill rensa ett borttaget nyckelvalv användaren behöver RBAC-behörighet till den *Microsoft.KeyVault/locations/deletedVaults/purge/action* igen. 
- Om du vill visa ett borttaget nyckelvalv användaren behöver RBAC-behörighet till den *Microsoft.KeyVault/deletedVaults/read* igen. 
- Som standard har bara en administratör för prenumerationen dessa behörigheter. 

### <a name="scheduled-purge"></a>Schemalagda Rensa

Visa en lista över objekt som borttaget nyckelvalv visar även när de är schemalagda tas bort av Key Vault. *Schemalagd Rensa datum* anger när ett nyckelvalv objekt tas bort permanent, om ingen åtgärd utförs. Som standard är kvarhållningsperioden för ett borttaget nyckelvalv objekt 90 dagar.

>[!IMPORTANT]
>En borttagen valvobjekt som utlöses av dess *schemalagda Rensa datum* fältet, bort permanent. Det kan inte återställas!

## <a name="other-resources"></a>Andra resurser

- En översikt över funktionen för mjuk borttagning för Key Vault finns i [Azure Key Vault mjuk borttagning översikt](key-vault-ovw-soft-delete.md).
- En allmän översikt över Azure Key Vault fungerar, se [Kom igång med Azure Key Vault](key-vault-get-started.md).

