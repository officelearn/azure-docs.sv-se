---
title: Smarta grupper
description: Smart grupper är sammanställning av aviseringar som hjälper dig att minska onödig avisering
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 960dc8f2908a20620a84113b27e474b553cd45d4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346491"
---
# <a name="smart-groups"></a>Smarta grupper
En gemensam utmaning kan stöta på när hantering av aviseringar sig igenom bruset för att ta reda på det faktiskt som är viktigt - smart grupper är avsedda att vara lösning på problemet.  

Smart grupper skapas automatiskt med hjälp av maskininlärningsalgoritmer kan kombinera relaterade aviseringar som representerar ett enda problem.  När en avisering skapas, läggs algoritmen det till en ny smart grupp eller en befintlig smart grupp baserat på information som historiska mönster, liknande egenskaper och liknande struktur. Exempel: om % CPU på flera virtuella datorer i en prenumeration aktivitetstoppar samtidigt leder till många enskilda aviseringar och om dessa aviseringar som har inträffat tillsammans när som helst tidigare aviseringarna sannolikt grupperas i en enda grupp för Smart föreslår en eventuella vanliga grundorsaken. Det innebär att för felsökning av aviseringar, smart grupper inte bara tillåter dem att minska bruset genom att hantera relaterade aviseringar som en sammansatt enhet, guidas även dem mot vanliga rotorsaker för sina aviseringar som möjligt.

För närvarande algoritmen som endast tar hänsyn till aviseringar från samma Övervakare tjänst inom en prenumeration. Smart grupper kan minska onödig avisering via denna konsolidering med upp till 99%. Du kan visa orsaken till att aviseringar ingick i en grupp i sidan med smarta grupp.

Du kan visa information om smart grupper och ställa in tillståndet på samma sätt till hur du kan med aviseringar. Varje avisering är medlem i en smart gruppen. 

## <a name="smart-group-state"></a>Smart grupptillstånd
Smart tillstånd är en liknande koncept till aviseringstillståndet, där du kan hantera lösningsprocessen på nivån för en smart grupp. Liknar aviseringstillståndet, när en smart grupp har skapats, har den **New** tillstånd, som du kan ändra antingen **godkänd** eller **stängd**.

Följande smart grupp tillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Ny | Problemet bara har identifierats och har inte har granskat. |
| Bekräftad | En administratör har granskat gruppen smart och mellanrum. |
| Stängd | Problemet har lösts. När en smart grupp har stängts kan öppna du det genom att ändra det till ett annat tillstånd. |

[Lär dig hur du ändrar status för smart gruppen.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Ändra tillståndet för en smart grupp ändrar inte tillståndet för aviseringarna som enskild medlem.

## <a name="smart-group-details-page"></a>Smart sidan med information

Sidan med smarta gruppen visas när du väljer en smart grupp. Den innehåller information om gruppen smart, inklusive resonemang som användes för att skapa gruppen och du kan ändra tillståndet.
 
![Smart grupp information](media/alerts-smartgroups-overview/smart-group-detail.png)


Sidan med smarta grupp innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Aviseringar | Visar en lista över de enskilda aviseringar som ingår i gruppen smart. Välj en avisering för att öppna appens sida aviseringsinformation. |
| Historik | Visar en lista över varje åtgärd som gruppen smart och eventuella ändringar som görs till den. Det här är för närvarande begränsad till tillståndsändringar och avisering Medlemskapsändringar. |

## <a name="smart-group-taxonomy"></a>Smart grupp taxonomi

Namnet på en smart grupp är namnet på den första aviseringen. Du kan inte skapa eller byta namn på en smart grupp.

## <a name="next-steps"></a>Nästa steg

- [Hantera smarta grupper](https://aka.ms/managing-smart-groups)
- [Ändra aviseringen och smart-tillstånd](https://aka.ms/managing-alert-smart-group-states)

