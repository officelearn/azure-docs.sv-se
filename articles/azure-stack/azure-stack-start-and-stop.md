---
title: Starta och stoppa Azure Stack | Microsoft Docs
description: Lär dig mer om att starta och stänga av Azure-stacken.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 53015ba5c282bbe9c7b8185b080ffb6d834b6c75
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-azure-stack"></a>Starta och stoppa Azure Stack
Du bör följa procedurerna i den här artikeln Stäng och starta om tjänsterna Azure Stack korrekt. 

## <a name="stop-azure-stack"></a>Stoppa Azure Stack 

Stänga av Azure-stacken med följande steg:

1. Öppna en privilegierad Endpoint Session (program) från en dator med nätverksåtkomst till Azure-stacken ERCS virtuella datorer. Instruktioner finns i [med Privilegierade slutpunkten i Azure-stacken](azure-stack-privileged-endpoint.md).

2. Kör från detta program:

    ```powershell
      Stop-AzureStack
    ```

3. Vänta tills alla fysiska noder som Azure Stack att stänga av.

> [!Note]  
> Du kan kontrollera status för en fysisk nod power genom att följa anvisningarna från OEM-tillverkaren (OEM) som tillhandahåller Azure Stack-maskinvara. 

## <a name="start-azure-stack"></a>Starta Azure-stacken 

Starta Azure-stacken med följande steg. Följ dessa steg oavsett hur Azure-stacken stoppades.

1. Slå på varje fysisk nod i Azure Stack-miljö. Kontrollera slå på instruktioner för fysiska noder genom att följa anvisningarna från OEM-tillverkaren (OEM) som angetts av maskinvaran för Azure-stacken.

2. Vänta tills Stack Azure infrastrukturtjänster startar. Azure infrastrukturtjänster för stacken kan kräva två timmar att slutföra startprocessen. Du kan kontrollera status för start av Azure-stacken med den [ **Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack).


## <a name="get-the-startup-status-for-azure-stack"></a>Hämta Start-status för Azure-stacken

Hämta starten för rutinen Azure Stack startades med följande steg:

1. Öppna en privilegierad Endpoint-Session från en dator med nätverksåtkomst till Azure-stacken ERCS virtuella datorer.

2. Kör från detta program:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Felsöka start och stopp av Azure-stacken

Utför följande steg om infrastruktur- och -tjänster inte att starta 2 timmar efter du power på Azure-stacken miljön. 

1. Öppna en privilegierad Endpoint-Session från en dator med nätverksåtkomst till Azure-stacken ERCS virtuella datorer.

2. Kör: 

    ```powershell
      Test-AzureStack
      ```

3. Granska utdata och åtgärda eventuella fel i hälsa. Mer information finns i [kör ett verifieringstest i Azure-stacken](azure-stack-diagnostic-test.md).

4. Kör:

    ```powershell
      Start-AzureStack
    ```

5. Om du kör **Start AzureStack** resulterar i ett fel kontaktar Microsoft Support Services. 

## <a name="next-steps"></a>Nästa steg 

Lär dig mer om Azure-stacken diagnosverktyg och utfärda loggning, se [Azure Stack diagnosverktyg](azure-stack-diagnostics.md).
