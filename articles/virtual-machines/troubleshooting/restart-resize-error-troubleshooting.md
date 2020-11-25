---
title: Virtuella datorer startar om eller ändrar storlek på problem i Azure | Microsoft Docs
description: Felsök distributions problem i Resource Manager med att starta om eller ändra storlek på en befintlig virtuell dator i Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85acd8e26ca10730638332047a37d281358d205f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022895"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Felsöka distributionsproblem relaterade till omstart eller storleksändring av en befintlig virtuell Windows-dator i Azure
När du försöker starta en stoppad virtuell Azure-dator (VM) eller ändra storlek på en befintlig virtuell Azure-dator uppstår ett allokeringsfel. Det här felet uppstår när klustret eller regionen inte har tillgängliga resurser eller inte stöder den begärda virtuella dator storleken.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Samla in aktivitets loggar
För att starta fel sökningen samlar du in aktivitets loggarna för att identifiera det fel som är associerat med problemet. Följande länkar innehåller detaljerad information om processen:

[Visa distributionsåtgärder](../../azure-resource-manager/templates/deployment-history.md)

[Visa aktivitets loggar för att hantera Azure-resurser](../../azure-resource-manager/management/view-activity-logs.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fel när du startar en stoppad virtuell dator
Du försöker starta en virtuell dator som har stoppats men får ett allokeringsfel.

### <a name="cause"></a>Orsak
Begäran om att starta den stoppade virtuella datorn måste göras vid det ursprungliga klustret som är värd för moln tjänsten. Klustret har dock inte tillräckligt med ledigt utrymme för att uppfylla begäran.

### <a name="resolution"></a>Lösning
* Stoppa alla virtuella datorer i tillgänglighets uppsättningen och starta sedan om varje virtuell dator.
  
  1. Klicka på **resurs grupper**  >  *resurs gruppen*  >  **resurser**  >  *din tillgänglighets uppsättning*  >  **Virtual Machines**  >  *den virtuella datorn*  >  **stoppas**.
  2. När alla virtuella datorer har stoppats markerar du var och en av de stoppade virtuella datorerna och klickar på Starta.
* Försök att starta om begäran vid ett senare tillfälle.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Fel när du ändrar storlek på en befintlig virtuell dator
Du försöker ändra storlek på en befintlig virtuell dator men få ett allokeringsfel.

### <a name="cause"></a>Orsak
Begäran om att ändra storlek på den virtuella datorn måste göras vid det ursprungliga klustret som är värd för moln tjänsten. Klustret stöder dock inte den begärda virtuella dator storleken.

### <a name="resolution"></a>Lösning
* Gör om begäran med en mindre VM-storlek.
* Om det inte går att ändra storleken på den begärda virtuella datorn:
  
  1. Stoppa alla virtuella datorer i tillgänglighets uppsättningen.
     
     * Klicka på **resurs grupper**  >  *resurs gruppen*  >  **resurser**  >  *din tillgänglighets uppsättning*  >  **Virtual Machines**  >  *den virtuella datorn*  >  **stoppas**.
  2. När alla virtuella datorer har stoppats ändrar du storlek på önskad virtuell dator till en större storlek.
  3. Välj den ändrade virtuella datorn och klicka på **Starta** och starta sedan var och en av de stoppade virtuella datorerna.

## <a name="next-steps"></a>Nästa steg
Om du stöter på problem när du skapar en ny virtuell Windows-dator i Azure kan du läsa [Felsöka distributions problem med att skapa en ny virtuell Windows-dator i Azure](./troubleshoot-deployment-new-vm-windows.md).
