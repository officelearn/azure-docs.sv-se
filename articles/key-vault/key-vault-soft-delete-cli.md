---
title: Azure Key Vault - hur du använder mjuk borttagning med CLI
description: Använda fallet exempel för mjuk borttagning med CLI-kodstycken
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mbaldwin
ms.openlocfilehash: aa9b89b9afec069e97236b7652e0f1d37644f5cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640488"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Hur du använder Key Vault mjuk borttagning med CLI

Azure Key Vault mjuk borttagning med funktionen kan återställning av borttagna valv och valv objekt. Mer specifikt mjuk borttagning adresser följande scenarier:

- Stöd för återställa borttagningen av ett nyckelvalv
- Stöd för återställa borttagningen av nyckelvalvobjekt. nycklar, hemligheter och certifikat

## <a name="prerequisites"></a>Nödvändiga komponenter

- Azure CLI - om du inte har konfigurerats för din miljö, se [hantera Nyckelvalv med Azure CLI](key-vault-manage-with-cli2.md).

Key Vault specifik referensinformation för CLI, se [Nyckelvalv för Azure CLI-referens](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nytt nyckelvalv

Att aktivera mjuk borttagning för ett nytt nyckelvalv görs vid tidpunkten för skapandet genom att aktivera mjuk borttagning flagga för att lägga till din skapa-kommando.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Kontrollera aktivering för mjuk borttagning

Kontrollera att ett nyckelvalv har aktivera mjuk borttagning med den *visa* kommandot och leta efter den ”ej permanent ta bort aktiverad”? attribut:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Tar bort en mjuk borttagning skyddade nyckelvalv

Kommandot för att ta bort ett nyckelvalv ändringar i beteende, beroende på om mjuk borttagning är aktiverat.

> [!IMPORTANT]
>Om du kör följande kommando för ett nyckelvalv som inte har aktiverat mjuk borttagning, ska du ta bort det här nyckelvalvet och allt dess innehåll med inga alternativ för återställning!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar dina nyckelvalv

Aktiverad med mjuk borttagning:

- Ett borttaget nyckelvalv tas bort från en resursgrupp och placeras i ett reserverat namnområde, som är associerade med den plats där den skapades. 
- Borttagna objekt som nycklar, hemligheter och certifikat, är tillgängliga så länge som deras som innehåller nyckelvalvet är i tillståndet deleted. 
- DNS-namnet för ett borttaget nyckelvalv reserveras förhindrar att ett nytt nyckelvalv med samma namn som skapas.  

Du kan visa tillståndet deleted nyckelvalv, som är associerade med din prenumeration med hjälp av följande kommando:

```azurecli
az keyvault list-deleted
```
- *ID* kan användas för att identifiera resursen när återställa eller rensa. 
- *Resurs-ID* är den ursprungliga resurs-ID för det här valvet. Eftersom det här nyckelvalvet är nu i ett borttaget tillstånd, det finns ingen resurs med resurs-ID. 
- *Schemalagd Rensa datum* är när valvet tas bort permanent, om ingen åtgärd utförs. Standardkvarhållningsperioden som används för att beräkna den *schemalagda Rensa datum*, är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställa ett nyckelvalv

Om du vill återställa ett nyckelvalv måste ange du nyckelvalvsnamn, resursgrupp och plats. Notera platsen och resursgruppen för det borttagna nyckelvalvet som du behöver dem för återställningen.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

När ett nyckelvalv återställs skapas en ny resurs med key vault ursprungliga resurs-ID. Om den ursprungliga resursgruppen tas bort, måste en skapas med samma namn innan du försöker återställning.

## <a name="deleting-and-purging-key-vault-objects"></a>Ta bort och rensa nyckelvalvobjekt

Följande kommando tar bort nyckeln 'ContosoFirstKey' i key vault med namnet ”ContosoVault” som har aktiverat mjuk borttagning:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Med nyckelvalvet som aktiverats för mjuk borttagning, visas en borttagen nyckel fortfarande som tas den bort utom, när du uttryckligen lista eller hämta borttagna nycklar. De flesta åtgärder på en nyckel i tillståndet deleted misslyckas förutom lista en borttagen nyckel, återställs eller rensa den. 

Till exempel om du vill begära att lista bort nycklar i key vault använder du följande kommando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Övergångstillstånd 

När du tar bort en nyckel i key vault med mjuk borttagning aktiverat kan det ta några sekunder för övergången att slutföra. Under den här ändringen kommer verka det att nyckeln inte är i aktivt läge eller Borttaget tillstånd. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Använda mjuk borttagning med key vault-objekt

Precis som nyckelvalv, en borttagna nyckeln eller hemligheten certifikat, finns kvar i Borttaget tillstånd i upp till 90 dagar, såvida du inte återställa den eller ta bort den.

#### <a name="keys"></a>Nycklar

För att återställa en ej permanent borttagna nyckel:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Att permanent ta bort (även kallat Rensa) en ej permanent borttagna nyckel:

> [!IMPORTANT]
> Rensa en nyckel tar permanent bort den och kommer inte att återställa! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

Den **återställa** och **Rensa** åtgärder har sina egna behörigheter som är associerade i en åtkomstprincip för nyckelvalvet. För en användare eller tjänstens huvudnamn för att kunna köra en **återställa** eller **Rensa** åtgärd, de måste ha behörigheten respektive för den här nyckeln eller hemligheten. Som standard **Rensa** inte läggs till en nyckelvalvets åtkomstprincip, när genvägen till ”alla” används för att ge alla behörigheter. Du måste uttryckligen beviljar **Rensa** behörighet. 

#### <a name="set-a-key-vault-access-policy"></a>Ange en åtkomstprincip för nyckelvalvet

Följande kommando ger user@contoso.com behörighet att använda flera åtgärder med nycklar i *ContosoVault* inklusive **Rensa**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Om du har ett befintligt nyckelvalv som har bara aktivera mjuk borttagning, kanske du inte har **återställa** och **Rensa** behörigheter.

#### <a name="secrets"></a>Hemligheter

T.ex. nycklar, som hemligheter hanteras med sina egna kommandon:

- Ta bort en hemlighet med namnet SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Lista över alla borttagna hemligheter i key vault: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Återställa en hemlighet i tillståndet deleted: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Rensa en hemlighet i Borttaget tillstånd: 

  > [!IMPORTANT]
  > Rensa en hemlighet tar permanent bort den och kommer inte att återställa! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Rensa en mjuk borttagning skyddade nyckelvalv

> [!IMPORTANT]
> Rensa key vault eller något av de ingående objekten tas bort permanent den, vilket innebär att den inte kan återställas!

Rensa-funktionen används för att permanent ta bort ett nyckelvalv-objekt eller en hel nyckelvalvet som har tidigare ej permanent borttagna. Som visas i föregående avsnitt, kan objekt som lagras i ett nyckelvalv med mjuk borttagning funktionen är aktiverad, gå igenom olika tillstånd:

- **Aktiva**: före borttagningen.
- **Ej permanent borttagna**: efter borttagningen kan visas och återställas till aktivt läge.
- **Permanent bort**: efter att rensa, kunde inte återställas.

Detsamma gäller för nyckelvalvet. För att permanent ta bort ej permanent borttagna nyckelvalvet och dess innehåll, måste du ta bort själva nyckelvalvet.

### <a name="purging-a-key-vault"></a>Rensa ett nyckelvalv

När ett nyckelvalv tas bort tas hela dess innehåll bort permanent, inklusive nycklar, hemligheter och certifikat. Om du vill rensa ett ej permanent borttagna nyckelvalvet, använda den `az keyvault purge` kommando. Du kan hitta platsen din prenumeration har tagits bort nyckelvalv med hjälp av kommandot `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Rensa behörigheter som krävs
- Om du vill rensa ett borttaget nyckelvalv användaren behöver RBAC-behörighet till den *Microsoft.KeyVault/locations/deletedVaults/purge/action* igen. 
- Om du vill visa ett borttaget nyckelvalv användaren behöver RBAC-behörighet till den *Microsoft.KeyVault/deletedVaults/read* igen. 
- Som standard har bara en administratör för prenumerationen dessa behörigheter. 

### <a name="scheduled-purge"></a>Schemalagda Rensa

Visa en lista över objekt som borttaget nyckelvalv visar även när de är schemalagda tas bort av Key Vault. *Schemalagd Rensa datum* anger när ett nyckelvalv objekt tas bort permanent, om ingen åtgärd utförs. Som standard är kvarhållningsperioden för ett borttaget nyckelvalv objekt 90 dagar.

>[!IMPORTANT]
>En borttagen valvobjekt som utlöses av dess *schemalagda Rensa datum* fältet, bort permanent. Det kan inte återställas!

## <a name="enabling-purge-protection"></a>Aktivering av Rensa skydd

När rensningsskydd aktiveras, ett valv eller ett objekt i borttagna kan inte tillstånd rensas tills kvarhållningsperiod på 90 dagar har passerat. Sådana valv eller ett objekt kan fortfarande återställas. Den här funktionen ger extra trygghet som ett valv eller ett objekt kan aldrig vara permanent ta bort förrän kvarhållning tidsperiod har passerat.

Du kan aktivera rensningsskydd endast om mjuk borttagning har aktiverats. 

Om du vill aktivera både mjuk borttagning och rensa skydd när du skapar ett valv genom att använda den [az keyvault skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) kommando:

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Om du vill lägga till Rensa skydd i ett befintligt valv (som redan har aktivera mjuk borttagning) använder du den [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) kommando:

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Andra resurser

- En översikt över funktionen för mjuk borttagning för Key Vault finns i [Azure Key Vault mjuk borttagning översikt](key-vault-ovw-soft-delete.md).
- En allmän översikt över Azure Key Vault fungerar, se [vad är Azure Key Vault?](key-vault-overview.md).

