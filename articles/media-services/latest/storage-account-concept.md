---
title: Azure-lagringskonton
titleSuffix: Azure Media Services
description: Lär dig hur du skapar ett Azure-lagringskonto som ska användas med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499834"
---
# <a name="azure-storage-accounts"></a>Azure Storage-konton

Om du vill börja hantera, kryptera, koda, analysera och strömma medieinnehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto.

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika ytterligare kostnader för svarstid och datautgående.

Du måste ha ett **primärt** lagringskonto och du kan ha valfritt antal **sekundära lagringskonton** kopplade till ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast Blob-konton tillåts inte som **Primär**.

Vi rekommenderar att du använder GPv2, så att du kan dra nytta av de senaste funktionerna och prestanda. Mer information om lagringskonton finns i [Översikt över Azure Storage-konto](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Endast hot access-nivån stöds för användning med Azure Media Services, även om de andra åtkomstnivåerna kan användas för att minska lagringskostnaderna för innehåll som inte används aktivt.

Det finns olika SKU:er som du kan välja för ditt lagringskonto. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU `--sku Standard_RAGRS`för produktion bör du överväga , vilket ger geografisk replikering för affärskontinuitet.

## <a name="assets-in-a-storage-account"></a>Tillgångar i ett lagringskonto

I Media Services v3 används lagrings-API:erna för att överföra filer till tillgångar. Mer information finns [i Tillgångar i Azure Media Services v3](assets-concept.md).

> [!Note]
> Försök inte ändra innehållet i blob-behållare som genererades av Media Services SDK utan att använda Api:er för Medietjänster.

## <a name="storage-side-encryption"></a>Kryptering på lagringssidan

För att skydda dina tillgångar i vila bör tillgångarna krypteras av kryptering på lagringssidan. Följande tabell visar hur kryptering på lagringssidan fungerar i Media Services v3:

|Krypteringsalternativ|Beskrivning|Media Services v3|
|---|---|---|
|Kryptering av lagring av Media Services| AES-256 kryptering, nyckel hanteras av Media Services. |Stöds inte. <sup>1.</sup>|
|[Kryptering av lagringstjänster för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden.|Stöds.|
|[Kryptering på klientsidan för lagring](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Kryptering på klientsidan som erbjuds av Azure-lagring, nyckel som hanteras av kunden i Key Vault.|Stöds inte.|

<sup>1</sup> I Media Services v3 stöds lagringskryptering (AES-256-kryptering) endast för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2, vilket innebär att v3 fungerar med befintliga lagringskrypterade tillgångar men inte tillåter skapande av nya.

## <a name="storage-account-errors"></a>Fel i lagringskontot

Tillståndet ”Frånkopplad” för ett Media Services-konto anger att kontot inte längre har åtkomst till en eller flera av de anslutna lagringskontona på grund av en ändring av åtkomstnycklarna för lagring. Uppdaterade lagringsåtkomstnycklar krävs av Media Services för att utföra många av aktiviteterna på kontot.

Följande är de primära scenarier som kan leda till ett Media Services-konto som inte har åtkomst till anslutna lagringskonton.

|Problem|Lösning|
|---|---|
|Media Services-kontot eller direktanslutna lagringskonton har migrerats till separata prenumerationer. |Migrera lagringskontot eller Media Services-kontot så att de alla har samma prenumeration. |
|Media Services-kontot använder ett konto för direktansluten lagring i en annan prenumeration eftersom detta var ett tidigare Media Services-konto där detta stöddes. Alla tidiga Media Services-konton har konverterats till moderna Azure Resources Manager-baserade konton och har ett frånkopplat tillstånd. |Migrera lagringskontot eller Media Services-kontot så att de alla har samma prenumeration.|

## <a name="azure-storage-firewall"></a>Brandvägg för Azure Storage

Azure Media Services stöder inte lagringskonton med Azure Storage-brandväggen eller [Privata slutpunkter](https://docs.microsoft.com/azure/storage/common/storage-network-security) aktiverade.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du bifogar ett lagringskonto till ditt Media Services-konto finns i [Skapa ett konto](create-account-cli-quickstart.md).
