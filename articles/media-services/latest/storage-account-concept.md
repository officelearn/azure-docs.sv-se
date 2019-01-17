---
title: Storage-konton i Azure Media Services | Microsoft Docs
description: Den här artikeln beskrivs hur Media Services använder storage-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: f5fc1385bff92851db81cd4ed397d66cb8a832f2
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54350939"
---
# <a name="storage-accounts"></a>Lagringskonton

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och lagringskontot som associerats med den måste vara en del av samma datacenter och samma resursgrupp.

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). 

>[!NOTE]
> Endast blob-konton tillåts inte som **primära**. 

Vi rekommenderar att du använder GPv2, så att du kan dra nytta av att välja mellan frekvent och lågfrekvent lagringsnivå. Mer information om lagringskonton finns [översikt över Azure Storage-konto](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Tillgångar i ett lagringskonto

I Media Services v3 för Storage-API: er att ladda upp filer. Om du vill se hur du använder Storage-API: er med Media Services för att ladda upp dina indatafiler, Kolla in [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md). 

> [!Note]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services SDK utan att använda API: er för Media Services.

## <a name="next-steps"></a>Nästa steg

Läs hur du kopplar ett storage-konto till ditt Media Services-konto i [skapa ett konto](create-account-cli-quickstart.md).
