---
ms.assetid: ''
title: Azure Key Vault - hur du använder mjuk borttagning med CLI
description: Använda fallet exempel för mjuk borttagning med CLI-kodstycken
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: b25d3d7bd5348d4e4ae5dc33362a9d0a2504236e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578610"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Hur du använder Key Vault mjuk borttagning med CLI

Azure Key Vault mjuk borttagning med funktionen kan återställning av borttagna valv och valv objekt. Mer specifikt mjuk borttagning adresser följande scenarier:

- Stöd för återställa borttagningen av ett nyckelvalv
- Stöd för återställa borttagningen av nyckelvalvobjekt. nycklar, hemligheter och certifikat

## <a name="prerequisites"></a>Förutsättningar

- Azure CLI 2.0 - om du inte har den här konfigurationen för din miljö finns i [hantera Key Vault med CLI 2.0](key-vault-manage-with-cli2.md).

Key Vault specifik referensinformation för CLI, se [Nyckelvalv för Azure CLI 2.0-referens](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nytt nyckelvalv

Att aktivera mjuk borttagning för ett nytt nyckelvalv görs vid tidpunkten för skapandet genom att aktivera mjuk borttagning flagga för att lägga till din skapa-kommando.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Kontrollera aktivering för mjuk borttagning

Kontrollera att ett nyckelvalv har aktivera mjuk borttagning med den *visa* kommandot och leta efter den ”ej permanent ta bort aktiverad”? attribut och dess inställning, true eller false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Tar bort ett nyckelvalv som skyddas av mjuk borttagning

Kommandot för att ta bort (eller ta bort) key vault är densamma, men dess beteende ändras beroende på om du har aktiverat mjuk borttagning eller inte.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Om du kör föregående kommando för ett nyckelvalv som inte har aktiverat mjuk borttagning tar permanent bort den här key vault och allt dess innehåll utan några alternativ för återställning.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar dina nyckelvalv

Aktiverad med mjuk borttagning:

- När ett nyckelvalv tas bort, den tas bort från en resursgrupp och placeras i ett reserverat namnområde som endast är kopplade till den plats där den skapades. 
- Objekt i en borttagen nyckel valvet, t.ex. nycklar, hemligheter och certifikat, är tillgängliga och förblir det medan sina som innehåller nyckelvalvet är i tillståndet deleted. 
- DNS-namnet för ett nyckelvalv i ett borttaget tillstånd är fortfarande reserverade så det inte går att skapa ett nytt nyckelvalv med samma namn.  

Du kan visa tillståndet deleted nyckelvalv, som är associerade med din prenumeration med hjälp av följande kommando:

```azurecli
az keyvault list-deleted
```

Den *resurs-ID* i utdata som refererar till den ursprungliga resurs-ID för det här valvet. Eftersom det här nyckelvalvet är nu i ett borttaget tillstånd, det finns ingen resurs med resurs-ID. Den *Id* fältet kan användas för att identifiera resursen när återställa eller rensa. Den *schemalagda Rensa datum* fältet anges när valvet tas bort permanent (rensas) om ingen åtgärd utförs för den här Borttaget valv. Standardkvarhållningsperioden som används för att beräkna den *schemalagda Rensa datum*, är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställa ett nyckelvalv

Om du vill återställa ett nyckelvalv måste du ange namn på key vault, resursgrupp och plats. Notera platsen och resursgruppen för borttagna nyckelvalvet som du behöver dem för ett nyckelvalv återställningsprocessen.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

När ett nyckelvalv återställs är resultatet en ny resurs med key vault ursprungliga resurs-ID. Om resursgruppen där nyckelvalvet fanns har tagits bort, måste en ny resursgrupp med samma namn skapas innan nyckelvalvet kan återställas.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault-objekt och Mjuk borttagning

För en nyckel, ContosoFirstKey, i en nyckelvalvets med namnet 'ContosoVault' med mjuk borttagning aktiverad här hur du tar bort nyckeln.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Med nyckelvalvet som aktiverats för mjuk borttagning, visas en borttagen nyckel fortfarande som tas den bort utom, när du uttryckligen lista eller hämta borttagna nycklar. De flesta åtgärder på en nyckel i tillståndet deleted misslyckas förutom lista en borttagen nyckel, återställs eller rensa den. 

Till exempel om du vill begära att lista bort nycklar i key vault använder du följande kommando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Övergångstillstånd 

När du tar bort en nyckel i key vault med mjuk borttagning aktiverat kan det ta några sekunder för övergången att slutföra. Under den här övergångstillstånd verka det att nyckeln inte är i aktivt läge eller Borttaget tillstånd. Det här kommandot visar en lista över alla borttagna nycklar i nyckelvalvet med namnet ”ContosoVault”.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Använda mjuk borttagning med key vault-objekt

Precis som nyckelvalv, en borttagen nyckel hemlighet eller certifikat finns kvar i Borttaget tillstånd i upp till 90 dagar såvida du inte återställa den eller ta bort den. 

#### <a name="keys"></a>Nycklar

För att återställa en borttagen nyckel:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Ta permanent bort en nyckel:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Rensa en nyckel tar permanent bort den, vilket innebär att den inte kan återställas.

Den **återställa** och **Rensa** åtgärder har sina egna behörigheter som är associerade i en åtkomstprincip för nyckelvalvet. För en användare eller tjänstens huvudnamn för att kunna köra en **återställa** eller **Rensa** åtgärd som de måste ha behörigheten respektive för objektet (nyckel eller hemlighet) i nyckelvalvets åtkomstprincip. Som standard den **Rensa** behörighet läggs inte till en nyckelvalvets åtkomstprincip när genvägen till ”alla” används för att ge alla behörigheter till en användare. Du måste uttryckligen bevilja **Rensa** behörighet. Till exempel följande kommando ger user@contoso.com behörighet att utföra flera åtgärder med nycklar i *ContosoVault* inklusive **Rensa**.

#### <a name="set-a-key-vault-access-policy"></a>Ange en åtkomstprincip för nyckelvalvet

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Om du har ett befintligt nyckelvalv som har bara aktivera mjuk borttagning, kanske du inte har **återställa** och **Rensa** behörigheter.

#### <a name="secrets"></a>Hemligheter

T.ex. nycklar drivs hemligheter i key vault på med sina egna kommandon. Följa, är kommandon för att ta bort, lista, återställa och rensa hemligheter.

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
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Rensa en hemlighet tar permanent bort den, vilket innebär att den inte kan återställas.

## <a name="purging-and-key-vaults"></a>Ovanstående och viktiga valv

### <a name="key-vault-objects"></a>Nyckelvalv-objekt

Rensa en nyckel tar hemlighet eller certifikat permanent bort den, vilket innebär att den inte kan återställas. Nyckelvalvet som innehåller det borttagna objektet förblir dock intakta liksom alla andra objekt i nyckelvalvet. 

### <a name="key-vaults-as-containers"></a>Nyckelvalv som behållare
När ett nyckelvalv tas bort tas allt innehåll, inklusive nycklar, hemligheter och certifikat, bort permanent. Om du vill ta bort ett nyckelvalv, använda den `az keyvault purge` kommando. Du kan hitta platsen din prenumeration har tagits bort nyckelvalv med hjälp av kommandot `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

