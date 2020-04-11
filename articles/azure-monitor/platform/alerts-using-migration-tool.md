---
title: Migrera Azure Monitor-aviseringsregler
description: Läs om hur du använder det frivilliga migreringsverktyget för att migrera dina klassiska varningsregler.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114251"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Använd det frivilliga migreringsverktyget för att migrera dina klassiska varningsregler

Som [tidigare meddelats](monitoring-classic-retirement.md)dras klassiska aviseringar i Azure Monitor tillbaka i september 2019 (var ursprungligen juli 2019). Ett migreringsverktyg är tillgängligt i Azure-portalen för kunder som använder klassiska varningsregler och som vill utlösa migreringen själva. I den här artikeln beskrivs hur du använder migreringsverktyget för att frivilligt migrera dina klassiska varningsregler innan den automatiska migreringen startar i september 2019.

> [!NOTE]
> På grund av förseningar i utrullningen av migreringsverktyget har pensioneringsdatumet för klassisk [registreringsmigrering förlängts till den 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) från det ursprungligen meddelade datumet den 30 juni 2019.

## <a name="benefits-of-new-alerts"></a>Fördelar med nya varningar

Klassiska aviseringar ersätts av nya, enhetliga aviseringar i Azure Monitor. Den nya varningsplattformen har följande fördelar:

- Du kan avisera en mängd flerdimensionella mått för [många fler Azure-tjänster](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- De nya måttaviseringarna stöder [aviseringsregler för flera resurser](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) som kraftigt minskar kostnaderna för att hantera många regler.
- Den enhetliga anmälningsmekanismen, som stöder:
  - [Åtgärdsgrupper](action-groups.md), en modulär meddelandemekanism som fungerar med alla nya varningstyper (mått, logg och aktivitetslogg).
  - Nya meddelandemekanismer som SMS, röst och ITSM Connector.
- Den [enhetliga varningsupplevelsen](alerts-overview.md) ger alla aviseringar på olika signaler (mått, logg och aktivitetslogg) på ett ställe.

## <a name="before-you-migrate"></a>Innan du migrerar

Migreringsprocessen konverterar klassiska varningsregler till nya, likvärdiga varningsregler och skapar åtgärdsgrupper. Var en förberedelse av följande punkter:

- Både formatet för meddelandenyttolast och API:er för att skapa och hantera nya varningsregler skiljer sig från de klassiska varningsreglerna eftersom de stöder fler funktioner. [Läs om hur du förbereder migreringen](alerts-prepare-migration.md).

- Vissa klassiska varningsregler kan inte migreras med hjälp av verktyget. [Lär dig vilka regler som inte kan migreras och vad du ska göra med dem](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Migreringsprocessen påverkar inte utvärderingen av dina klassiska varningsregler. De fortsätter att köra och skicka aviseringar tills de migreras och de nya varningsreglerna börjar gälla.

## <a name="how-to-use-the-migration-tool"></a>Använda migreringsverktyget

Så här utlöser du migrering av dina klassiska varningsregler i Azure-portalen:

1. Välj **Övervaka**i [Azure-portalen](https://portal.azure.com).

1. Välj **Aviseringar**och välj sedan **Hantera varningsregler** eller **Visa klassiska aviseringar**.

1. Välj **Migrera till nya regler** för att gå till migreringsmålsidan. På den här sidan visas en lista över alla dina prenumerationer och deras migreringsstatus:

    ![migration-landning](media/alerts-migration/migration-landing.png "Migrera regler")

    Alla prenumerationer som kan migreras med verktyget markeras som **Redo att migrera**.

    > [!NOTE]
    > Migreringsverktyget lanseras i faser till alla prenumerationer som använder klassiska varningsregler. I de tidiga faserna av distributionen kan vissa prenumerationer markeras som inte redo för migreringen.

1. Välj en eller flera prenumerationer och välj sedan **Förhandsgranska migrering**.

    Den resulterande sidan visar information om klassiska varningsregler som ska migreras för en prenumeration i taget. Du kan också välja **Hämta migreringsinformation för den här prenumerationen för** att få information i csv-format.

    ![förhandsgranskning av migrering](media/alerts-migration/migration-preview.png "Förhandsgranska migrering")

1. Ange en eller flera e-postadresser som ska meddelas om migreringsstatus. Du får e-post när migreringen är klar eller om någon åtgärd behövs från dig.

1. Välj **Starta migrering**. Läs informationen som visas i bekräftelsedialogrutan och bekräfta att du är redo att starta migreringen.

    > [!IMPORTANT]
    > När du har initierat migreringen för en prenumeration kan du inte redigera eller skapa klassiska varningsregler för den prenumerationen. Den här begränsningen säkerställer att inga ändringar av dina klassiska varningsregler går förlorade under migreringen till de nya reglerna. Även om du inte kan ändra dina klassiska varningsregler fortsätter de fortfarande att köras och ger aviseringar tills de har migrerats. När migreringen är klar för prenumerationen kan du inte längre använda klassiska varningsregler.

    ![migration-bekräfta](media/alerts-migration/migration-confirm.png "Bekräfta startmigrering")

1. När migreringen är klar, eller om du behöver en åtgärd, får du ett e-postmeddelande på de adresser som du angav tidigare. Du kan också regelbundet kontrollera statusen på migreringsmålsidan i portalen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Varför visas inte min prenumeration som redo för migrering?

Migreringsverktyget lanseras till kunder i faser. I de tidiga faserna kan de flesta eller alla dina prenumerationer markeras som **Inte redo för migrering**. 

När en prenumeration blir klar för migrering får prenumerationsägaren ett e-postmeddelande om att verktyget är tillgängligt. Håll utkik efter det här meddelandet.

### <a name="who-can-trigger-the-migration"></a>Vem kan utlösa migreringen?

Användare som har rollen Övervakningsdeltagare tilldelad dem på prenumerationsnivå kan utlösa migreringen. [Läs mer om rollbaserad åtkomstkontroll för migreringsprocessen](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Hur lång tid tar migreringen?

Migreringen slutförs för de flesta prenumerationer på mindre än en timme. Du kan hålla reda på migreringsstatusen på migreringsmålsidan. Under migreringen kan du vara säker på att dina aviseringar fortfarande körs antingen i det klassiska varningssystemet eller i det nya.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Vad kan jag göra om jag stöter på ett problem under migreringen?

Mer information finns i [felsökningsguiden](alerts-understand-migration.md#common-problems-and-remedies) om du vill ha hjälp med problem som kan uppstå under migreringen. Om det behövs någon åtgärd från dig för att slutföra migreringen får du ett meddelande på de e-postadresser som du angav när du konfigurerade verktyget.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migreringen](alerts-prepare-migration.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)
