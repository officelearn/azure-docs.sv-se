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
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: 471bc34272b8e141c8640bd218bdafd840850d24
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672278"
---
# <a name="cloud-upload-and-storage"></a>Uppladdning till och lagring i molnet

Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att använda storage-konton på samma plats som Media Services-konto för att undvika extra kostnader för svarstid och utgående

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). 

>[!NOTE]
> Endast blob-konton tillåts inte som **primära**. 

Vi rekommenderar att du använder GPv2, så att du kan dra nytta av att välja mellan frekvent och lågfrekvent lagringsnivå. Mer information om lagringskonton finns [översikt över Azure Storage-konto](../../storage/common/storage-account-overview.md). 

Det finns olika SKU: er som du kan välja för ditt lagringskonto. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Om du vill experimentera med storage-konton kan använda `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du, `--sku Standard_RAGRS`, vilket ger geografiska replikering för affärskontinuitet. 

## <a name="assets-in-a-storage-account"></a>Tillgångar i ett lagringskonto

I Media Services v3 för Storage-API: er att ladda upp filer.

> [!Note]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services SDK utan att använda API: er för Media Services.

Om du vill se hur du använder Storage-API: er med Media Services för att ladda upp dina indatafiler, Kolla in [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md). 
 
## <a name="next-steps"></a>Nästa steg

Läs hur du kopplar ett storage-konto till ditt Media Services-konto i [skapa ett konto](create-account-cli-quickstart.md).
