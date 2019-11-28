---
title: Förnya Azure-reservationer automatiskt
description: Lär dig hur du kan förnya Azure-reservationer automatiskt för att fortsätta att få reservationsrabatter.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 7fd74ded4afea56a4ae1baf18895e0b6661b8f58
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225989"
---
# <a name="automatically-renew-reservations"></a>Förnya reservationer automatiskt

Du kan förnya reservationer för att automatiskt köpa en ersättning när en befintlig reservation upphör att gälla. Automatisk förnyelse är ett enkelt sätt att fortsätta få reservationsrabatter. Du slipper även att noggrant övervaka förfallodatumet för en reservation. Med automatisk förnyelse ser du till att inte gå miste om minskade utgifter eftersom du inte behöver förnya manuellt. Inställningen för förnyelse är inaktiverad som standard. Aktivera eller inaktivera förnyelseinställningen när som helst, fram till upphörandet av den befintliga reservationen.

Om du förnyar en reservation skapas en ny reservation när den befintliga reservationen upphör att gälla. Perioden för den befintliga reservationen förlängs inte.

Välj att förnya automatiskt när som helst. Förnyelsepriset är tillgängligt 30 dagar innan en befintlig reservation upphör att gälla. När du aktiverar förnyelse mer än 30 dagar innan reservationen upphör att gälla skickas ett e-postmeddelande med förnyelsekostnader 30 dagar före upphörandet. Reservationspriset kan komma att ändras mellan den tidpunkt då du låser förnyelsepriset och förnyelsetiden. I så fall blir din förnyelsekostnad den lägre av de två kostnaderna. Du kan göra ändringar i reservationskvantiteten. Om du gör det uppdateras förnyelsen till att använda det marknadspris som anges vid tiden för kvantitetsändringen.

Det finns ingen skyldighet att förnya, och du kan välja avbryta förnyelsen när som helst innan den befintliga reservationen upphör att gälla.

## <a name="set-up-renewal"></a>Konfigurera förnyelse

Gå till Azure-portalen > **Reservationer**.

1. Välj reservationen.
2. Klicka på **Förnyelse**.
3. Välj **Automatically purchase a new reservation upon expiry** (Köp en ny reservation automatiskt vid upphörande).  
  ![Exempel som visar en reservationsförnyelse](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Om du inte förnyar

Dina tjänster fortsätter att köras normalt. Du debiteras Betala per användning-priser för din användning efter det att reservationen har upphört.

## <a name="required-renewal-permissions"></a>Nödvändiga behörigheter för förnyelse

Följande villkor måste vara uppfyllda för att du ska kunna förnya en reservation:

- Du måste vara ägare till den befintliga reservationen.
- Du måste vara ägare till prenumerationen om reservationen omfångsanpassas till en enskild prenumeration eller resursgrupp.
- Du måste vara ägare till prenumerationen om den har ett delat omfång.

## <a name="default-renewal-settings"></a>Standardinställningar för förnyelse

Som standard ärver förnyelsen alla egenskaper från den upphörande reservationen. Ett inköp av reservationsförnyelse har samma SKU, region, omfång, faktureringsprenumeration, period och kvantitet.

Du kan dock uppdatera inköpskvantiteten för förnyelsereservation för att optimera dina besparingar.

## <a name="when-the-new-reservation-is-purchased"></a>När den nya reservationen köps

En ny reservation köps när den befintliga reservationen upphör att gälla. Vi försöker förhindra fördröjningar mellan de två reservationerna. Kontinuitet säkerställer att dina kostnader är förutsägbara och du fortsätter att få rabatter.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Ändra överordnad reservation efter angiven förnyelse

Om du gör någon av följande ändringar i den upphörande reservationen avbryts reservationsförnyelsen:

- Dela
- Sammanfoga
- Överföra reservationen från ett konto till ett annat
- Överföra reservationen från en WebDirect-prenumeration till en Enterprise-avtalsprenumeration (EA) eller någon annan inköpsmetod
- Förnya registreringen

Den nya reservationen ärver omfånget och inställningen för flexibel instansstorlek från den upphörande reservationen under förnyelse.

## <a name="new-reservation-permissions"></a>Behörigheter för den nya reservationen

Azure kopierar behörigheterna från den upphörda reservationen till den nya reservationen. Dessutom har prenumerationens kontoadministratör för reservationsköpet åtkomst till den nya reservationen.

## <a name="potential-renewal-problems"></a>Potentiella problem med förnyelse

Azure bearbetar inte förnyelsen om:

- Betalningen inte kan genomföras
- Ett systemfel uppstår under förnyelsen
- Den upphörda SKU:n inte är aktiv under förnyelsen
- Enterprise-avtalet förnyas till ett annat Enterprise-avtal

Du får ett e-postmeddelande om något av de föregående villkoren inträffar och förnyelsen inaktiveras.

## <a name="renewal-notification"></a>Meddelande om förnyelse

E-postmeddelanden skickas till olika personer beroende på din inköpsmetod:

- EA-kunder – e-postmeddelanden skickas till de aviseringskontakter som anges i EA-portalen.
- Enskilda prenumerationskunder med Betala per användning-priser – e-postmeddelanden skickas till användare som anges som kontoadministratörer.
- Cloud Solution Provider-kunder – e-postmeddelanden skickas till partnermeddelandekontakten.

## <a name="next-steps"></a>Nästa steg
- Du kan läsa mer om Azure-reservationer i [Vad är Azure-reservationer?](billing-save-compute-costs-reservations.md)
