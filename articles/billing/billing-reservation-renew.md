---
title: Förnya automatiskt Azure-reservationer
description: Lär dig hur du automatiskt kan förnya Azure-reservationer för att fortsätta att få rabatt rabatter.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679463"
---
# <a name="automatically-renew-reservations"></a>Förnya reservationer automatiskt

Du kan förnya reservationer för att automatiskt köpa en ersättning när en befintlig reservation upphör att gälla. Automatisk förnyelse ger ett enkelt sätt att fortsätta få rabatt rabatter. Du slipper också att noggrant övervaka förfallo datum för en reservation. Med automatisk förnyelse förhindrar du besparingar av besparingar genom att inte behöva förnya manuellt. Förnyelse inställningen är inaktive rad som standard. Aktivera eller inaktivera förnyelse inställningen när som helst, tills den befintliga reservationen upphör att gälla.

Om du förnyar en reservation skapas en ny reservation när den befintliga reservationen upphör att gälla. Den utökar inte den befintliga reservationens period.

Välj att förnya automatiskt när som helst. Förnyelse priset är tillgängligt 30 dagar innan en befintlig reservation upphör att gälla. När du aktiverar förnyelsen mer än 30 dagar innan reservationen upphör att gälla, skickas ett e-postmeddelande om förnyelse kostnader 30 dagar före förfallo datum. Reservations priset kan ändras mellan den tidpunkt då du låser förnyelse priset och förnyelse tiden. I så fall är din förnyelse kostnad lägre än de två kostnaderna. Du kan göra ändringar i reservations antalet. Om du gör det uppdateras förnyelsen till att använda pris uppsättningen på marknaden vid tidpunkten för ändring av kvantiteten.

Det finns ingen skyldighet att förnya och du kan välja att inte förnya förnyelsen innan den befintliga reservationen upphör att gälla.

## <a name="set-up-renewal"></a>Konfigurera förnyelse

Gå till Azure Portal >- **reservationer**.

1. Välj reservationen.
2. Klicka på **förnya**.
3. Välj **köp automatiskt en ny reservation vid förfallo datum**.  
  ![Exempel som visar reservations förnyelse](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Om du inte förnyar

Dina tjänster fortsätter att köras normalt. Du debiteras taxan enligt principen betala per användning när reservationen har löpt ut.

## <a name="required-renewal-permissions"></a>Nödvändiga förnyelse behörigheter

Följande villkor måste vara uppfyllda för att du ska kunna förnya en reservation:

- Du måste vara ägare till den befintliga reservationen.
- Du måste vara ägare till prenumerationen om reservationen är begränsad till en enda prenumeration eller resurs grupp.
- Du måste vara ägare till prenumerationen om den har en delad omfattning.

## <a name="default-renewal-settings"></a>Inställningar för standard förnyelse

Som standard ärver förnyelsen alla egenskaper från den utgående reservationen. Ett inköp av reservations förnyelse har samma SKU, region, omfattning, fakturerings prenumeration, period och kvantitet.

Du kan dock uppdatera inköps antalet för förnyelse reservationen för att optimera dina besparingar.

## <a name="when-the-new-reservation-is-purchased"></a>När den nya reservationen köps

En ny reservation köps när den befintliga reservationen upphör att gälla. Vi försöker förhindra en fördröjning mellan de två reservationerna. Kontinuitet garanterar att dina kostnader är förutsägbara och du fortsätter att få rabatt.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Ändra överordnad reservation efter förnyelse av inställning

Om du gör någon av följande ändringar i den utgående reservationen avbryts reservations förnyelsen:

- Delat
- Slå samman
- Överföra reservationen från ett konto till ett annat
- Överföra reservationen från en WebDirect-prenumeration till en Enterprise Agreement-prenumeration (EA) eller någon annan inköps metod
- Förnya registreringen

Den nya reservationen ärver storleks inställningen för omfattning och instans storlek från den förfallna reservationen under förnyelsen.

## <a name="new-reservation-permissions"></a>Nya reservations behörigheter

Azure kopierar behörigheterna från den förfallna reservationen till den nya reservationen. Dessutom har prenumerations konto administratören för reservations köpet åtkomst till den nya reservationen.

## <a name="potential-renewal-problems"></a>Potentiella förnyelse problem

Azure kan inte bearbeta förnyelsen om:

- Det går inte att samla in betalning
- Ett systemfel uppstår under förnyelsen
- Den utgångna SKU: n är inte aktiv under förnyelsen
- EA-attributet förnyas till ett annat EA

Du får ett e-postmeddelande om något av de föregående villkoren inträffar och förnyelsen inaktive ras.

## <a name="renewal-notification"></a>Förnyelse meddelande

E-postmeddelanden skickas till olika personer beroende på din köp metod:

- EA-kunder – e-postmeddelanden skickas till aviserings kontakterna som anges på EA-portalen.
- Enskilda prenumerations kunder med priser enligt principen betala per användning – e-postmeddelanden skickas till användare som har kon figurer ATS som konto administratörer.
- Cloud Solution Provider-kunder – e-postmeddelanden skickas till partner meddelande kontakten.

## <a name="next-steps"></a>Nästa steg
- Mer information om Azure Reservations finns i [Vad är Azure reservations?](billing-save-compute-costs-reservations.md)
