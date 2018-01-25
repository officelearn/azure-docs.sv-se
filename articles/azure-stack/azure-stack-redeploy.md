---
title: Distribuera Azure Stack | Microsoft Docs
description: Distribuera Azure-stacken.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Distribuera Azure Stack
Om du får ett fel vid distribution av Azure-stacken, du kan köra igen installationen med hjälp av följande PowerShell-kommando: `.\InstallAzureStackpoc.ps1 -rerun`. Detta kommando startar om Azure Stack-installationen på den plats som den tidigare misslyckats utan att behöva börja om från början. Om du får samma installationsfel, kan det vara nödvändigt att utföra en fullständig Omdistributionen till adress problemet. 

Om du vill distribuera om Azure-stacken börja du om från början som beskrivs nedan.

## <a name="steps-to-redeploy-azure-stack"></a>Steg för att distribuera Azure-stacken
1. Öppna en upphöjd PowerShell-konsol på development kit värden > Gå till skriptet asdk installer.ps1 > Kör den > klickar du på **omstart**.
2. Välj det grundläggande operativsystemet (inte **Azure Stack**) och klicka på **nästa**.
3. Ta bort filen CloudBuilder.vhdx som används som en del av den tidigare distribueringen när development kit värden startas om.
4. [Distribuera i development kit](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Nästa steg
[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)

