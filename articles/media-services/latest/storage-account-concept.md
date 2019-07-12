---
title: Azure Storage-konton med Azure Media Services-konton | Microsoft Docs
description: När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto.
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
ms.openlocfilehash: 5c5bfa224b87040f5142663e6adab01072c6e6ba
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619303"
---
# <a name="azure-storage-accounts"></a>Azure Storage-konton

Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att använda storage-konton på samma plats som Media Services-konto för att undvika extra kostnader för svarstid och utgående

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). <br/>Endast blob-konton tillåts inte som **primära**. 

Vi rekommenderar att du använder GPv2, så du kan dra nytta av de senaste funktionerna och prestanda. Mer information om lagringskonton finns [översikt över Azure Storage-konto](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Endast frekvent åtkomstnivå har stöd för användning med Azure Media Services, även om andra åtkomstnivåer som kan användas för att minska lagringskostnaderna för innehåll som aktivt inte används.

Det finns olika SKU: er som du kan välja för ditt lagringskonto. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du överväga `--sku Standard_RAGRS`, som ger geografisk replikering för affärskontinuitet. 

## <a name="assets-in-a-storage-account"></a>Tillgångar i ett lagringskonto

I Media Services v3 för Storage-API: er att överföra filer till tillgångar. Mer information finns i [tillgångar konceptet](assets-concept.md).

> [!Note]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services SDK utan att använda API: er för Media Services.
 
## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services v3:

|Krypteringsalternativet|Beskrivning|Media Services v3|
|---|---|---|
|Media Services-Lagringskryptering| AES-256-kryptering, viktiga hanteras av Media Services|Stöds inte<sup>(1)</sup>|
|[Kryptering av lagringstjänst för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|
|[Storage Client Side Encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|

<sup>1</sup> i Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="storage-account-errors"></a>Lagringskonton

Tillståndet ”Frånkopplad” för ett Media Services-konto anger att kontot inte längre har åtkomst till en eller flera av de anslutna lagringskontona på grund av en ändring av åtkomstnycklarna för lagring. Uppdaterade lagringsåtkomstnycklar krävs av Media Services för att utföra många av aktiviteterna på kontot.

Följande är de primära scenarier som kan leda till ett Media Services-konto som inte har åtkomst till anslutna lagringskonton. 

|Problem|Lösning|
|---|---|
|Media Services-kontot eller direktanslutna lagringskonton har migrerats till separata prenumerationer. |Du kan migrera lagringskonton eller Media Services-konton så att de finns på samma prenumeration. |
|Media Services-kontot använder ett konto för direktansluten lagring i en annan prenumeration eftersom detta var ett tidigare Media Services-konto där detta stöddes. Alla tidiga Media Services-konton har omvandlats till moderna konton med Azure Resource Manager och har frånkopplat läge. |Du kan migrera lagringskonto neller Media Services-konton så att de finns på samma prenumeration.|

## <a name="next-steps"></a>Nästa steg

Läs hur du kopplar ett storage-konto till ditt Media Services-konto i [skapa ett konto](create-account-cli-quickstart.md).
