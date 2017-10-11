---
title: "Hantera datatillgångar i Azure Data Catalog | Microsoft Docs"
description: "Artikeln visar hur du styr synligheten och ägarskapet för datatillgångar som har registrerats i Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 8b9159b7bc4f7b2dac12d9012c6c903e75a6ac16
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Hantera datatillgångar i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är utformad för identifiering av datakällan, så att du lätt kan identifiera och förstå datakällorna måste du utföra analyser och fatta beslut. Dessa funktioner för identifiering av få den största effekten när du och andra användare kan hitta och förstå mycket stort antal typer av datakällor. Med de här elementen i åtanke är standardbeteendet för Data Catalog för alla registrerade datakällor ska vara synlig för och kan identifieras av alla användare i katalogen.

Data Catalog ger inte dig tillgång till själva informationen. Dataåtkomst styrs av ägaren till datakällan. Du kan identifiera datakällor och visa de metadata som är relaterade till datakällor som är registrerade i katalogen med Data Catalog.

Det kan finnas situationer, men var datakällor bara ska visas för specifika användare eller medlemmar i specifika grupper. I sådana fall kan användarna bli ägare till registrerade datatillgångar i katalogen och styra visningen av de resurser som de äger.

> [!NOTE]
> Funktioner som beskrivs i den här artikeln är endast tillgänglig i Standard Edition av Azure Data Catalog. Den kostnadsfria versionen ger inte några funktioner för ägare och begränsning datatillgång synlighet.
>
>

## <a name="manage-ownership-of-data-assets"></a>Hantera ägarskapet för datatillgångar
Som standard är datatillgångar som är registrerade i Data Catalog oägd. En användare med behörighet att få åtkomst till katalogen kan identifiera och kommentera dessa tillgångar. Användare kan bli ägare till oägd datatillgångar och begränsar synligheten för de resurser som de äger.

När en datatillgång i Data Catalog ägs endast användare som är godkända av ägarna kan identifiera tillgången och visa dess metadata och endast ägare kan ta bort tillgången från katalogen.

> [!NOTE]
> Ägarskap i Data Catalog påverkar endast de metadata som lagras i katalogen. Ägarskap ger inte några behörigheter på den underliggande datakällan.
>
>

### <a name="take-ownership"></a>Bli ägare
Användare kan ta över ägarskapet för datatillgångar genom att välja den **bli ägare** alternativ i Data Catalog-portalen. Inga särskilda behörigheter krävs för att överta ägarskapet för en datatillgång oägd. Alla användare kan bli ägare för en datatillgång oägd.

### <a name="add-owners-and-co-owners"></a>Lägg till ägare och Medägare
Om en datatillgång ägs redan, kan inte andra användare bara bli ägare. De måste läggas till som delägare av en befintlig ägare. Alla ägare kan lägga till fler användare eller säkerhetsgrupper som delägare.

> [!NOTE]
> Det är bäst att ha minst två personer som ägare för alla företagsägda datatillgångar.
>
>

### <a name="remove-owners"></a>Ta bort ägare
Precis som alla tillgångens ägare kan lägga till delägare måste alla tillgångens ägare kan ta bort alla Medägare.

En tillgångsägare som tar bort honom eller sig själv som ägare kan inte längre hantera tillgången. Om tillgångens ägare tar bort honom eller sig själv som en ägare och det finns inga andra delägare, återgår tillgången till oägd status.

## <a name="control-visibility"></a>Kontrollen synlighet
Datatillgång ägare kan styra visningen av datatillgångar som de äger. Om du vill begränsa synligheten som standard, där alla Data Catalog-användare kan identifiera och visa data tillgången, tillgångens ägare kan växla mellan inställningar från **alla** till **ägare och dessa användare** i Egenskaper för tillgången. Ägare kan sedan lägga till specifika användare och säkerhetsgrupper.

> [!NOTE]
> När det är möjligt ska tillgången ägarskap och synlighet behörigheter tilldelas till säkerhetsgrupper och inte till enskilda användare.
>
>

## <a name="catalog-administrators"></a>Katalogadministratörer
Data Catalog administratörer är implicit delägare av alla tillgångar i katalogen. Tillgångsinformation ägare kan inte ta bort synlighet från administratörer och administratörer kan hantera ägarskap och synlighet för alla datatillgångar i katalogen.

## <a name="summary"></a>Sammanfattning
Data Catalog gemensam modell för metadata och data tillgången identifiering kan alla katalogens användare bidra och identifiera. I Standard Edition av Data Catalog är utformad för ägare och hantering att begränsa synlighet och användning av specifika datatillgångar.
