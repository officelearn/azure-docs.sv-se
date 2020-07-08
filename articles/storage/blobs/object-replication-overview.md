---
title: Översikt över objekt replikering (för hands version)
titleSuffix: Azure Storage
description: Objekt replikering (för hands version) kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Använd objekt replikering för att minimera svars tiden för Läs begär Anden, för att öka effektiviteten för beräknings arbets belastningar, optimera data distribution och minimera kostnaderna.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2d8d4c369cef8bf996628e8c89a424f04dcdbe71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888073"
---
# <a name="object-replication-for-block-blobs-preview"></a>Objekt replikering för block-blobar (för hands version)

Objekt replikering (för hands version) kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Vissa scenarier som stöds av objekt replikering är:

- **Minimerar svars tid.** Objekt replikering kan minska svars tiden för Läs begär Anden genom att göra det möjligt för klienter att använda data från en region som ligger närmare fysisk närhet.
- **Öka effektiviteten för beräknings arbets belastningar.** Med objekt replikering kan beräknings arbets belastningar bearbeta samma uppsättningar block blobbar i olika regioner.
- **Optimering av data distribution.** Du kan bearbeta eller analysera data på en och samma plats och sedan replikera bara resultaten till ytterligare regioner.
- **Optimera kostnader.** När dina data har repliker ATS kan du minska kostnaderna genom att flytta den till Arkiv nivån med hanterings principer för livs cykel.

Följande diagram visar hur objekt replikeringen replikerar block blobbar från ett käll lagrings konto i en region till mål konton i två olika regioner.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram som visar hur objekt replikering fungerar":::

Information om hur du konfigurerar objekt replikering finns i [Konfigurera objekt replikering (för hands version)](object-replication-configure.md).

## <a name="object-replication-policies-and-rules"></a>Principer och regler för objekt replikering

När du konfigurerar objekt replikering skapar du en replikeringsprincip som anger käll lagrings kontot och mål kontot. En replikeringsprincip innehåller en eller flera regler som anger en käll behållare och en mål behållare och anger vilka block-blobar som ska replikeras i käll behållaren.

När du har konfigurerat objekt replikering kontrollerar Azure Storage ändrings flödet för käll kontot med jämna mellanrum och replikerar eventuellt Skriv-eller borttagnings åtgärder till mål kontot. Replikeringsfördröjning beror på storleken på den Block-Blob som replikeras.

> [!IMPORTANT]
> Eftersom block BLOB-data replikeras asynkront synkroniseras inte käll kontot och mål kontot omedelbart. Det finns för närvarande inget service avtal för hur lång tid det tar att replikera data till mål kontot.

### <a name="replications-policies"></a>Principer för replikering

När du konfigurerar objekt replikering skapas en replikeringsprincip på både käll kontot och mål kontot via Azure Storage resurs leverantören. Replikeringsprincipen identifieras av ett princip-ID. Principen på käll-och mål kontona måste ha samma princip-ID för att replikeringen ska kunna ske.

Ett lagrings konto kan fungera som käll konto för upp till två mål konton. Och ett mål konto får inte ha fler än två käll konton. Käll- och målkontona kan finnas i olika regioner. Du kan konfigurera separata replikeringsprinciper för att replikera data till varje mål konto.

### <a name="replication-rules"></a>Regler för replikering

Reglerna för replikering anger hur Azure Storage kommer att replikera blobbar från en käll behållare till en mål behållare. Du kan ange upp till 10 regler för replikering för varje replikeringsprincip. Varje regel definierar en enda käll-och mål behållare, och varje käll-och mål behållare kan bara användas i en regel.

När du skapar en replikeringsprincip, kopieras som standard bara nya block blobbar som senare läggs till i käll behållaren. Du kan också ange att både nya och befintliga block-blobar ska kopieras, eller så kan du definiera en anpassad kopierings omfattning som kopierar block-blobbar som skapats från en angiven tids period.

Du kan också ange ett eller flera filter som en del av en replikeringsprincip för att filtrera block blobbar efter prefix. När du anger ett prefix kopieras bara blobbar som matchar det prefixet i käll behållaren till mål behållaren.

Käll-och mål behållarna måste finnas innan du kan ange dem i en regel. När du har skapat replikeringsprincipen blir målcontainern skrivskyddad. Försök att skriva till målcontainern misslyckas med felkoden 409 (konflikt). Du kan dock anropa åtgärden [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) på en BLOB i mål behållaren för att flytta den till Arkiv nivån. Mer information om Arkiv nivån finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md#archive-access-tier)lag rings nivåer.

## <a name="about-the-preview"></a>Om för hands versionen

Objekt replikering stöds endast för allmänna-syfte v2-lagrings konton. Objekt replikering är tillgängligt i följande regioner i för hands versionen:

- Frankrike, centrala
- Kanada, östra
- Kanada, centrala

Både käll-och mål kontona måste finnas i något av dessa regioner för att det ska gå att använda objekt replikering. Kontona kan finnas i två olika regioner.

Under för hands versionen finns det inga ytterligare kostnader för replikering av data mellan lagrings konton.

> [!IMPORTANT]
> För hands versionen av objekt replikering är endast avsedd för användning utan produktion. Service nivå avtal (service avtal) för produktions tjänster är inte tillgängliga för närvarande.

### <a name="prerequisites-for-object-replication"></a>Förhandskrav för objektreplikering

Objekt replikering kräver att följande Azure Storage funktioner är aktiverade: 
- [Ändra feed](storage-blob-change-feed.md)
- [Versionshantering](versioning-overview.md)

Innan du konfigurerar objekt replikering måste du aktivera dess krav. Ändra feed måste vara aktiverat på käll kontot och blob-versioner måste vara aktiverade på både käll-och mål kontot. Mer information om hur du aktiverar de här funktionerna finns i följande artiklar:

- [Aktivera och inaktivera ändrings flödet](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)

Se till att du registrerar dig för för hands versionerna av ändra feed och blob-versioner innan du aktiverar dem.

Att aktivera ändrings flöde och blob-versioner kan medföra ytterligare kostnader. Mer information finns på [sidan med Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Registrera dig för för hands versionen

Du kan registrera dig för för hands versionen av objekt replikering med PowerShell eller Azure CLI. Se till att du även registrerar dig för för hands versionerna av ändra feed-och blob-versioner om du inte redan gjort det.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill registrera dig för för hands versionen med PowerShell kör du följande kommandon:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig för för hands versionen med Azure CLI kör du följande kommandon:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Kontrol lera registrerings status

Du kan kontrol lera status för dina registrerings begär Anden med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kör följande kommandon för att kontrol lera statusen för dina registrerings begär Anden med PowerShell:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera statusen för dina registrerings begär Anden med Azure CLI kör du följande kommandon:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Ställ frågor eller ge feedback

Om du vill ställa frågor om för hands versionen av objekt replikering eller lämna feedback kontaktar du Microsoft på AzureStorageFeedback@microsoft.com . Idéer och förslag om Azure Storage är alltid välkomna i [Azure Storage feedback-forumet](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera objekt replikering (för hands version)](object-replication-configure.md)
- [Ändra stöd för feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
