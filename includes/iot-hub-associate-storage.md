---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76020928"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associera ett Azure Storage-konto till IoT Hub

Eftersom den simulerade enhetsappen laddar upp en fil till en blob måste du ha ett [Azure Storage-konto](../articles/storage/common/storage-account-create.md) kopplat till din IoT-hubb. När du associerar ett Azure Storage-konto med en IoT-hubb genererar IoT-hubben en SAS URI. En enhet kan använda den här SAS URI-filen för att på ett säkert sätt överföra en fil till en blob-behållare. IoT Hub-tjänsten och enhetenSDK:er samordnar processen som genererar SAS URI och gör den tillgänglig för en enhet som ska användas för att överföra en fil.

Följ instruktionerna i [Konfigurera filöverföringar med Azure-portalen](../articles/iot-hub/iot-hub-configure-file-upload.md). Kontrollera att en blob-behållare är associerad med din IoT-hubb och att filmeddelanden är aktiverade.

![Aktivera filmeddelanden i portalen](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
