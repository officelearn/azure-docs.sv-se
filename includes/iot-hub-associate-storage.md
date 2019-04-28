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
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399172"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associera ett Azure Storage-konto till IoT Hub

Eftersom den simulerade enhetsappen överför en fil till en blob, måste du ha en [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) kontot som är associerat med din IoT-hubb. När du kopplar ett Azure Storage-konto med en IoT-hubb, genererar IoT-hubben en SAS-URI. En enhet kan använda den här SAS-URI för att på ett säkert sätt överföra en fil till en blob-behållare. Tjänsten IoT Hub och SDK: er för enhetens samordna processen som genererar SAS-URI och gör dem tillgängliga för en enhet som du använder för att ladda upp en fil.

Följ instruktionerna i [konfigurera filöverföringar med Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Se till att en blob-behållare är associerad med din IoT-hubb och att filen meddelanden har aktiverats.

![Aktivera meddelanden för filen i portalen](./media/iot-hub-associate-storage/enable-file-notifications.png)