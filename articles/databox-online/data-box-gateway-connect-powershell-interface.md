---
title: Använd Windows PowerShell för att ansluta till och hantera Azure Data Box Gateway-enhet
description: Beskriver hur du ansluter till och sedan hanterar Data Box Gateway via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384609"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Hantera en Azure Data Box Gateway-enhet via Windows PowerShell

Med Azure Data Box Gateway lösning kan du skicka data via nätverket till Azure. I den här artikeln beskrivs några konfigurations-och hanterings uppgifter för din Data Box Gateway-enhet. Du kan använda Azure Portal, det lokala webb gränssnittet eller Windows PowerShell-gränssnittet för att hantera enheten.

Den här artikeln fokuserar på de uppgifter du gör med PowerShell-gränssnittet.

Den här artikeln innehåller följande procedurer:

- Ansluta till PowerShell-gränssnittet
- Skapa ett support paket
- Överför certifikat
- Starta i icke-DHCP-miljö
- Visa enhets information

## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett support paket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Överför certifikat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Starta i icke-DHCP-miljö

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Visa enhets information

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.
