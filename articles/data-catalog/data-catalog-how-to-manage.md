---
title: Hantera datatillgångar i Azure Data Catalog
description: Artikeln visar hur du kontrollerar synlighet och ägarskap för datatillgångar som registrerats i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736332"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Hantera datatillgångar i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är utformad för identifiering av datakällor, så att du enkelt kan identifiera och förstå de datakällor du behöver för att utföra analyser och fatta beslut. Dessa identifieringsfunktioner gör störst effekt när du och andra användare kan hitta och förstå det bredaste utbudet av tillgängliga datakällor. Med dessa element i åtanke är standardbeteendet för Data Catalog att alla registrerade datakällor ska vara synliga för och upptäckbara av alla kataloganvändare.

Data Catalog ger dig inte tillgång till själva data. Dataåtkomsten styrs av datakällans ägare. Med Data Catalog kan du identifiera datakällor och visa metadata som är relaterade till de källor som är registrerade i katalogen.

Det kan dock finnas situationer där datakällor endast bör vara synliga för specifika användare eller för medlemmar i specifika grupper. I sådana fall kan användare bli ägare till registrerade datatillgångar i katalogen och sedan kontrollera synligheten för de tillgångar de äger.

> [!NOTE]
> De funktioner som beskrivs i den här artikeln är endast tillgängliga i Standard Edition av Azure Data Catalog. Free Edition innehåller inga funktioner för ägarskap och begränsning av synligheten för datatillgångar.
>
>

## <a name="manage-ownership-of-data-assets"></a>Hantera ägarskap för datatillgångar
Som standard är datatillgångar som är registrerade i Data Catalog oägda. Alla användare som har behörighet att komma åt katalogen kan identifiera och kommentera dessa tillgångar. Användare kan bli ägare till oägda datatillgångar och sedan begränsa synligheten för de tillgångar de äger.

När en datatillgång i Data Catalog ägs kan endast användare som är auktoriserade av ägarna identifiera tillgången och visa dess metadata, och endast ägarna kan ta bort tillgången från katalogen.

> [!NOTE]
> Ägarskap i datakatalogen påverkar bara de metadata som lagras i katalogen. Ägarskap ger inte några behörigheter för den underliggande datakällan.
>
>

### <a name="take-ownership"></a>Bli ägare
Användare kan bli ägare till datatillgångar genom att välja alternativet **Ta ägarskap** i datakatalogportalen. Inga särskilda behörigheter krävs för att bli ägare till en oägd datatillgång. Alla användare kan bli ägare till en oägd datatillgång.

### <a name="add-owners-and-co-owners"></a>Lägg till ägare och delägare
Om en datatillgång redan ägs kan andra användare inte bara bli ägare. De måste läggas till som delägare av en befintlig ägare. Alla ägare kan lägga till ytterligare användare eller säkerhetsgrupper som delägare.

> [!NOTE]
> Det är en bästa praxis att ha minst två personer som ägare för alla ägda datatillgång.
>
>

### <a name="remove-owners"></a>Ta bort ägare
Precis som alla tillgångsägare kan lägga till delägare, kan alla ägare av tillgångar ta bort alla delägare.

En tillgångsägare som tar bort sig själva som ägare kan inte längre hantera tillgången. Om tillgångsägaren tar bort sig själva som ägare och det inte finns några andra delägare återgår tillgången till ett oägt tillstånd.

## <a name="control-visibility"></a>Kontrollera synlighet
Data-tillgångsägare kan kontrollera synligheten för de datatillgångar de äger. Om du vill begränsa synligheten som standard, där alla datakataloganvändare kan identifiera och visa datatillgången, kan tillgångsägaren växla synlighetsinställningen från **Alla** till **ägare & Dessa användare** i egenskaperna för tillgången. Ägare kan sedan lägga till specifika användare och säkerhetsgrupper.

> [!NOTE]
> När det är möjligt bör behörigheter för ägarskap och synlighet för tillgångar tilldelas säkerhetsgrupper och inte till enskilda användare.
>
>

## <a name="catalog-administrators"></a>Katalogadministratörer
Datakatalogadministratörer är implicit delägare i alla tillgångar i katalogen. Tillgångsägare kan inte ta bort synlighet från administratörer och administratörer kan hantera ägarskap och synlighet för alla datatillgångar i katalogen.

## <a name="summary"></a>Sammanfattning
Datakatalogen crowdsourcing modell till metadata och data asset discovery tillåter alla kataloganvändare att bidra och upptäcka. Standardutgåvan av datakatalogen är utformad för ägarskap och hantering för att begränsa synligheten och användningen av specifika datatillgångar.
