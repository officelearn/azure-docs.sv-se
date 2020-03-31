---
title: Använda hjälpmedelsfunktioner i designern (förhandsgranskning)
titleSuffix: Azure Machine Learning
description: Läs mer om tangentbordsgenvägar och hjälpmedelsfunktioner för skärmläsare som är tillgängliga i designern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366191"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Använda ett tangentbord för att använda Azure Machine Learning designer (förhandsversion)

Lär dig hur du använder en tangentbords- och skärmläsare för att använda Azure Machine Learning-designern. En lista över kortkommandon som fungerar överallt i Azure-portalen finns [i Kortkommandon i Azure-portalen](../azure-portal/azure-portal-keyboard-shortcuts.md)

Arbetsflödet har testats med [Skärmläsaren](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) och [JAWS](https://www.freedomscientific.com/products/software/jaws/), men det ska fungera med andra vanliga skärmläsare.

## <a name="navigate-the-pipeline-graph"></a>Navigera i pipelinediagrammet

Pipelinediagrammet är ordnat som en kapslad lista. Den yttre listan är en modullista som beskriver alla moduler i pipelinediagrammet. Den inre listan är en anslutningslista som beskriver alla anslutningar för en viss modul.  

1. I modullistan använder du piltangenten för att byta moduler.
1. Använd fliken för att öppna anslutningslistan för målmodulen.
1. Använd piltangenten för att växla mellan anslutningsportarna för modulen.
1. Använd "G" för att gå till målmodulen.

## <a name="edit-the-pipeline-graph"></a>Redigera pipelinediagrammet

### <a name="add-a-module-to-the-graph"></a>Lägga till en modul i diagrammet

1. Använd Ctrl+F6 för att växla fokus från arbetsytan till modulträdet.
1. Hitta önskad modul i modulträdet med hjälp av standard treeview kontroll.

### <a name="edit-a-module"></a>Redigera en modul

Så här ansluter du en modul till en annan modul:

1. Använd Ctrl + Skift + H när du riktar in en modul i modullistan för att öppna anslutningshjälparen.
1. Redigera anslutningsportarna för modulen.

Så här justerar du modulegenskaperna:

1. Använd Ctrl + Skift + E när du riktar in dig på en modul för att öppna modulegenskaperna.
1. Redigera modulegenskaperna.

## <a name="navigation-shortcuts"></a>Navigeringsgenvägar

| Tangenttryckning | Beskrivning |
|-|-|
| Ctrl + F6 | Växla fokus mellan arbetsyta och modulträd |
| Ctrl + F1   | Öppna informationskortet när du fokuserar på en nod i modulträdet |
| Ctrl + Skift + H | Öppna anslutningshjälparen när fokus ligger på en nod |
| Ctrl + Skift + E | Öppna modulegenskaper när fokus ligger på en nod |
| Ctrl + G | Flytta fokus till den första misslyckade noden om pipelinekörningen misslyckades |

## <a name="action-shortcuts"></a>Åtgärdsgenvägar

Använd följande kortkommandon med åtkomstnyckeln. Mer information om åtkomstnycklar https://en.wikipedia.org/wiki/Access_keyfinns i .

| Tangenttryckning | Åtgärd |
|-|-|
| Åtkomstnyckel + R | Kör |
| Åtkomstnyckel + P | Publicera |
| Åtkomstnyckel + C | Klona |
| Åtkomstnyckel + D | Distribuera |
| Åtkomstnyckel + I | Pipeline för inferens för att skapa/uppdatera inferens |
| Åtkomstnyckel + B | Pipeline för inferenspipeline för att skapa/uppdatera batch |
| Åtkomstnyckel + K | Öppna listrutan "Skapa slutledningspipeline" |
| Åtkomstnyckel + U | Öppna listrutan "Uppdatera inferenspipeline" |
| Åtkomstnyckel + M | Öppna listrutan Mer(...) |

## <a name="next-steps"></a>Nästa steg

- [Aktivera hög kontrast eller ändra tema](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Hjälpmedelsrelaterade verktyg på Microsoft](https://www.microsoft.com/accessibility)
