---
title: Optimera kostnader för Azure Databricks med en Pre-Purchase
description: Lär dig hur du kan betala i förskott för Azure Databricks avgifter med reserverad kapacitet att spara pengar.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811263"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optimera kostnader för Azure Databricks med en Pre-Purchase

Du kan spara på Azure Databricks unit (DBU) kostnaderna när du förhandsköpa Azure Databricks genomför enheter (DBCU) för ett eller tre år. Du kan använda köpt DBCUs när som helst under perioden för köp. Till skillnad från virtuella datorer, köpt enheter upphör att gälla inte per timme och du kan använda dem när som helst under perioden för köpet.

Azure Databricks användning dras från köpt Dbu automatiskt. Du behöver inte distribuera om eller associera en köpt plan för din Azure Databricks-arbetsytor för DBU-användning att hämta Pre-Purchase rabatter.

Pre-Purchase-rabatten gäller endast för DBU-användning. Andra avgifter, till exempel beräkning, lagring och nätverk debiteras separat.

## <a name="determine-the-right-size-to-buy"></a>Fastställa rätt storlek för att köpa

Databricks Pre-Purchase gäller för alla Databricks arbetsbelastningar och nivåer. Du kan tänka dig Pre-Purchase som en pool av förbetald Databricks commit-enheter. Användning dras från poolen, oavsett arbetsbelastning eller nivå. Användning av i följande förhållanden:

| **Arbetsbelastning** | **DBU-förhållande för program – Standard-nivån** | **Förhållandet mellan DBU-program – Premium-nivån** |
| --- | --- | --- |
| Datanalys | 0.4 | 0.55 |
| Datateknik | 0.15 | 0,30 |
| Datateknik (lättversion) | 0.07 | 0.22 |

Till exempel när en mängd Data Analytics – Standard-nivån förbrukas, köpt Databricks commit enheter dras av 0,4 enheter.

Innan du köper beräkna den totala DBU kvantiteten som används för olika arbetsbelastningar och nivåer. Använd föregående förhållanden för att normalisera till DBCU och kör sedan en projektion av totala användning över bredvid ett eller tre år.

## <a name="purchase-databricks-commit-units"></a>Köpa Databricks commit-enheter

Du kan köpa Databricks planer den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Du måste ha rollen ägare för minst en enterprise-prenumeration om du vill köpa reserverad kapacitet.

- För närvarande kan köpa förbetald är bara tillgängligt för Enterprise Agreement-kunder.
- Du måste vara i en ägarrollen för minst en Enterprise-prenumeration.
- För Enterprise-prenumerationer, **lägga till reserverade instanser** måste aktiveras i den [EA-portalen](https://ea.azure.com/). Eller, om den här inställningen har inaktiverats kan du måste vara en EA-administratör för prenumerationen.

**Att köpa:**

1. Gå till [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Välj en prenumeration. Använd den **prenumeration** och välj den prenumeration som används för att betala för reserverad kapacitet. Betalningsmetoden för prenumerationen debiteras startavgifter för reserverad kapacitet. Avgifter dras av från den registrering saldo för åtagandebelopp eller debiteras som överanvändning.
1. Välj ett omfång. Använd den **omfång** och välj en prenumeration omfattning:
    - **Enkel resurs Gruppomfång** – reservationsrabatten avser matchande resurserna i den valda resursgruppen bara.
    - **Enkel prenumerationsomfattningen** – reservationsrabatten avser de matchande resurserna i den valda prenumerationen.
    - **Dela omfattning** – gäller rabatten för matchande resurser i berättigade prenumerationer som är i kontexten för fakturering. För Enterprise Agreement-kunder är faktureringskontexten registreringen.
1. Välj hur många Azure Databricks commit-enheter som du vill köpa och slutför köpet.


![Exempel på Azure Databricks köp på Azure-portalen](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Ändra omfång och ägarskap

Du kan göra följande typer av ändringar till en reservation efter köpet:

- Uppdatera reservationsomfånget
- Rollbaserad åtkomst

Du kan inte dela eller slå samman Databricks commit enhet Pre-Purchase. Läs mer om hur du hanterar reservationer [hantera reservationer efter köpet](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Avbokningar och utbyten

Avbryt och exchange stöds inte för Databricks Pre-Purchase planer. Alla inköp är slutgiltiga.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Azure reservationer, finns i följande artiklar:
  - [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
  - [Förstå hur en rabatt på Azure Databricks Pre-Purchase DBCU tillämpas](billing-reservation-discount-databricks.md)
  - [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
