---
title: Ansluta till och hantera Microsoft Azure Data Box Gateway-enheten via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och hantera sedan Data Box Gateway via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555222"
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

> [!IMPORTANT]
> Azure Data Box-Gateway är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Starta en supportsession

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

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
