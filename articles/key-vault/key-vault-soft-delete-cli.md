---
ms.assetid: 
title: "Azure Key Vault - hur du använder mjuk borttagning med CLI"
description: "Använda case exempel på mjuk borttagning med CLI-kodstyckena"
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Hur du använder Key Vault mjuk borttagning med CLI

Funktionen för mjuk borttagning av Azure Key Vault kan återställning av borttagna valv och valvet objekt. Mer specifikt mjuk borttagning adresser i följande scenarier:

- Stöd för återställningsbara borttagningen av ett nyckelvalv
- Stöd för återställningsbara borttagning av objekt i nyckelvalvet. nycklar hemligheter, och certifikat

## <a name="prerequisites"></a>Förutsättningar

- Azure CLI 2.0 - om du inte har den här installationen för din miljö finns [hantera Nyckelvalv med hjälp av CLI 2.0](key-vault-manage-with-cli2.md).

Key Vault specifika referensinformation för CLI, se [Azure CLI 2.0 Key Vault referens](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Key Vault hanteras separat via rollbaserade behörigheter för åtkomstkontroll (RBAC) enligt följande:

| Åtgärd | Beskrivning | Användarbehörighet |
|:--|:--|:--|
|Visa lista|Visar bort nyckelvalv.|Microsoft.KeyVault/deletedVaults/read|
|Återställ|Återställer borttagna nyckelvalvet.|Microsoft.KeyVault/vaults/write|
|Rensa|Tar permanent bort borttagna nyckelvalvet och allt dess innehåll.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Mer information om behörighet och åtkomstkontroll finns [Secure nyckelvalvet](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Aktivera mjuk borttagning

Om du vill kunna återställa borttagna nyckelvalv eller objekt som lagras i ett nyckelvalv måste du först aktivera mjuk borttagning för att nyckelvalvet.

### <a name="existing-key-vault"></a>Befintliga nyckelvalvet

Aktivera mjuk borttagning på följande sätt för en befintlig nyckelvalv med namnet ContosoVault. 

>[!NOTE]
>För närvarande måste du använda Azure Resource Manager resurs manipulering direkt skriva den *enableSoftDelete* egenskap till Key Vault-resurs.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nytt nyckelvalv

Aktivera mjuk borttagning för ett nytt nyckelvalv görs vid skapandet genom att lägga till soft-ta bort flaggan till din skapa-kommando.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Kontrollera mjuk borttagning aktivering

Om du vill kontrollera att ett nyckelvalv har aktiverat soft-delete, kör den *visa* kommandot och leta efter den 'ej permanent ta bort Enabled?' attribut och dess inställning, true eller false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Om du tar bort ett nyckelvalv som skyddas av mjuk borttagning

Kommandot för att ta bort (eller ta bort) en nyckelvalv förblir detsamma, men dess beteende ändras beroende på om du har aktiverat soft-ta bort eller inte.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Om du kör det föregående kommandot för ett nyckelvalv som inte har aktiverat soft-delete tar permanent bort den här nyckelvalvet och allt dess innehåll utan alternativ för återställning.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar ditt nyckelvalv

Aktiverad med mjuk borttagning:

- När en nyckelvalv tas bort, den tas bort från dess resursgrupp och placeras i ett reserverat namnområde som endast som är kopplade till den plats där den skapades. 
- Objekt i en borttagen för valvet, t.ex. nycklar, hemligheter och certifikat, är tillgänglig och förblir det medan sina som innehåller nyckelvalv är i tillståndet deleted. 
- DNS-namn för nyckelvalvet i ett borttaget tillstånd är kvar så det inte går att skapa ett nytt nyckelvalv med samma namn.  

Du kan visa nyckelvalv i Borttaget tillstånd, som är associerade med din prenumeration, med följande kommando:

```azurecli
az keyvault list-deleted
```

Den *resurs-ID* i utdata som refererar till den ursprungliga resurs-ID för det här valvet. Eftersom den här nyckelvalv är nu i ett borttaget tillstånd, det finns någon resurs med resurs-ID. Den *Id* fält kan användas för att identifiera resursen när du återställer eller rensa. Den *schemalagda Rensa datum* anger när valvet tas bort permanent (rensas) om ingen åtgärd utförs för borttagna valvet. Loggperioden som används för att beräkna den *schemalagda Rensa datum*, är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställa en nyckelvalv

Om du vill återställa ett nyckelvalv som du behöver ange nyckelvalv namn, resursgruppens namn och plats. Notera platsen och resursgruppen för borttagna nyckelvalvet som du behöver dem för en nyckelvalv återställningsprocessen.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

När en nyckelvalv återställs är resultatet en ny resurs med det nyckelvalv ursprungliga resurs-ID. Om resursgruppen där nyckelvalvet fanns har tagits bort, måste en ny resursgrupp med samma namn skapas innan nyckelvalvet kan återställas.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault-objekt och Mjuk borttagning

För en nyckel ContosoFirstKey, i en källnyckelvalvets med namnet 'ContosoVault' med mjuk borttagning aktiverad här hur du skulle ta bort nyckeln.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Med nyckelvalvet aktiverad för mjuk borttagning, visas borttagna nyckeln fortfarande som om det har tagits bort utom, när du inte uttryckligen lista eller hämta borttagna nycklar. De flesta åtgärder på en nyckel i tillståndet deleted misslyckas förutom visar en lista över borttagna nyckeln, återställs eller rensa den. 

Till exempel om du vill begära att listan bort nycklar i en nyckelvalvet, använder du följande kommando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Övergångstillstånd 

När du tar bort en nyckel i ett nyckelvalv med aktiverat soft bort kan det ta några sekunder för övergången. Under den här övergångstillstånd kan visas att nyckeln inte är i aktivt läge eller Borttaget tillstånd. Det här kommandot visar en lista över alla borttagna nycklar i nyckelvalvet med namnet 'ContosoVault'.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Hur mjuk borttagning med nyckelvalv objekt

Precis som nyckelvalv, en borttagen nyckel hemlighet eller certifikat finns kvar i Borttaget tillstånd för upp till 90 dagar såvida du inte återställa den eller rensa den. 

#### <a name="keys"></a>Nycklar

För att återställa en borttagen nyckel:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Du ta bort en nyckel:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Rensa en nyckel tar permanent bort, vilket innebär att det inte går att återställa.

Den **återställa** och **Rensa** åtgärder har sina egna behörigheter i en åtkomstprincip för nyckelvalv. För en användare eller tjänstens huvudnamn för att kunna köra en **återställa** eller **Rensa** åtgärd som de måste ha behörigheten respektive för objektet (nyckel eller hemlighet) i åtkomstprincipen nyckelvalvet. Som standard den **Rensa** behörighet läggs inte till ett nyckelvalv åtkomstprincip genvägen till 'all' används för att ge alla behörigheter till en användare. Du måste uttryckligen bevilja **Rensa** behörighet. Till exempel följande kommando ger user@contoso.com behörighet att utföra flera åtgärder med nycklar i *ContosoVault* inklusive **Rensa**.

#### <a name="set-a-key-vault-access-policy"></a>Skapa en princip för nyckelvalvet åtkomst

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Om du har en befintlig nyckelvalv som precis har haft soft-ta bort aktiverat kan du kanske inte har **återställa** och **Rensa** behörigheter.

#### <a name="secrets"></a>Hemligheter

Som nycklar drivs hemligheter i en nyckelvalvet på med sina egna kommandon. Följande, är kommandon för att ta bort, lista, återställa och rensa hemligheter.

- Ta bort en hemlighet som heter SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Lista alla borttagna hemligheter i en nyckelvalvet: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Återställa en hemlighet i Borttaget tillstånd: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Rensa en hemlighet i Borttaget tillstånd: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Rensa en hemlighet tar permanent bort, vilket innebär att det inte går att återställa.

## <a name="purging-and-key-vaults"></a>Ovanstående och nyckel valv

### <a name="key-vault-objects"></a>Nyckelvalv objekt

Rensa en nyckel tar hemlighet eller certifikat permanent bort, vilket innebär att det inte går att återställa. Nyckelvalvet med borttagna objekt förblir dock intakta som kommer alla andra objekt i nyckelvalvet. 

### <a name="key-vaults-as-containers"></a>Nyckel-valv som behållare
När en nyckelvalv rensas tas allt innehåll, inklusive nycklar och hemligheter certifikat bort permanent. Om du vill rensa en key vault använder den `az keyvault purge` kommando. Du kan hitta platsen för din prenumeration borttagna nyckelvalv med hjälp av kommandot `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>Rensa en key vault tar permanent bort, vilket innebär att det inte går att återställa.

### <a name="purge-permissions-required"></a>Rensa behörigheter som krävs
- Om du vill rensa borttagna nyckelvalvet, så att valvet och allt dess innehåll tas bort permanent, måste användaren RBAC-behörighet för att utföra en *Microsoft.KeyVault/locations/deletedVaults/purge/action* igen. 
- Om du vill visa den borttagna nyckeln valvet en användare behöver RBAC-behörighet för att utföra *Microsoft.KeyVault/deletedVaults/read* behörighet. 
- Som standard har bara en prenumeration administratör dessa behörigheter. 

### <a name="scheduled-purge"></a>Schemalagda Rensa

Visar en lista över borttagna nyckelvalv-objekt visar när de är schedled tas bort av Key Vault. Den *schemalagda Rensa datum* fältet visas när ett nyckelvalv objekt tas bort permanent, om ingen åtgärd utförs. Som standard är loggperioden för ett objekt för borttagna nyckelvalv 90 dagar.

>[!NOTE]
>Ett objekt för borttagna valvet utlöses av dess *schemalagda Rensa datum* fältet, bort permanent. Den kan inte återställas.

## <a name="other-resources"></a>Andra resurser

- En översikt över funktionen för mjuk borttagning av Key Vault finns [översikt över Azure Key Vault-soft-ta bort](key-vault-ovw-soft-delete.md).
- En allmän översikt över användning av Azure Key Vault finns [Kom igång med Azure Key Vault](key-vault-get-started.md).

