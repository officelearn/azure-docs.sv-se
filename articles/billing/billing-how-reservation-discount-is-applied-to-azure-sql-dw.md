---
title: Hur reservation rabatter som gäller för Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig hur reservation rabatter som gäller för Azure SQL Data Warehouse för att spara pengar.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918432"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Hur reservation rabatter som gäller för Azure SQL Data Warehouse

När du köper Azure SQL Data Warehouse reserverad kapacitet tillämpas i reservationsrabatten automatiskt till datalager som finns i den regionen. Rabatten gäller för användning som anges av SQL Data Warehouse cDWU mätaren. Lagring och nätverk debiteras enligt priserna för användningsbaserad betalning.

## <a name="reservation-discount-application"></a>Tillämpningen av rabatter för reservation

SQL Data Warehouse rabatten reserverad kapacitet till att köra informationslager per timme. Om du inte har ett informationslager som distribueras i en timme, gått reserverad kapacitet för den timmen. Den sprids inte.

Efter köpet matchas som du köper reservationen till SQL Data Warehouse användning anges genom att köra informationslager när som helst i tid. Om du stänger av vissa distributionslager sedan tillämpa reservation rabatter automatiskt på de matchande lager.

För informationslager som inte körs för en hel timme, används automatiskt reservationen för andra matchande instanser i den timmen.

## <a name="discount-examples"></a>Rabatt exempel

I följande exempel visas hur SQL Data Warehouse reserverad kapacitet rabatten gäller beroende på distributioner.

- **Exempel 1**: Du köper 5 enheter om 100 cDWU reserverad kapacitet. Du kan köra en DW1500c SQL Data Warehouse-instans i en timme. I det här fallet anges användning för 15 enheter av 100 cDWU-användning. Rabatten gäller för de 5 enheter som du använde. Du debiteras med priserna för användningsbaserad betalning för återstående 10 enheter med 100 cDWU användning som du använde.

- **Exempel 2**: Du köper 5 enheter om 100 cDWU reserverad kapacitet. Du kan köra två DW100c SQL Data Warehouse-instanser i en timme. I det här fallet genereras två användning händelserna för 1 enhet av 100 cDWU-användning. Båda Användningshändelser få rabatter reserverad kapacitet. Återstående 3 enheter av 100 cDWU reserverad kapacitet har gått och sprids inte för framtida användning.

- **Exempel 3**: Du kan köpa 1 enhet av 100 cDWU reserverad kapacitet. Du kör två DW100c SQL Data Warehouse-instanser. Båda körs under 30 minuter. I det här fallet få både Användningshändelser reserverad kapacitet rabatter. Ingen användning debiteras med priserna för användningsbaserad betalning.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

- Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Visa transaktioner för reservation](billing-view-reservations.md)
- [Hämta reserverade transaktioner och användning via API: et](billing-reservation-apis.md)
- [Hantera reservationer](billing-manage-reserved-vm-instance.md)
