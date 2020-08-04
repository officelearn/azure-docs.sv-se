---
title: Lägga till lagring till en Azure HPC-cache
description: Definiera lagrings mål så att Azure HPC-cachen kan använda ditt lokala NFS-system eller Azure Blob-behållare för långsiktig fil lagring
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7ad910823c4dd2430aeae085dd8e510fcd42c80f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87532602"
---
# <a name="add-storage-targets"></a>Lägga till lagringsmål

*Lagrings målen* är Server dels lagring för filer som nås via en Azure HPC-cache. Du kan lägga till NFS-lagring (t. ex. ett lokalt maskin varu system) eller lagra data i Azure blob.

Du kan definiera upp till tio olika lagrings mål för ett cacheminne. Cachen visar alla lagrings mål i ett sammanlagt namn område.

Kom ihåg att lagrings exporten måste vara tillgänglig från cachens virtuella nätverk. För lokal maskin varu lagring kan du behöva konfigurera en DNS-server som kan matcha värdnamn för NFS-lagrings åtkomst. Läs mer i [DNS-åtkomst](hpc-cache-prerequisites.md#dns-access).

Lägg till lagrings mål när du har skapat din cache. Proceduren skiljer sig något beroende på om du lägger till Azure Blob Storage eller en NFS-export. Information om var och en finns nedan.

Klicka på bilden nedan om du vill titta på en [video demonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) av hur du skapar en cache och lägger till ett lagrings mål från Azure Portal.

[![video miniatyr: Azure HPC cache: Setup (Klicka för att besöka video sidan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="view-storage-targets"></a>Visa lagrings mål

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna din cache-instans från Azure Portal och klicka på **lagrings mål** på den vänstra sid panelen. Sidan lagrings mål listar alla befintliga mål och innehåller en länk för att lägga till en ny.

![skärm bild av länken lagrings mål på sid panelen under rubriken konfigurera, som är mellan kategorins rubrik inställningar och övervakning](media/hpc-cache-storage-targets-sidebar.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Använd alternativet [AZ HPC-cache Storage-Target List](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) för att visa befintliga lagrings mål för en cache. Ange cache-namn och resurs grupp (om du inte har angett det globalt).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Använd [AZ HPC-cache Storage-mål show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) för att visa information om ett visst lagrings mål. (Ange lagrings målet efter namn.)

Exempel:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="add-a-new-azure-blob-storage-target"></a>Lägg till ett nytt Azure Blob Storage-mål

Ett nytt Blob Storage-mål måste ha en tom BLOB-behållare eller en behållare som är ifylld med data i Azure HPC-cachens moln fil system format. Läs mer om att för hands läsa in en BLOB-behållare i [Flytta data till Azure Blob Storage](hpc-cache-ingest.md).

Sidan Azure Portal **Lägg till lagrings mål** innehåller alternativet att skapa en ny BLOB-behållare precis innan du lägger till den.

### <a name="portal"></a>[Portal](#tab/azure-portal)

![skärm bild av sidan Lägg till lagrings mål, ifylld med information för ett nytt Azure Blob Storage-mål](media/hpc-cache-add-blob.png)

Ange den här informationen för att definiera en Azure Blob-behållare.

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.
* **Måltyp** – Välj **BLOB**.
* **Lagrings konto** – Välj det konto som du vill använda.

  Du måste auktorisera cache-instansen för att komma åt lagrings kontot enligt beskrivningen i [Lägg till åtkomst roller](#add-the-access-control-roles-to-your-account).

  Information om vilken typ av lagrings konto du kan använda finns i [krav för Blob Storage](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Lagrings behållare** – Välj BLOB-behållaren för målet eller klicka på **Skapa ny**.

  ![skärm bild av dialog rutan för att ange namn och åtkomst nivå (privat) för ny behållare](media/add-blob-new-container.png)

* **Sökväg till virtuellt namn område** – ange sökvägen till klientens fil för det här lagrings målet. Läs [Konfigurera sammanställd namnrymd](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

> [!NOTE]
> Om ditt lagrings kontos brand vägg är inställt på begränsa åtkomsten till endast "valda nätverk" använder du den tillfälliga lösningen som dokumenteras i [brand Väggs inställningarna för Blob Storage-kontot](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Lägg till roller för åtkomst kontroll i ditt konto

Azure HPC cache använder [rollbaserad åtkomst kontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) för att ge Cache-tjänsten åtkomst till ditt lagrings konto för Azure Blob Storage-mål.

Lagrings kontots ägare måste uttryckligen lägga till rollerna [lagrings konto deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) och [Storage BLOB data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) för användaren "HPC-Provider för HPC-cache".

Du kan göra detta i förväg eller genom att klicka på en länk på sidan där du lägger till ett Blob Storage-mål. Tänk på att det kan ta upp till fem minuter innan roll inställningarna sprids genom Azure-miljön, så du bör vänta några minuter efter att du har lagt till rollerna innan du skapar ett lagrings mål.

Steg för att lägga till Azure-roller:

1. Öppna sidan **åtkomst kontroll (IAM)** för lagrings kontot. (Länken på sidan **Lägg till lagrings mål** öppnar automatiskt den här sidan för det valda kontot.)

1. Klicka på **+** överst på sidan och välj **Lägg till en roll tilldelning**.

1. Välj rollen "lagrings konto deltagare" i listan.

1. I fältet **tilldela åtkomst till** lämnar du standardvärdet markerat ("Azure AD User, Group eller service huvud namn").  

1. I **Välj** -fältet söker du efter "HPC".  Den här strängen ska matcha ett tjänst huvud namn med namnet "HPC-Provider för HPC-cache". Klicka på det primära objektet för att välja det.

   > [!NOTE]
   > Om en sökning efter "HPC" inte fungerar kan du prova att använda strängen "storagecache" i stället. Användare som deltog i för hands versionerna (före GA) kan behöva använda det äldre namnet för tjänstens huvud namn.

1. Klicka på knappen **Spara** längst ned.

1. Upprepa den här processen för att tilldela rollen "Storage BLOB data Contributor".  

![skärm bild av användar gränssnittet för Lägg till roll tilldelning](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>Förutsättning: åtkomst till lagrings konto

Innan du lägger till ett Blob Storage-mål kontrollerar du att cachen har rätt roller för att komma åt lagrings kontot och att brand Väggs inställningarna tillåter skapande av lagrings mål.

Azure HPC cache använder [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/index.yml) för att ge Cache-tjänsten åtkomst till ditt lagrings konto för Azure Blob Storage-mål.

Lagrings kontots ägare måste uttryckligen lägga till rollerna [lagrings konto deltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor) och [Storage BLOB data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) för användaren "HPC-Provider för HPC-cache".

Det gick inte att skapa lagrings mål om cacheminnet inte har dessa roller.

Det kan ta upp till fem minuter innan roll inställningarna sprids genom Azure-miljön, så du bör vänta några minuter efter att du har lagt till rollerna innan du skapar ett lagrings mål.

Läs [Lägg till eller ta bort Azure Role-tilldelningar med hjälp av Azure CLI](../role-based-access-control/role-assignments-cli.md) för detaljerade instruktioner.

Kontrol lera också ditt lagrings kontos brand Väggs inställningar. Om brand väggen är inställd på att begränsa åtkomsten till endast "valda nätverk" kan det hända att det inte går att skapa lagrings målet. Använd den lösning som dokumenteras i [nätverks brand Väggs inställningarna för Blob Storage-kontot](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Lägga till ett Blob Storage-mål med Azure CLI

Använd [AZ HPC-cache Blob-Storage – Target Add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) Interface för att definiera ett Azure Blob Storage-mål.

Förutom standard resurs gruppen och cache namn parametrarna måste du ange följande alternativ för lagrings målet:

* ``--name``– Ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.

* ``--storage-account``– Konto identifieraren, i följande format:/Subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Information om vilken typ av lagrings konto du kan använda finns i [krav för Blob Storage](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name``– Ange namnet på den behållare som ska användas för det här lagrings målet.

* ``--virtual-namespace-path``-Ange sökvägen till klientens fil för det här lagrings målet. Omge sökvägar med citat tecken. Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

Exempel kommando:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Lägg till ett nytt NFS-lagrings mål

Ett NFS-lagrings mål har fler fält än Blob Storage-målet. Dessa fält anger hur lagrings exporten ska uppnås och hur data ska cachelagras effektivt. Dessutom kan du med ett NFS-lagrings mål skapa flera namn områdes Sök vägar om NFS-värden har fler än en export tillgänglig.

![Skärm bild av sidan Lägg till lagrings mål med NFS-mål definierat](media/add-nfs-target.png)

> [!NOTE]
> Innan du skapar ett NFS-lagrings mål bör du kontrol lera att lagrings systemet är tillgängligt från Azure HPC cache och uppfyller behörighets kraven. Det gick inte att skapa lagrings mål om cachen inte kan komma åt lagrings systemet. Läs om [lagrings krav för NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) och [FELSÖK problem med NAS-konfiguration och NFS-lagring](troubleshoot-nas.md) för mer information.

### <a name="choose-a-usage-model"></a>Välj en användnings modell
<!-- referenced from GUI - update aka.ms link if you change this heading -->

När du skapar ett lagrings mål som pekar på ett NFS-lagrings system måste du välja användnings modellen för det målet. Den här modellen avgör hur dina data cachelagras.

Det finns tre alternativ:

* **Läs tung, ovanliga skrivningar** – Använd det här alternativet om du vill påskynda Läs åtkomsten till filer som är statiska eller sällan ändrade.

  Med det här alternativet cachelagras filer som klienter läser, men skickar omedelbart skrivningar till backend-lagringen. Filer som lagras i cacheminnet jämförs aldrig med filerna på NFS-lagrings volymen.

  Använd inte det här alternativet om det finns en risk att en fil kan ändras direkt på lagrings systemet utan att först skriva den till cacheminnet. Om detta händer kommer den cachelagrade versionen av filen aldrig att uppdateras med ändringar från Server delen och data uppsättningen kan bli inkonsekvent.

* **Större än 15% skrivningar** – det här alternativet påskyndar både Läs-och skriv prestanda. När du använder det här alternativet måste alla klienter komma åt filer via Azure HPC-cachen i stället för att montera Server dels lagringen direkt. De cachelagrade filerna kommer att ha nya ändringar som inte lagras på Server delen.

  I den här användnings modellen kontrol leras inte filer i cacheminnet mot filerna på backend-lagringen. Den cachelagrade versionen av filen antas vara mer aktuell. En ändrad fil i cachen skrivs till Server dels lagrings systemet när den har varit i cachen under en timme utan ytterligare ändringar.

* **Klienter skriver till NFS-målet, vilket kringgår cachen** – Välj det här alternativet om några klienter i arbets flödet skriver data direkt till lagrings systemet utan att först skriva till cachen. Filer som klienten begär cachelagras, men eventuella ändringar av filerna från klienten skickas tillbaka till lagrings systemet på Server sidan omedelbart.

  Med den här användnings modellen kontrol leras ofta filerna i cacheminnet mot backend-versionerna för uppdateringar. Den här verifieringen tillåter att filer ändras utanför cachen och samtidigt bibehåller sig data konsekvens.

I den här tabellen sammanfattas skillnaderna mellan användnings modeller:

| Användnings modell                   | Cacheläge | Verifiering på Server Sidan | Maximal Skriv åtgärds fördröjning |
|-------------------------------|--------------|-----------------------|--------------------------|
| Läs tung, sällan skrivna skrivningar | Läsa         | Aldrig                 | Ingen                     |
| Större än 15% skrivningar       | Läsa/skriva   | Aldrig                 | 1 timme                   |
| Klienterna kringgår cachen      | Läsa         | 30 sekunder            | Ingen                     |

### <a name="create-an-nfs-storage-target"></a>Skapa ett NFS-lagrings mål

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Skärm bild av sidan Lägg till lagrings mål med NFS-mål definierat](media/add-nfs-target.png)

Ange den här informationen för ett NFS-baserat lagrings mål:

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.

* **Måltyp** – Välj **NFS**.

* **Hostname** – ange IP-adressen eller det fullständiga domän namnet för NFS-lagrings systemet. (Använd bara ett domän namn om din cache har åtkomst till en DNS-server som kan matcha namnet.)

* **Användnings modell** – Välj en av profilerna för Datacachen baserat på ditt arbets flöde, som beskrivs i [Välj en användnings modell](#choose-a-usage-model) ovan.

### <a name="nfs-namespace-paths"></a>Sökväg till NFS-namnrymd

Ett NFS-lagrings mål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export-eller under katalog på samma lagrings system.

Skapa alla sökvägar från ett lagrings mål.

Du kan när som helst [lägga till och redigera namn områdes Sök vägar](hpc-cache-edit-storage.md) på ett lagrings mål.

Fyll i följande värden för varje namn områdes Sök väg:

* **Sökväg till virtuellt namn område** – ange sökvägen till klientens fil för det här lagrings målet. Läs [Konfigurera sammanställd namnrymd](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

* **NFS-export Sök väg** – ange sökvägen till NFS-exporten.

* **Sökväg till under katalog** – om du vill montera en speciell under katalog för exporten anger du den här. Annars lämnar du fältet tomt.

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Använd Azure CLI-kommandot [AZ HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) för att skapa lagrings målet. Ange dessa värden utöver cache-namn och cache-resurs grupp:

* ``--name``– Ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.
* ``--nfs3-target``– IP-adressen för NFS-lagrings systemet. (Du kan använda ett fullständigt kvalificerat domän namn här om din cache har åtkomst till en DNS-server som kan matcha namnet.)
* ``--nfs3-usage-model``– En av profilerna för datacachelagring, som beskrivs i [Välj en användnings modell](#choose-a-usage-model)ovan.

  Kontrol lera namnen på användnings modellerna med kommandot [AZ HPC-cache Usage-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction``– Kopplings parametern länkar den klient-riktade virtuella fil Sök vägen med en export Sök väg på lagrings systemet.

  Ett NFS-lagrings mål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export-eller under katalog på samma lagrings system. Skapa alla sökvägar för ett lagrings system på ett lagrings mål.

  Du kan när som helst [lägga till och redigera namn områdes Sök vägar](hpc-cache-edit-storage.md) på ett lagrings mål.

  ``--junction``Parametern använder följande värden:

  * ``namespace-path``-Klientens virtuella fil Sök väg
  * ``nfs-export``– Lagrings system exporten som ska associeras med den klient-riktade sökvägen
  * ``target-path``(valfritt) – en under katalog till exporten, om det behövs

  Exempel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Läs [Konfigurera sammanställd namnrymd](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

Exempel kommando:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Resultat:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="next-steps"></a>Nästa steg

När du har skapat lagrings mål bör du överväga något av följande:

* [Montera Azure HPC Cache](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage](hpc-cache-ingest.md)

Om du behöver uppdatera inställningarna kan du [Redigera ett lagrings mål](hpc-cache-edit-storage.md).
