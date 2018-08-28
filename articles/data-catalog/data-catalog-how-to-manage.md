---
title: Hantera datatillgångar i Azure Data Catalog
description: Artikeln visar hur du styr synlighet och ägarskapet för datatillgångar som har registrerats i Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e102b1f436b4f6d39f57445b02638ffd11f27786
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053437"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Hantera datatillgångar i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är utformat för identifiering av datakällan, så att du enkelt kan identifiera och förstå datakällorna måste du utföra analyser och fatta beslut. Dessa funktioner för identifiering av få den största effekten när du och andra användare kan hitta och förstå det bredaste utbudet av tillgängliga datakällor. Med de här elementen i åtanke är standardbeteendet för Data Catalog för alla registrerade datakällor ska vara synliga för och kan identifieras av alla kataloganvändare.

Data Catalog ger inte åtkomst till själva informationen. Dataåtkomst styrs av ägaren av datakällan. Med Data Catalog kan du identifiera datakällor och visa metadata som är relaterade till de datakällor som är registrerade i katalogen.

Det kan finnas situationer, men där datakällor endast bör visas för särskilda användare eller för medlemmar i specifika grupper. Användare kan i sådana scenarier kan bli ägare av registrerade datatillgångar i katalogen och styra visningen av de resurser som de äger.

> [!NOTE]
> Funktioner som beskrivs i den här artikeln är bara tillgängliga i Standard Edition av Azure Data Catalog. Den kostnadsfria versionen ger inte funktioner för ägarskap och begränsa datatillgång synlighet.
>
>

## <a name="manage-ownership-of-data-assets"></a>Hantera ägarskapet för datatillgångar
Som standard är datatillgångar som är registrerade i Data Catalog oägd. Alla användare med behörighet att komma åt katalogen kan identifiera och anteckna dessa tillgångar. Användare kan bli ägare till oägd datatillgångar och begränsa visningen av de resurser som de äger.

När en datatillgång i Data Catalog ägs endast användare som har auktoriserats av ägare kan identifiera tillgången och visa dess metadata och endast ägare kan ta bort tillgången från katalogen.

> [!NOTE]
> Ägarskap i Data Catalog påverkar endast de metadata som lagras i katalogen. Ägarskap ger inte några behörigheter på den underliggande datakällan.
>
>

### <a name="take-ownership"></a>Överta ägarskap
Användare kan ta över ägarskapet för datatillgångar genom att välja den **bli ägare** alternativ i Data Catalog-portalen. Inga särskilda behörigheter krävs för att överta ägarskapet för en oägd datatillgången. Alla användare kan bli ägare till en oägd datatillgången.

### <a name="add-owners-and-co-owners"></a>Lägg till ägare och Medägare
Om en datatillgång ägs redan, kan inte andra användare bara bli ägare. De måste läggas till som Medägare av en befintlig ägare. Alla ägare kan lägga till ytterligare användare eller säkerhetsgrupper som Medägare.

> [!NOTE]
> Det är en bra idé att ha minst två personer som ägare för alla ägda datatillgångar.
>
>

### <a name="remove-owners"></a>Ta bort ägare
Precis som alla tillgångens ägare kan lägga till Medägare måste alla tillgångens ägare kan ta bort alla Medägare.

En tillgångsägare som tar bort sig själv som ägare kan inte längre hantera tillgången. Om tillgångens ägare tar bort sig själv som ägare och det finns inga andra Medägare, återgår tillgången till oägd status.

## <a name="control-visibility"></a>Kontrollen synlighet
Data-tillgångsägare kan styra synligheten för de dataresurser som de äger. Om du vill begränsa synligheten som standard, där alla Data Catalog-användare kan identifiera och visa datatillgången, tillgångens ägare kan växla synlighetsinställningen från **alla** till **ägare och dessa användare** i Egenskaper för tillgången. Ägare kan sedan lägga till specifika användare och säkerhetsgrupper.

> [!NOTE]
> När det är möjligt ska tillgången ägarskap och synlighet behörigheter tilldelas till säkerhetsgrupper och inte till enskilda användare.
>
>

## <a name="catalog-administrators"></a>Katalogadministratörer
Data Catalog-administratörer är implicit Medägare över alla tillgångar i katalogen. Tillgångsägare kan inte ta bort synlighet från administratörer och administratörer kan hantera ägarskap och synlighet för alla datatillgångar i katalogen.

## <a name="summary"></a>Sammanfattning
Data Catalog gemensam modell för metadata och data tillgångsidentifiering kan alla kataloganvändare bidra och identifiera. Standard Edition av Data Catalog är utformat för ägarskap och hantering för att begränsa synligheten och användningen av specifika datatillgångar.
