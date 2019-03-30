---
title: Migrera din klassiska aviseringar i Azure Monitor med hjälp av verktyget frivillig migrering
description: Lär dig hur du använder frivillig migrering för att migrera din klassiska Varningsregler.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632521"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Använd verktyget frivillig migrering för att migrera din klassiska Varningsregler

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är dras tillbaka i juli 2019. Migreringsverktyget att utlösa migrering frivilligt är tillgänglig i Azure portal och inför för kunder som använder klassiska Varningsregler. Den här artikeln vägleder dig om hur du använder Migreringsverktyget för att migrera din klassiska Varningsregler frivilligt innan automatisk migrering startar i juli 2019.

## <a name="benefits-of-new-alerts"></a>Fördelarna med nya aviseringar

Klassiska aviseringar ersätts med nya enhetliga aviseringar i Azure Monitor. Den nya varningsplattformen har följande fördelar:

- Avisera om olika flerdimensionella mått för [många fler Azure-tjänster](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Nya mått aviseringar support [flera resource Varningsregler](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) som avsevärt minska arbetet med att hantera många regler.
- Enhetligt sätt
  - [Åtgärdsgrupper](action-groups.md) är en modulbaserad meddelande mekanism som fungerar med alla nya aviseringstyper (mått, log och aktivitetsloggen)
  - Du kommer även att kunna dra nytta av nya notification mekanismer som SMS, röst och ITSM-anslutningsprogram
- Den [unified aviseringar](alerts-overview.md) ger alla aviseringar på olika signaler (mått, aktivitet log och loggfiler) i ett och samma ställe

## <a name="before-you-migrate"></a>Innan du migrerar

Som en del av migreringen, klassiska Varningsregler konverteras till motsvarande nya Varningsregler och åtgärdsgrupper skapas.

- Meddelandeformat för nyttolasten samt API: er för att skapa och hantera nya Varningsregler skiljer sig från de klassiska Varningsregler eftersom de stöder fler funktioner. Lär dig [hur du förbereder för migreringen](alerts-prepare-migration.md).

- Vissa klassiska Varningsregler kan inte migreras med hjälp av verktyget. [Lär dig vilka regler som inte är migreringsbart och se hur du migrerar dem](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Migreringen påverkar inte utvärderingen av klassiska notifieringsregler. De fortsätter att köra och skicka aviseringar tills de har migrerats och nya Varningsregler börjar utvärdera.


## <a name="how-to-use-the-migration-tool"></a>Så här använder du Migreringsverktyget

Följande procedur beskriver hur du utlöser migreringen av dina klassiska aviseringsregler i Azure-portalen:

1. I [Azure-portalen](https://portal.azure.com), klicka på **övervakaren**.

2. Klicka på **aviseringar** klicka sedan på **hantera aviseringsregler** eller **Visa klassiska aviseringar**.

3. Klicka på **migrera till nya regler** att gå till startsidan för migrering. Den här sidan visar en lista över alla dina prenumerationer och status för migreringen för dessa.

    ![migrering startsida](media/alerts-migration/migration-landing.png "migrera regler")

4. Alla prenumerationer som kan migreras med hjälp av verktyget kommer att markeras som **redo att migrera**.

    > [!NOTE]
    > Migreringsverktyget är lanseras i faser till alla prenumerationer som använder klassiska Varningsregler. Du kan se några prenumerationer som inte klar för migrering i de tidiga faserna av lansering.

5. Välj en eller flera prenumerationer och klicka på **Förhandsgranska migrering**

6. På den här sidan kan se du information om klassiska Varningsregler som ska migreras för en prenumeration i taget. Du kan också **hämta information om migrering för den här prenumerationen** i en CSV-format.

    ![Förhandsgranskning av videomigrering](media/alerts-migration/migration-preview.png "Förhandsgranska migrering")

7. Ange en eller flera **e-postadresser** för att aviseras om Migreringsstatus för. Vi skickar ett e-postmeddelande när migreringen är klar eller en åtgärd krävs från dig.

8. Klicka på **starta migrering**. Läs informationen som visas i bekräftelsedialogrutan och bekräfta om du är redo att starta migreringen.

    >[!IMPORTANT]
    > När du har initierat migreringsprocessen för en prenumeration kan du inte redigera/skapa klassiska Varningsregler för prenumerationen. Däremot fortsätter klassiska notifieringsregler som körs och genererar varningar tills de har migrerats över. Detta är att säkerställa exakthet mellan klassiska Varningsregler och de nya regler som skapats under migreringen. När migreringen är klar för din prenumeration kan använda du inte klassiska Varningsregler längre.

    ![Bekräfta migreringen](media/alerts-migration/migration-confirm.png "bekräfta starta migrering")

9. Som vi har slutfört migreringen eller behöver en åtgärd från dig, får du ett e-post på de e-postadresser som tillhandahålls i steg 8. Du kan också regelbundet kontrollera status från Landningssida för migrering i portalen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Varför är min prenumerationerna i listan som inte klar för migrering?**

Migreringsverktyget är lanseras i faser för alla kunder. I de tidiga faserna, de flesta eller alla dina prenumerationer kan markeras som **inte klar för migrering**. Mid april bör alla prenumerationer dock redo att migrera.

När en prenumeration blir klar för migrering, får Prenumerationsägare ett e-postmeddelande tillgängligheten för verktyget. Håll utkik för det här meddelandet.

### <a name="who-can-trigger-the-migration"></a>**Vem som kan utlösa migreringen?**

Användare som har rollen deltagare för övervakning som tilldelats på prenumerationsnivå kommer att kunna utlösa migreringen. Läs mer om [rollbaserad åtkomstkontroll för migreringsprocessen](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**Hur lång tid migreringen kommer att?**

För de flesta prenumerationer Slutför migrering vanligtvis mindre än en timme. Du kan hålla reda på migreringens förlopp från Landningssida för migrering.  Under denna tid. säkerställas att aviseringarna fortfarande körs antingen i klassiska aviseringar systemet eller den nya servern.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**Vad kan jag göra om jag får problem under migreringen?**

Följ den [felsökningsguide för att se steg om eventuella problem som kan uppstår under migreringen](alerts-understand-migration.md#common-issues-and-remediations). Om någon åtgärd krävs från dig för att slutföra migreringen, kommer du att meddelas på de e-postadresser som tillhandahålls under migreringen.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migrering](alerts-prepare-migration.md)
- [Förstå hur Migreringsverktyget fungerar](alerts-understand-migration.md)
