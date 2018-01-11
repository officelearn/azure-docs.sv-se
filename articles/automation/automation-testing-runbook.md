---
title: Testa en runbook i Azure Automation | Microsoft Docs
description: "Innan du publicerar en runbook i Azure Automation kan du testa den för att kontrollera som fungerar som förväntat.  Den här artikeln beskriver hur du testar en runbook och visa utdata."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 7f7db785-52c0-4613-aa12-b02fd32a5182
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
ms.openlocfilehash: 49e8dfa341940386f15932ec4346c8811effbf0b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="testing-a-runbook-in-azure-automation"></a>Testa en runbook i Azure Automation
När du testar en runbook i [utkast](automation-creating-importing-runbook.md#publishing-a-runbook) körs och eventuella åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men [utdata](automation-runbook-output-and-messages.md#output-stream) och [varnings- och](automation-runbook-output-and-messages.md#message-streams) dataströmmar visas i testet utdata fönstret. Meddelanden till den [utförliga strömmen](automation-runbook-output-and-messages.md#message-streams) visas i utdatafönstret bara om den [variabeln $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) är inställd på Fortsätt.

Även om utkastet körs den fortfarande körs arbetsflödet normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa runbooks på icke-produktionsresurser.

Proceduren för att testa varje [typ av runbook](automation-runbook-types.md) är samma, och det finns ingen skillnad i tester mellan textrepresentation editor och grafiska redigerare i Azure-portalen.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Du testar en runbook i Azure-portalen
Du kan arbeta med alla [runbooktyp](automation-runbook-types.md) i Azure-portalen.

1. Öppnar du Utkastversionen av runbooken i antingen den [textrepresentation editor](automation-edit-textual-runbook.md) eller [grafiska redigerare](automation-graphical-authoring-intro.md).
2. Klicka på den **Test** knappen för att öppna bladet Test.
3. Om runbooken har parametrar visas de i den vänstra rutan, där du kan ange värden som ska användas för testet.
4. Om du vill köra test en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), ändra **inställningar** till **Hybrid Worker** och välj namnet på målgruppen.  Annars behåller du standardvärdet **Azure** att köra testet i molnet.
5. Klicka på den **starta** för att starta testet.
6. Om runbook är [PowerShell-arbetsflöde](automation-runbook-types.md#powershell-workflow-runbooks) eller [grafisk](automation-runbook-types.md#graphical-runbooks), och du kan stoppa eller inaktivera den när den testas med hjälp av knapparna under Utdatarutan. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
7. Granska utdata från runbooken i utdatarutan.

## <a name="next-steps"></a>Nästa steg
* Information om hur du skapar eller importera en runbook finns [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om hur du konfigurerar runbooks för att returnera statusmeddelanden och fel, inklusive rekommenderade metoder, se [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)

