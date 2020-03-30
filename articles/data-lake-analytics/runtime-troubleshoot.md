---
title: Felsöker Azure Data Lake Analytics U-SQL-körningsfel
description: Lär dig hur du felsöker U-SQL-körningsfel.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648459"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Lär dig hur du felsöker U-SQL-körningsfel på grund av körningsändringar

Azure Data Lake U-SQL-körningen, inklusive kompilatorn, optimeraren och jobbhanteraren, är det som bearbetar din U-SQL-kod.

## <a name="choosing-your-u-sql-runtime-version"></a>Välja din U-SQL-körningsversion

När du skickar U-SQL-jobb från antingen Visual Studio, ADL SDK eller Azure Data Lake Analytics-portalen, kommer jobbet att använda den tillgängliga standardkörningen. Nya versioner av U-SQL-körningen släpps regelbundet och innehåller både mindre uppdateringar och säkerhetskorrigeringar.

Du kan också välja en anpassad runtime-version. antingen för att du vill prova en ny uppdatering, måste stanna på en äldre version av en körning, eller har fått en snabbkorrigering för ett rapporterat problem där du inte kan vänta på den vanliga nya uppdateringen.

> [!CAUTION]
> Att välja en körning som skiljer sig från standard har potential att bryta dina U-SQL-jobb. Använd dessa andra versioner endast för testning.

I sällsynta fall kan Microsoft Support fästa en annan version av en körning som standard för ditt konto. Se till att du återställer stiftet så snart som möjligt. Om du förblir fäst vid den versionen upphör den att gälla vid ett senare tillfälle.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Övervaka dina jobb U-SQL runtime version

Du kan se historiken för vilken körningsversion dina tidigare jobb har använt i kontots jobbhistorik via Visual Studios jobbwebbläsare eller Azure-portalens jobbhistorik.

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Välj **Visa alla jobb**. En lista över alla aktiva och nyligen avslutade jobb i kontot visas.
3. Du kan också klicka på **Filter** för att hjälpa dig att hitta jobben efter **tidsintervall,** **jobbnamn**och **författarvärden.**
4. Du kan se körningen som används i slutförda jobb.

![Visa körningsversionen av ett tidigare jobb](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

De tillgängliga körningsversionerna ändras med tiden. Standardkörningstiden kallas alltid "standard" och vi håller åtminstone den tidigare körningen tillgänglig under en tid samt gör särskilda körningar tillgängliga av olika skäl. Uttryckligen namngivna runtimes följer i allmänhet följande format (kursiv stil används för variabla delar och [] anger valfria delar):

release_YYYYMMDD_adl_buildno[_modifier]

Till exempel innebär release_20190318_adl_3394512_2 den andra versionen av build 3394512 av runtime-utgåvan den 18 mars 2019 och release_20190318_adl_3394512_private innebär en privat version av samma utgåva. Obs: Datumet är relaterat till när den senaste incheckningen har tagits för den utgåvan och inte nödvändigtvis det officiella releasedatumet.

Följande är de körtidsversioner som för närvarande är tillgängliga.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 den aktuella standardinställningen
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Felsöka problem med U-SQL-körningsversion

Det finns två möjliga körningsversionsproblem som du kan stöta på:

1. Ett skript eller någon användarkod ändrar beteende från en version till nästa. Sådana brytande förändringar meddelas normalt i förväg med offentliggörandet av release notes. Om du stöter på en sådan brytningsändring kontaktar du Microsoft Support för att rapportera det här brytningsbeteendet (om det inte har dokumenterats ännu) och skicka in dina jobb mot den äldre körningsversionen.

2. Du har använt en körning som inte är standard antingen uttryckligen eller implicit när den har fästs på ditt konto och den körningen har tagits bort efter en tid. Om du stöter på saknade körningar uppgraderar du skripten så att de körs med den aktuella standardkörningstiden. Om du behöver ytterligare tid kontaktar du Microsoft Support

## <a name="see-also"></a>Se även

- [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Hantera Azure Data Lake Analytics med Azure-portal](data-lake-analytics-manage-use-portal.md)
- [Övervaka jobb i Azure Data Lake Analytics med Azure-portalen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
