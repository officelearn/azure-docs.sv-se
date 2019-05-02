---
title: Migrera din klassiska aviseringar i Azure Monitor med hjälp av verktyget frivillig migrering
description: Lär dig hur du använder verktyget frivillig migrering för att migrera din klassiska Varningsregler.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 55e6b515328df635a423e0ecf736b03e17a90b8f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698425"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Använd verktyget frivillig migrering för att migrera din klassiska Varningsregler

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är dras tillbaka i juli 2019. Migreringsverktyg finns i Azure portal för att kunder som använder klassiska Varningsregler och som vill aktivera migrering själva. Den här artikeln förklarar hur du använder Migreringsverktyget för att migrera din klassiska Varningsregler frivilligt innan automatisk migrering startar i juli 2019.

## <a name="benefits-of-new-alerts"></a>Fördelarna med nya aviseringar

Klassiska aviseringar ersätts med ny, enhetlig aviseringar i Azure Monitor. Den nya varningsplattformen har följande fördelar:

- Du kan meddela på olika flerdimensionella mått för [många fler Azure-tjänster](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Den nya måtten aviseringar support [flera resource Varningsregler](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) som avsevärt minska arbetet med att hantera många regler.
- Den mekanism för enhetlig meddelande som har stöd för:
  - [Åtgärdsgrupper](action-groups.md), ett modulära sätt som fungerar med alla nya aviseringstyper (mått, logg och aktivitetsloggen).
  - Nytt meddelande mekanismer som SMS, röst och ITSM-anslutningsprogram.
- Den [unified aviseringar](alerts-overview.md) tar alla aviseringar på olika signaler (mått, logg och aktivitetsloggen) till ett och samma ställe.

## <a name="before-you-migrate"></a>Innan du migrerar

Migreringsprocessen konverterar klassiska Varningsregler till nya, motsvarande Varningsregler och skapar åtgärdsgrupper. Som förberedelse måste du vara medveten om följande punkter:

- Både nyttolast meddelandeformat och API: er för att skapa och hantera nya Varningsregler skiljer sig från de klassiska Varningsregler eftersom de stöder fler funktioner. [Lär dig hur du förbereder för migreringen](alerts-prepare-migration.md).

- Vissa klassiska Varningsregler kan inte migreras med hjälp av verktyget. [Lär dig vilka regler kan inte migreras och vad du gör med dem.](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Migreringen påverkar inte utvärderingen av klassiska notifieringsregler. De fortsätter att köra och skicka aviseringar tills de är migrerade och nya Varningsregler träder i kraft.

## <a name="how-to-use-the-migration-tool"></a>Så här använder du Migreringsverktyget

Följ dessa steg om du vill starta migreringen av dina klassiska aviseringsregler i Azure portal:

1. I [Azure-portalen](https://portal.azure.com)väljer **övervakaren**.

1. Välj **aviseringar**, och välj sedan **hantera aviseringsregler** eller **Visa klassiska aviseringar**.

1. Välj **migrera till nya regler** att gå till startsidan för migrering. Den här sidan visar en lista över alla dina prenumerationer och deras Migreringsstatus:

    ![migrering startsida](media/alerts-migration/migration-landing.png "migrera regler")

    Alla prenumerationer som kan migreras med hjälp av verktyget är märkta **redo att migrera**.

    > [!NOTE]
    > Migreringsverktyget är lanseras i faser till alla prenumerationer som använder klassiska Varningsregler. Du kan se några prenumerationer som markerats som inte klar för migrering i de tidiga faserna av distributionen.

1. Välj en eller flera prenumerationer och välj sedan **Förhandsgranska migrering**.

    Den resulterande visar information om klassiska Varningsregler som ska migreras för en prenumeration i taget. Du kan också välja **hämta information om migrering för den här prenumerationen** att hämta information i ett CSV-format.

    ![Förhandsgranskning av videomigrering](media/alerts-migration/migration-preview.png "Förhandsgranska migrering")

1. Ange en eller flera e-postadresser för att aviseras om Migreringsstatus för. Du får e-postmeddelande när migreringen är klar eller om någon åtgärd krävs från dig.

1. Välj **starta migrering**. Läs informationen som visas i den bekräftande dialogrutan och bekräfta att du är redo att börja migreringsprocessen.

    > [!IMPORTANT]
    > När du har initierat migrering för en prenumeration kan du inte redigera eller skapa klassiska Varningsregler för den aktuella prenumerationen. Den här begränsningen säkerställer att inga ändringar i din klassiska Varningsregler går förlorade vid migrering till de nya reglerna. Även om du inte kan ändra din klassiska Varningsregler, kommer de fortfarande fortsätta att köra och för att ge aviseringar tills de har migrerats. När migreringen är klar för din prenumeration kan använda du inte klassiska Varningsregler längre.

    ![Bekräfta migreringen](media/alerts-migration/migration-confirm.png "bekräfta starta migrering")

1. När migreringen är klar, eller om åtgärd krävs från dig, får du ett e-postmeddelande på de adresser som du angav tidigare. Du kan också regelbundet kontrollera status på landningssidan för migrering i portalen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Varför är min prenumeration visas inte redo för migrering?

Migreringsverktyget inför för kunder i faser. I de tidiga faserna, de flesta eller alla dina prenumerationer kan markeras som **inte klar för migrering**. Mid april dock bör alla prenumerationer vara redo att migrera.

När en prenumeration blir klar för migrering, får Prenumerationens ägare ett e-postmeddelande om att verktyget är tillgängligt. Håll utkik för det här meddelandet.

### <a name="who-can-trigger-the-migration"></a>Vem som kan utlösa migreringen?

Användare som har rollen deltagare för övervakning som tilldelats på prenumerationsnivå kan utlösa migreringen. [Mer information om rollbaserad åtkomstkontroll för migreringsprocessen](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Hur lång tid tar migreringen?

Migreringen har slutförts för de flesta prenumerationer i mindre än en timme. Du kan hålla reda på migreringens förlopp på landningssidan för migrering. Under migreringen kan vara säker på att dina aviseringar fortfarande körs antingen i klassiska aviseringar systemet eller i den nya.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Vad kan jag göra om jag kör ett problem uppstod under migreringen?

Se den [felsökningsguide](alerts-understand-migration.md#common-problems-and-remedies) för hjälp med problem som kan uppstår under migreringen. Om någon åtgärd krävs från dig för att slutföra migreringen, kommer du att meddelas i e-postadresser du angav när du konfigurerar verktyget.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migrering](alerts-prepare-migration.md)
- [Förstå hur Migreringsverktyget fungerar](alerts-understand-migration.md)
