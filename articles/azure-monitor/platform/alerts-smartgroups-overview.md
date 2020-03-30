---
title: Smarta grupper
description: Smarta grupper är aggregeringar av aviseringar som hjälper dig att minska varningsbrus
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665501"
---
# <a name="smart-groups"></a>Smarta grupper

En gemensam utmaning när man hanterar varningar är att sålla igenom bullret för att ta reda på vad som faktiskt betyder något - smarta grupper är avsedda att vara lösningen på detta problem.  

Smarta grupper skapas automatiskt med hjälp av maskininlärningsalgoritmer för att kombinera relaterade aviseringar som representerar ett enda problem.  När en avisering skapas lägger algoritmen till den i en ny smart grupp eller en befintlig smart grupp baserat på information som historiska mönster, liknande egenskaper och liknande struktur. Om % CPU på flera virtuella datorer i en prenumeration samtidigt spikar som leder till många enskilda aviseringar, och om sådana aviseringar har inträffat tillsammans när som helst tidigare, kommer dessa aviseringar sannolikt att grupperas i en enda smart grupp, vilket tyder på en potentiella gemensamma grundorsaken. Detta innebär att för någon felsökning av varningar, smarta grupper inte bara tillåter dem att minska buller genom att hantera relaterade aviseringar som en enda aggregerad enhet, det också vägleder dem mot möjliga gemensamma grundläggande orsaker till sina varningar.

För närvarande tar algoritmen endast hänsyn till aviseringar från samma övervakartjänst i en prenumeration. Smarta grupper kan minska upp till 99 % av varningsbruset genom den här konsolideringen. Du kan visa orsaken till att aviseringar ingick i en grupp på informationssidan för smarta grupper.

Du kan visa information om smarta grupper och ange tillståndet på samma sätt som du kan med aviseringar. Varje avisering är medlem i en och endast en smart grupp. 

## <a name="smart-group-state"></a>Smart grupptillstånd

Smart grupptillstånd är ett liknande begrepp som aviseringstillståndet, vilket gör att du kan hantera lösningsprocessen på nivån för en smart grupp. I likhet med aviseringstillståndet har **den** nya tillståndet, när en smart grupp skapas, som kan ändras till antingen **Bekräftad** eller **Stängd**.

Följande smarta grupptillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Ny | Problemet har just upptäckts och har ännu inte granskats. |
| Bekräftad | En administratör har granskat den smarta gruppen och börjat arbeta med den. |
| Stängd | Problemet har lösts. När en smart grupp har stängts kan du öppna den igen genom att ändra den till ett annat tillstånd. |

[Läs om hur du ändrar tillståndet för din smarta grupp.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Om du ändrar tillståndet för en smart grupp ändras inte tillståndet för de enskilda medlemsaviseringarna.

## <a name="smart-group-details-page"></a>Informationssida för smart grupp

Informationssidan för smart grupp visas när du väljer en smart grupp. Den innehåller information om den smarta gruppen, inklusive det resonemang som användes för att skapa gruppen, och gör att du kan ändra dess tillstånd.
 
![Information om smart grupp](media/alerts-smartgroups-overview/smart-group-detail.png)


Den smarta gruppinformationssidan innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Aviseringar | Visar en lista över de enskilda aviseringar som ingår i den smarta gruppen. Välj en avisering för att öppna dess aviseringsdetaljsida. |
| Historik | Visar en lista över varje åtgärd som vidtas av den smarta gruppen och eventuella ändringar som görs i den. Detta är för närvarande begränsat till tillståndsändringar och ändringar av aviseringar. |

## <a name="smart-group-taxonomy"></a>Smart grupptaxonomi

Namnet på en smart grupp är namnet på den första aviseringen. Du kan inte skapa eller byta namn på en smart grupp.

## <a name="next-steps"></a>Nästa steg

- [Hantera smarta grupper](https://aka.ms/managing-smart-groups)
- [Ändra din varning och smarta grupptillstånd](https://aka.ms/managing-alert-smart-group-states)


