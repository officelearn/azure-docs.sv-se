---
title: Azure Key Vault - Så här använder du mjuk borttagning med PowerShell
description: Använda fallexempel på mjuk borttagning med PowerShell-kodklipp
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 52feeb67681bacb64cd20601e00f00109a4b810f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257920"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Så här använder du Key Vault mjuk borttagning med PowerShell

Azure Key Vaults mjuka borttagningsfunktion gör det möjligt att återställa borttagna valv och valvobjekt. I synnerhet tar mjuk borttagning upp följande scenarier:

- Stöd för borttagning av nyckelvalv som kan återställas
- Stöd för återställningsbar borttagning av nyckelvalvsobjekt. nycklar, hemligheter och certifikat

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 eller senare - Om du inte redan har den här installationen installerar du Azure PowerShell och associerar det med din Azure-prenumeration, se [Så här installerar och konfigurerar](https://docs.microsoft.com/powershell/azure/overview)du Azure PowerShell . 

>[!NOTE]
> Det finns en föråldrad version av vår Key Vault PowerShell-utdataformateringsfil som **kan** läsas in i din miljö i stället för rätt version. Vi räknar med en uppdaterad version av PowerShell för att innehålla den nödvändiga korrigeringen för utdataformateringen och kommer att uppdatera det här avsnittet vid den tidpunkten. Den aktuella lösningen, om du stöter på det här formateringsproblemet, är:
> - Använd följande fråga om du märker att du inte ser egenskapen för `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`mjuk borttagning som beskrivs i det här avsnittet: .


Specifik referensinformation för Key Vault för PowerShell finns i [Azure Key Vault PowerShell-referens](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Key Vault-åtgärder hanteras separat via RBAC-behörigheter (Role-Based Access Control) enligt följande:

| Åtgärd | Beskrivning | Användarbehörighet |
|:--|:--|:--|
|Visa lista|Visar borttagna nyckelvalv.|Microsoft.KeyVault/deletedVaults/read Microsoft.KeyVault/deletedVaults/read Microsoft.KeyVault/deletedVaults/read Microsoft.|
|Återställ|Återställer ett borttaget nyckelvalv.|Microsoft.KeyVault/valv/skrivning|
|Rensa|Tar permanent bort ett borttaget nyckelvalv och allt dess innehåll.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Mer information om behörigheter och åtkomstkontroll finns i [Skydda nyckelvalvet](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Aktivera mjuk borttagning

Du aktiverar "soft-delete" för att tillåta återställning av ett borttaget nyckelvalv eller objekt som lagras i ett nyckelvalv.

> [!IMPORTANT]
> Att aktivera "mjuk borttagning" på ett nyckelvalv är en oåterkallelig åtgärd. När egenskapen soft-delete har ställts in på "true" kan den inte ändras eller tas bort.  

### <a name="existing-key-vault"></a>Befintligt nyckelvalv

För ett befintligt nyckelvalv med namnet ContosoVault aktiverar du mjukborttagning enligt följande. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nytt nyckelvalv

Aktivera mjuk borttagning för ett nytt nyckelvalv görs vid skapandetillfället genom att lägga till flaggan för mjuk borttagning i ditt skapa-kommando.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Verifiera mjuk borttagningsaktivering

Om du vill kontrollera att ett nyckelvalv har aktiverat mjukt borttagning kör du *kommandot visa* och letar efter alternativet "Mjuk borttagning aktiverad?" Attributet:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Ta bort ett mjukt borttagningsbeskyddat nyckelvalv

Kommandot för att ta bort ett nyckelvalv ändras i beteendet, beroende på om mjuk borttagning är aktiverat.

> [!IMPORTANT]
>Om du kör följande kommando för ett nyckelvalv som inte har mjuk-delete aktiverat, kommer du att permanent ta bort denna nyckel valv och allt dess innehåll utan alternativ för återhämtning!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar dina nyckelvalv

Med mjuk borttagning aktiverat:

- Ett borttaget nyckelvalv tas bort från resursgruppen och placeras i ett reserverat namnområde som är associerat med den plats där det skapades. 
- Borttagna objekt som nycklar, hemligheter och certifikat är inte tillgängliga så länge de innehåller nyckelvalvet är i borttaget tillstånd. 
- DNS-namnet för ett borttaget nyckelvalv är reserverat, vilket förhindrar att ett nytt nyckelvalv med samma namn skapas.  

Du kan visa borttagna tillståndsnyckelvalv som är associerade med din prenumeration med följande kommando:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* kan användas för att identifiera resursen när du återställer eller rensar. 
- *Resurs-ID* är det ursprungliga resurs-ID:t för det här valvet. Eftersom det här nyckelvalvet nu är i ett borttaget tillstånd finns det ingen resurs med det resurs-ID:et. 
- *Planerat rensningsdatum* är när valvet tas bort permanent, om ingen åtgärd vidtas. Standardkvarhållningsperioden, som används för att beräkna det *schemalagda rensningsdatumet,* är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställa ett nyckelvalv

Om du vill återställa ett nyckelvalv anger du nyckelvalvets namn, resursgrupp och plats. Observera platsen och resursgruppen för det borttagna nyckelvalvet, eftersom du behöver dem för återställningsprocessen.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

När ett nyckelvalv återställs skapas en ny resurs med nyckelvalvets ursprungliga resurs-ID. Om den ursprungliga resursgruppen tas bort måste en skapas med samma namn innan återställningen försöker återställas.

## <a name="deleting-and-purging-key-vault-objects"></a>Ta bort och rensa nyckelvalvsobjekt

Följande kommando kommer att ta bort "ContosoFirstKey"-tangenten i ett nyckelvalv med namnet "ContosoVault", som har mjuk borttagning aktiverat:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

När nyckelvalvet är aktiverat för mjuk borttagning verkar en borttagen nyckel fortfarande tas bort, såvida du inte uttryckligen listar borttagna nycklar. De flesta åtgärder på en nyckel i det borttagna tillståndet misslyckas, förutom att lista, återställa, rensa en borttagen nyckel. 

Följande kommando visar till exempel borttagna nycklar i nyckelvalvet ContosoVault:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Övergångstillstånd 

När du tar bort en nyckel i ett nyckelvalv med mjuk borttagning aktiverat kan det ta några sekunder innan övergången slutförs. Under den här övergången kan det verka som om nyckeln inte är i aktivt tillstånd eller det borttagna tillståndet. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Använda mjuk borttagning med nyckelvalvsobjekt

Precis som nyckelvalv förblir en borttagen nyckel, hemlig eller ett certifikat i borttaget tillstånd i upp till 90 dagar, såvida du inte återställer den eller rensar den. 

#### <a name="keys"></a>Nycklar

Så här återställer du en mjuk borttagen nyckel:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Så här tar du bort (kallas även rensning) permanent en borttagen nyckel:

> [!IMPORTANT]
> Rensa en nyckel kommer permanent att ta bort den, och det kommer inte att kunna återvinnas! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**Återställnings-** och **rensningsåtgärderna** har sina egna behörigheter som är associerade i en princip för nyckelvalvsåtkomst. För att en användare eller tjänsthuvudnamn ska kunna utföra en **återställnings-** eller **rensningsåtgärd** måste de ha respektive behörighet för nyckeln eller hemligheten. Som standard läggs **rensning** inte till i en nyckelvalvets åtkomstprincip när "alla"-genvägen används för att bevilja alla behörigheter. Du måste särskilt bevilja **rensningsbehörighet.** 

#### <a name="set-a-key-vault-access-policy"></a>Ange en princip för nyckelvalvsåtkomst

Följande kommando user@contoso.com ger behörighet att använda flera åtgärder på nycklar i *ContosoVault,* inklusive **rensning:**

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Om du har ett befintligt nyckelvalv som just har aktiverats för mjuk borttagning kanske du inte har **behörigheter för återställning** och **rensning.**

#### <a name="secrets"></a>Hemligheter

Precis som nycklar hanteras hemligheter med egna kommandon:

- Ta bort en hemlighet med namnet SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Lista alla borttagna hemligheter i ett nyckelvalv: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Återställa en hemlighet i det borttagna tillståndet: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Rensa en hemlighet i borttaget tillstånd: 

  > [!IMPORTANT]
  > Rensa en hemlighet kommer att permanent ta bort den, och det kommer inte att kunna återvinnas!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certifikat

Du kan hantera certifikat med hjälp av nedanstående kommandon:

- Ta bort ett certifikat med namnet SQLPassword: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Lista alla borttagna certifikat i ett nyckelvalv: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Återställa ett certifikat i borttaget tillstånd: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Rensa ett certifikat i borttaget tillstånd: 

  > [!IMPORTANT]
  > Rensa ett certifikat kommer att permanent ta bort det, och det kommer inte att kunna återställas!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Rensa ett mjukt bortreda skyddade nyckelvalv

> [!IMPORTANT]
> Rensa en nyckel valv eller en av dess innehöll objekt, kommer permanent bort det, vilket innebär att det inte kommer att kunna återvinnas!

Rensningsfunktionen används för att permanent ta bort ett nyckelvalvsobjekt eller ett helt nyckelvalv, som tidigare togs bort mjukt. Som visas i föregående avsnitt kan objekt som lagras i ett nyckelvalv med funktionen för mjuk borttagning aktiveras gå igenom flera lägen:
- **Aktiv**: före borttagning.
- **Mjuk borttagen:** efter borttagning, kan listas och återställas tillbaka till aktivt tillstånd.
- **Permanent borttagen**: efter rensning, inte kunna återställas.


Detsamma gäller för nyckelvalvet. Om du vill ta bort ett mjukt borttaget nyckelvalv och dess innehåll måste du rensa själva nyckelvalvet.

### <a name="purging-a-key-vault"></a>Rensa ett nyckelvalv

När ett nyckelvalv rensas tas hela dess innehåll bort permanent, inklusive nycklar, hemligheter och certifikat. Om du vill rensa ett mjukt `Remove-AzKeyVault` borttaget `-InRemovedState` nyckelvalv använder du kommandot med alternativet `-Location location` och anger platsen för det borttagna nyckelvalvet med argumentet. Du hittar platsen för ett borttaget `Get-AzKeyVault -InRemovedState`valv med kommandot .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Rensa behörigheter som krävs
- För att rensa ett borttaget nyckelvalv behöver användaren RBAC-behörighet till *åtgärden Microsoft.KeyVault/locations/deletedVaults/purge/action.* 
- Om du vill visa ett borttaget nyckelvalv behöver användaren RBAC-behörighet till *åtgärden Microsoft.KeyVault/deletedVaults/read.* 
- Som standard har endast en prenumerationsadministratör dessa behörigheter. 

### <a name="scheduled-purge"></a>Schemalagd rensning

Om du listar borttagna nyckelvalvsobjekt visas också när de är schemalagda att rensas av Key Vault. *Schemalagt rensningsdatum* anger när ett nyckelvalvsobjekt tas bort permanent, om ingen åtgärd vidtas. Som standard är kvarhållningsperioden för ett borttaget nyckelvalvsobjekt 90 dagar.

>[!IMPORTANT]
>Ett rensat valvobjekt, som utlöses av fältet *Planerat rensningsdatum,* tas bort permanent. Det är inte återvinningsbart!

## <a name="enabling-purge-protection"></a>Aktivera rensningsskydd

När rensningsskydd är aktiverat kan ett valv eller ett objekt i borttaget tillstånd inte rensas förrän kvarhållningsperioden på 90 dagar har passerats. Ett sådant valv eller objekt kan fortfarande återställas. Den här funktionen ger ytterligare en försäkran om att ett valv eller ett objekt aldrig kan tas bort permanent förrän kvarhållningsperioden har passerats.

Du kan bara aktivera rensningsskydd om mjuk borttagning också är aktiverat. 

Om du vill aktivera både mjukt borttagnings- och rensningsskydd när du skapar ett valv använder du cmdleten [New-AzKeyVault:](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0)

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Om du vill lägga till rensningsskydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du cmdletsna [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)och [Set-AzResource:](/powershell/module/az.resources/set-azresource?view=azps-1.5.0)

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Andra resurser

- En översikt över Key Vaults mjukborttagningsfunktion finns i [Azure Key Vaults översikt över mjukborttagning](key-vault-ovw-soft-delete.md).
- En allmän översikt över användningen av Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-overview.md).
