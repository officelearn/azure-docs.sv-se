---
title: Testa en runbook i Azure Automation
description: Innan du publicerar en runbook i Azure Automation kan du testa den för att säkerställa att det fungerar som förväntat.  Den här artikeln beskriver hur du testar en runbook och visar dess utdata.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 95e3f4426fab8ed3ff28877607dee8694962e79f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422481"
---
# <a name="testing-a-runbook-in-azure-automation"></a>Testa en runbook i Azure Automation
När du testar en runbook i [utkastet](automation-creating-importing-runbook.md#publishing-a-runbook) körs och alla åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men [utdata](automation-runbook-output-and-messages.md#output-stream) och [varnings- och](automation-runbook-output-and-messages.md#message-streams) strömmar visas i testet utdata fönstret. Meddelanden till den [utförlig Stream](automation-runbook-output-and-messages.md#message-streams) visas i utdatafönstret endast om den [variabeln $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) är inställd på Fortsätt.

Även om utkastet körs runbook fortfarande att arbetsflödet körs normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa runbooks på icke-produktionsresurser.

Proceduren för att testa varje [typ av runbook](automation-runbook-types.md) är samma, och det finns ingen skillnad i testning mellan textredigeraren och den grafiska redigeraren i Azure-portalen.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Testa en runbook i Azure portal
Du kan använda någon [runbooktyp](automation-runbook-types.md) i Azure-portalen.

1. Öppnar du Utkastversionen av runbooken i antingen den [textredigeraren](automation-edit-textual-runbook.md) eller [grafiska redigeraren](automation-graphical-authoring-intro.md).
2. Klicka på den **Test** knappen för att öppna bladet Test.
3. Om runbooken har parametrar visas de i det vänstra fönstret där du kan ange värden som ska användas för testet.
4. Om du vill köra test en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), ändra **inställningar** till **Hybrid Worker** och välj namnet på målgruppen.  Annars behåller du standardvärdet **Azure** att köra testet i molnet.
5. Klicka på den **starta** för att starta testet.
6. Om runbook är [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) eller [grafisk](automation-runbook-types.md#graphical-runbooks), du kan stoppa eller inaktivera den när den testas med hjälp av knapparna under Utdatarutan. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
7. Granska utdata från runbooken i utdatarutan.

## <a name="next-steps"></a>Nästa steg
* Om du vill lära dig mer om att skapa eller importera en runbook, se [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Läs mer om hur du konfigurerar runbooks för att returnera statusmeddelanden och fel, inklusive rekommenderade metoder, i [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)


