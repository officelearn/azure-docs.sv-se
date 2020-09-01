---
title: Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge GPU-enhet
description: Beskriver hur du konfigurerar TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: fdd88a902e812c89270a175aef4bf0de197c6e9b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146138"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge-enhet

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Om du använder en Windows-klient för att få åtkomst till din Azure Stack Edge-enhet, måste du konfigurera TLS 1,2 på klienten. Den här artikeln innehåller resurser och rikt linjer för att konfigurera TLS 1,2 på din Windows-klient. 

De rikt linjer som anges här baseras på test som utförs på en klient som kör Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Konfigurera TLS 1,2 för den aktuella PowerShell-sessionen

Utför följande steg för att konfigurera TLS 1,2 på klienten.

1. Kör PowerShell som administratör.
2. Om du vill ställa in TLS 1,2 för den aktuella PowerShell-sessionen skriver du:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Konfigurera TLS 1,2 på klienten

Om du vill ställa in systemomfattande TLS 1,2 för din miljö följer du rikt linjerna i dessa dokument:

- [Allmänt – så här aktiverar du TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [Så här aktiverar du TLS 1,2 på klienter](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Så här aktiverar du TLS 1,2 på plats servrar och fjärranslutna plats system](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protokoll i TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Chiffersviter](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12): Konfigurera bara [TLS cipher Suite-ordningen](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) se till att du visar dina aktuella chiffersviter och lägga som saknas i följande lista:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Du kan också lägga till dessa chiffersviter genom att redigera register inställningarna direkt.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Så här ställer du in elliptiska kurvor

    Se till att du visar dina aktuella elliptiska kurvor och lägga som saknas i följande lista:

    - P-256 
    - P-384

    Du kan också lägga till dessa elliptiska kurvor genom att redigera register inställningarna direkt.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Ange minsta RSA Key Exchange-storlek till 2048](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Nästa steg

[Anslut till Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)