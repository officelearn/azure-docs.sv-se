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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: dbd8dea32bbd4cacefd64e91541ab20b1056731e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379049"
---
# <a name="storage-accounts"></a>Lagringskonton

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. 

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
