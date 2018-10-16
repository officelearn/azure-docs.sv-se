---
title: Starta och stoppa Azure Stack | Microsoft Docs
description: Lär dig mer om att starta och stänga av Azure Stack.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6da81ab90a87285f9e1874e3f10eff4570124192
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344021"
---
# <a name="start-and-stop-azure-stack"></a>Starta och stoppa Azure Stack
Du bör följa procedurerna i den här artikeln för att korrekt stänger och startar om Azure Stack-tjänster. Avstängning avstängning fysiskt hela Azure Stack-miljön. Start aktiveras alla infrastrukturroller för och returnerar klientens resurser till energinivån de befann sig i före avstängning.

## <a name="stop-azure-stack"></a>Stoppa Azure Stack 

Stäng av Azure Stack med följande steg:

1. Förbered alla arbetsbelastningar som körs på Azure Stack-miljön klientresurser för kommande avstängningen. 

2. Öppna en privilegierad slutpunkt Session (program) från en dator med nätverksåtkomst till Azure Stack ERCS virtuella datorer. Anvisningar finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md).

3. Kör från detta program:

    ```powershell
      Stop-AzureStack
    ```

4. Vänta tills alla fysiska Azure Stack-noder till en strömkälla av.

> [!Note]  
> Du kan kontrollera energistatusen på en fysisk nod genom att följa instruktionerna från Original Equipment Manufacturer (OEM) som tillhandahåller Azure Stack-maskinvara. 

## <a name="start-azure-stack"></a>Starta Azure Stack 

Starta Azure Stack med följande steg. Följ dessa steg oavsett hur Azure Stack stoppats.

1. Slå på varje fysisk nod i Azure Stack-miljön. Kontrollera ström på instruktioner för fysisk nod genom att följa instruktionerna från Original Equipment Manufacturer (OEM) som tillhandahåller maskinvaran för Azure Stack.

2. Vänta tills Azure Stack-infrastruktur-tjänster startar. Azure Stack-infrastrukturtjänster kan kräva två timmar att slutföra startprocessen. Du kan kontrollera status för Azure Stack med start den [ **Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack).

3. Se till att alla dina klientresurser har kommit tillbaka till det tillstånd som de befann sig i före avstängning. Arbetsbelastningar som körs på klientens resurser kan behöva konfigureras efter starten av hanteraren för arbetsbelastning.

## <a name="get-the-startup-status-for-azure-stack"></a>Hämta status för start för Azure Stack

Hämta Start för Azure Stack-rutin för start med följande steg:

1. Öppna en privilegierad slutpunkt-Session från en dator med nätverksåtkomst till Azure Stack ERCS virtuella datorer.

2. Kör från detta program:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Felsöka start och stopp av Azure Stack

Utför följande steg om infrastruktur- och klienttrafik tjänsterna inte att starta 2 timmar efter att du power på Azure Stack-miljön. 

1. Öppna en privilegierad slutpunkt-Session från en dator med nätverksåtkomst till Azure Stack ERCS virtuella datorer.

2. Kör: 

    ```powershell
      Test-AzureStack
      ```

3. Granska utdata och lös eventuella problem med hälsotillståndet. Mer information finns i [kör ett verifieringstest av Azure Stack](azure-stack-diagnostic-test.md).

4. Kör:

    ```powershell
      Start-AzureStack
    ```

5. Om kör **Start AzureStack** resulterar i ett fel, kontakta Microsofts kundsupport för tjänster. 

## <a name="next-steps"></a>Nästa steg 

Läs mer om [diagnostiska verktyg för Azure Stack](azure-stack-diagnostics.md)
