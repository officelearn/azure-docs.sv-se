---
title: Ansluta till och hantera Microsoft Azure Data Box Gateway-enheten via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och hantera sedan Data Box Gateway via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403501"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Hantera en Azure Data Box Gateway-enheten via Windows PowerShell

Azure Data Box Gateway-lösning kan du skicka data via nätverket till Azure. Den här artikeln beskriver några av konfiguration och hanteringsaktiviteter för din Data Box-Gateway-enhet. Du kan använda Azure-portalen, lokala webbgränssnittet eller Windows PowerShell-gränssnittet för att hantera din enhet.

Den här artikeln handlar om de uppgifter som du gör med hjälp av PowerShell-gränssnittet.

Den här artikeln innehåller följande procedurer:

- Ansluta till PowerShell-gränssnittet
- Starta en supportsession
- Skapa ett supportpaket
- Överför certifikat
- Starta i icke-DHCP-miljö
- Visa enhetsinformation

## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett supportpaket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Överför certifikat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Starta i icke-DHCP-miljö

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Visa enhetsinformation

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.
