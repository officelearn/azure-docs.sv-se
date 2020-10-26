---
title: Felsöka Azure-reservationsförbrukning
description: Den här artikeln hjälper dig att förstå och felsöka Azure-reservationer som visar ingen eller noll (0) förbrukning i Azure-portalen. Förbrukning som inte matchar förklaras också.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207444"
---
# <a name="troubleshoot-reservation-utilization"></a>Felsöka reservationsförbrukning

Den här artikeln hjälper dig att förstå och felsöka Azure-reservationer som visar ingen eller noll (0) förbrukning i Azure-portalen. Förbrukning som inte matchar förklaras också.

## <a name="symptoms"></a>Symtom

1. Logga in på [Azure-portalen](https://portal.azure.com) och gå till **Reservationer** .
1. I listan över reservationer ser du mängden förbrukning för en reservation i kolumnen **Utilization (%)** (Förbrukning (%)). Det kan vara 0 %.
1. Välj reservationen.
1. På sidan för reservationsöversikt matchar din använda procentandel i grafen kanske inte det värde som visas i reservationslistan.

## <a name="cause"></a>Orsak

I kolumnen **Utilization (%)** (Förbrukning (%)) i Azure-portalen visas värdet för aktuell dag. Värdet beräknas när användningsdata tas emot från den plats där resurser körs. Azure använder användningen till att beräkna förbrukningsprocenten.

En del resurser rapporterar användning långsammare än andra. Dessutom rapporterar vissa produkttyper såsom SQL-databaser sina användningsdata långsamt.

Svarstiden kan leda till att förbrukningsberäkningen visar lägre värden än den faktiska användningen. Skillnaden är märkbar vid dagsgränsen. Om Azure inte hämtar användningsdata inom fyra till åtta timmar beräknar det i så fall värdet 0 %. Värdet 0 % visas eftersom användningsdata inte har tagits emot och det visar sig att reservationen inte tillämpar en fördel på några resurser.

När användningsdata tas emot ändras värdet mot rätt procentandel. När alla användningsdata har samlats in fastställs rätt värde och visas korrekt i grafen.

## <a name="solution"></a>Lösning

Om du upptäcker att dina förbrukningsvärden inte uppfyller dina förväntningar kan du läsa grafen för att få den största vyn av din faktiska användning. Alla punktvärden som är äldre än två dagar bör vara korrekta. Längre genomsnitt för perioder från 7 till 30 dagar bör vara korrekta.

## <a name="next-steps"></a>Nästa steg

- Mer information om hantering av reservationer finns i [Hantera reservationer för Azure-resurser](manage-reserved-vm-instance.md).