---
title: Förstå hur den automatiska migreringsprocessen för dina klassiska Azure Monitor-aviseringar fungerar
description: Läs om hur den automatiska migreringsprocessen fungerar.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668255"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Förstå den automatiska migreringsprocessen för dina klassiska varningsregler

Som [tidigare meddelats](monitoring-classic-retirement.md)dras klassiska aviseringar i Azure Monitor tillbaka i september 2019 (var ursprungligen juli 2019). Som en del av pensionsprocessen är [ett migreringsverktyg](alerts-using-migration-tool.md) tillgängligt i Azure-portalen för kunder att utlösa migreringen själva. Om du inte har använt migreringsverktyget senast den 31 augusti 2019 startar Azure Monitor processen med automatisk migrering av dina klassiska aviseringar från och med den 1 september 2019.
I den här artikeln får du hjälp med den automatiska migreringsprocessen och hjälper dig att lösa eventuella problem som du kan stöta på.

  > [!NOTE]
  > Den här artikeln gäller endast för offentligt Azure-moln. Pensioneringsprocessen för klassiska Azure Monitor-aviseringar i Azure Government-molnet och Azure China 21Vianet kommer att tillkännages vid framtida datum.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Vad händer under den automatiska migreringsprocessen?

- Från och med **den 1 september 2019**kan kunderna inte skapa några nya klassiska varningsregler förutom på [vissa mätvärden.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)
  - För undantagen kan kunden fortsätta att skapa nya klassiska varningsregler och använda sina klassiska aviseringar fram till juni 2020.
- Från och med **den 1 september 2019**utlöses migreringen av klassiska aviseringar i batchar för alla kunder som har klassiska aviseringar.
- Precis som med det frivilliga migreringsverktyget kommer vissa klassiska varningsregler som inte kan migreras att lämnas som de är. Dessa klassiska varningsregler kommer att fortsätta att stödjas fram till juni 2020. Alla ogiltiga klassiska varningsregler tas dock bort eftersom de inte är funktionella.
Klassiska varningsregler som övervakar borttagna målresurser eller [mått som inte längre stöds](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) anses ogiltiga.
- När migreringen för prenumerationen startar, om det inte finns några problem, ska migreringen vara klar inom en timme. Kunder kan övervaka status för migrering på [migreringsbladet i Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Prenumerationsägare får ett e-postmeddelande när migreringen har slutförts.
- Om det finns några problem under migreringen får prenumerationsägare också ett e-postmeddelande som informerar dem om samma. Kunder kan använda migreringsbladet för att se fullständig information om problemet.
- Om en åtgärd behövs från kunden som att tillfälligt inaktivera ett resurslås eller ändra en principtilldelning måste kunderna lösa eventuella problem senast den 31 oktober 2019. Om problemen inte är lösta då kan en lyckad migrering av dina klassiska aviseringar inte garanteras.

    > [!NOTE]
    > Om du inte vill vänta på att den automatiska migreringsprocessen ska starta kan du fortfarande utlösa migreringen frivilligt med migreringsverktyget.

## <a name="important-things-to-note"></a>Viktiga saker att notera

Migreringsprocessen konverterar klassiska varningsregler till nya, likvärdiga varningsregler och skapar åtgärdsgrupper. Var en förberedelse av följande punkter:

- Varningsformaten för nya varningsregler skiljer sig från de klassiska varningsreglerna eftersom de stöder fler funktioner. Om du har logikappar, runbooks eller webhooks som utlöses av klassisk varningsregel kan de sluta fungera som förväntat när migreringen är klar på grund av skillnader i meddelandenyttolaster. [Läs om hur du förbereder migreringen](alerts-prepare-migration.md).

- Vissa klassiska varningsregler kan inte migreras med hjälp av verktyget. [Lär dig vilka regler som inte kan migreras och vad du ska göra med dem.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Migreringsprocessen påverkar inte utvärderingen av dina klassiska varningsregler. De fortsätter att köra och skicka aviseringar tills de migreras och de nya varningsreglerna börjar gälla.

## <a name="what-if-the-automatic-migration-fails"></a>Vad händer om den automatiska migreringen misslyckas?

När den automatiska migreringsprocessen misslyckas får prenumerationsägare ett e-postmeddelande om problemet. Du kan använda migreringsbladet i Azure Monitor för att se fullständig information om problemet.

Mer information finns i [felsökningsguiden](alerts-understand-migration.md#common-problems-and-remedies) om du vill ha hjälp med problem som kan uppstå under migreringen.

  > [!NOTE]
  > Om en åtgärd behövs från kunden som att tillfälligt inaktivera ett resurslås eller ändra en principtilldelning måste kunderna lösa eventuella problem senast den 31 oktober 2019. Om problemen inte är lösta då kan en lyckad migrering av dina klassiska aviseringar inte garanteras.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migreringen](alerts-prepare-migration.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)
