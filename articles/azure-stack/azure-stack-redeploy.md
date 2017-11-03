---
title: Distribuera Azure Stack | Microsoft Docs
description: Distribuera Azure-stacken.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Distribuera Azure Stack
Om du vill distribuera om Azure-stacken börja du om från början som beskrivs nedan.

## <a name="steps-to-redeploy-azure-stack"></a>Steg för att distribuera Azure-stacken
1. Öppna en upphöjd PowerShell-konsol på development kit värden > Gå till skriptet asdk installer.ps1 > Kör den > klickar du på **omstart**.
2. Välj det grundläggande operativsystemet (inte **Azure Stack**) och klicka på **nästa**.
3. Ta bort filen CloudBuilder.vhdx som används som en del av den tidigare distribueringen när development kit värden startas om.
4. [Distribuera i development kit](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Nästa steg
[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)

