---
title: Molnbaserad överföring och lagring med Azure Media Services | Microsoft Docs
description: Den här artikeln molnöverföring och koncept.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/28/2019
ms.author: juliako
ms.openlocfilehash: 96c3a3eb5e4c07ad9cad8ea5060a27c0c33eec5f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621306"
---
# <a name="cloud-upload-and-storage"></a>Uppladdning till och lagring i molnet

Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att använda storage-konton på samma plats som Media Services-konto för att undvika extra kostnader för svarstid och utgående

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). 

>[!NOTE]
> Endast blob-konton tillåts inte som **primära**. 

Vi rekommenderar att du använder GPv2, så att du kan dra nytta av att välja mellan frekvent och lågfrekvent lagringsnivå. Mer information om lagringskonton finns [översikt över Azure Storage-konto](../../storage/common/storage-account-overview.md). 

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

## <a name="next-steps"></a>Nästa steg

Läs hur du kopplar ett storage-konto till ditt Media Services-konto i [skapa ett konto](create-account-cli-quickstart.md).
