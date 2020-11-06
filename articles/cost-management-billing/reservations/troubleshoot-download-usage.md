---
title: Felsöka nedladdning av användningsinformation för reservationer i Azure
description: I den här artikeln får du hjälp med att förstå och felsöka varför nedladdning av användningsinformation för reserverade instanser inte är tillgänglig i Azure-portalen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147345"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Felsöka nedladdning av användningsinformation för reservationer i Azure

I den här artikeln får du hjälp med att förstå och felsöka varför nedladdning av användningsinformation för reserverade instanser inte är tillgänglig i Azure-portalen.

## <a name="symptoms"></a>Symtom

1. Logga in på [Azure-portalen](https://portal.azure.com/) och gå till **Reservationer**.
1. Välj en reservation.
1. På sidan med reservationsöversikten visar standardvyn användning för de senaste sju dagarna. Du kan välja **Ladda ned som CSV** om du vill ladda ned reservationens användningsinformation.
1. När du ändrar tidsintervallet blir alternativet för att **Ladda ned som CSV** otillgängligt.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Exempel där alternativet Ladda ned som CSV är otillgängligt" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Orsak

På grund av tekniska begränsningar stöder Azure inte nedladdning av data för längre perioder än sju dagar. Långa perioder för kunder med stora mängder reservationer genererar stora mängder data. Returnering av data via API:er belastar Azure-resurser.

## <a name="solution"></a>Lösning

Vi förstår att kunder vill ladda ned data för längre perioder. För närvarande finns det ändå inget sätt att ladda ned användningsdata för reservationer för långa tidsperioder.

## <a name="next-steps"></a>Nästa steg

- Du kan läsa mer om reservationer i [Vad är Azure-reservationer?](save-compute-costs-reservations.md)