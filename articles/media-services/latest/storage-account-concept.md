---
title: Azure Storage konton med Azure Media Services-konton | Microsoft Docs
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
ms.openlocfilehash: 4bbadd7e10f0fd6896932dd79a5ca42d9906d2a2
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602131"
---
# <a name="azure-storage-accounts"></a>Azure Storage konton

För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagrings konton på samma plats som Media Services kontot för att undvika ytterligare svars tid och data utgående kostnader

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). <br/>Endast blob-konton tillåts inte som **primära**. 

Vi rekommenderar att du använder GPv2 så att du kan dra nytta av de senaste funktionerna och prestandan. Mer information om lagrings konton finns i [Översikt över Azure Storage konto](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Endast frekvent åtkomst nivå stöds för användning med Azure Media Services, även om de andra åtkomst nivåerna kan användas för att minska lagrings kostnaderna för innehåll som inte används aktivt.

Det finns olika SKU: er som du kan välja för ditt lagrings konto. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du överväga `--sku Standard_RAGRS`, som ger geografisk replikering för affärskontinuitet. 

## <a name="assets-in-a-storage-account"></a>Till gångar i ett lagrings konto

I Media Services v3 används lagrings-API: er för att ladda upp filer till till gångar. Mer information finns i [till gångs koncept](assets-concept.md).

> [!Note]
> Du bör inte försöka ändra innehållet i BLOB-behållare som genererades av Media Services SDK utan att använda Media Services-API: er.
 
## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services v3:

|Krypteringsalternativet|Beskrivning|Media Services v3|
|---|---|---|
|Media Services-Lagringskryptering| AES-256-kryptering, viktiga hanteras av Media Services|Stöds inte<sup>(1)</sup>|
|[Kryptering för lagringstjänst för vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|
|[Kryptering av lagring på klient Sidan](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|

<sup>1</sup> i Media Services v3 stöds inte lagrings kryptering (AES-256-kryptering) för bakåtkompatibilitet när dina till gångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="storage-account-errors"></a>Fel vid lagrings konto

Tillståndet ”Frånkopplad” för ett Media Services-konto anger att kontot inte längre har åtkomst till en eller flera av de anslutna lagringskontona på grund av en ändring av åtkomstnycklarna för lagring. Uppdaterade lagringsåtkomstnycklar krävs av Media Services för att utföra många av aktiviteterna på kontot.

Följande är de primära scenarier som kan leda till ett Media Services-konto som inte har åtkomst till anslutna lagringskonton. 

|Problem|Lösning|
|---|---|
|Media Services-kontot eller direktanslutna lagringskonton har migrerats till separata prenumerationer. |Du kan migrera lagringskonton eller Media Services-konton så att de finns på samma prenumeration. |
|Media Services-kontot använder ett konto för direktansluten lagring i en annan prenumeration eftersom detta var ett tidigare Media Services-konto där detta stöddes. Alla tidiga Media Services-konton har omvandlats till moderna konton med Azure Resource Manager och har frånkopplat läge. |Du kan migrera lagringskonto neller Media Services-konton så att de finns på samma prenumeration.|

## <a name="azure-storage-firewall"></a>Azure Storage brand vägg

Azure Media Services stöder inte lagrings konton med Azure Storage brand väggen eller [privata slut punkter](https://docs.microsoft.com/azure/storage/common/storage-network-security) aktiverade.

## <a name="next-steps"></a>Nästa steg

Information om hur du ansluter ett lagrings konto till ditt Media Services-konto finns i [skapa ett konto](create-account-cli-quickstart.md).
