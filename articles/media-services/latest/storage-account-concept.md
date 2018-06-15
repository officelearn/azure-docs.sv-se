---
title: Storage-konton i Azure Media Services | Microsoft Docs
description: Den här artikeln beskrivs hur Media Services använder storage-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788069"
---
# <a name="storage-accounts"></a>Lagringskonton

När du skapar ett Media Services-konto kan behöva du ange namnet på en resurs för Azure Storage-konto. Det angivna lagringskontot är kopplad till ditt Media Services-konto. 

Du måste ha en **primära** storage-konto och du kan ha valfritt antal **sekundära** storage-konton som är associerade med Media Services-kontot. Media Services stöder **allmänna v2** (GPv2) eller **allmänna v1** konton (GPv1). 

>[!NOTE]
> Endast BLOB-konton tillåts inte som **primära**. 

Vi rekommenderar att du använder GPv2, så att du kan dra nytta av att välja mellan hot och kall lagringsnivåer. Mer information om lagringskonton finns [Azure Storage-konto](../../storage/common/storage-account-options.md). 

## <a name="assets-in-a-storage-account"></a>Tillgångar i ett lagringskonto

I Media Services v3 för Storage-API: er att överföra filer. Om du vill se hur du använder API: erna lagring med Media Services för att ladda upp dina indatafiler kolla [skapar ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md). 

> [!Note]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services SDK utan att använda Media Services-API: er.

## <a name="next-steps"></a>Nästa steg

Information om hur du kopplar ett lagringskonto till Media Services-kontot finns [skapa ett konto](create-account-cli-quickstart.md).
