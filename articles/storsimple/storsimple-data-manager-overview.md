---
title: Översikt över Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: En översikt över StorSimple Data Manager-tjänsten
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: vidarmsft
ms.openlocfilehash: d57229ad79909aa0334cc623d727b733a1ec73f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652016"
---
# <a name="storsimple-data-manager-solution-overview"></a>Lösning: översikt för StorSimple Data Manager

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple använder lagringsutrymmet i molnet som ett tillägg för lösningen lokalt och nivåer data på lokal lagring och molnet. Data lagras i molnet i formatet deduplicerade och komprimerade för maximal effektivitet och sänka kostnaderna. Eftersom data lagras i StorSimple-format, är det inte lätt kan användas av andra molnprogram som du vill använda.

Data StorSimple Manager kan du sömlöst kan komma åt och använda StorSimple formatera data i molnet. Det gör du genom att omvandla StorSimple format till interna blobbar och filer som du kan använda med andra tjänster som Azure Media Services, Azure HDInsights och Azure Machine Learning.

Den här artikeln innehåller en översikt över StorSimple Data Manager-lösning. Här beskrivs också hur du kan använda den här tjänsten för att skriva program som använder StorSimple data och andra Azure-tjänster i molnet.

## <a name="how-it-works"></a>Så här fungerar det?

StorSimple Data Manager-tjänsten identifierar StorSimple data i molnet från en StorSimple 8000-serien lokala enhet. StorSimple-data i molnet är deduplicerade, komprimerade StorSimple-format. Data Manager-tjänsten innehåller API: er för att extrahera StorSimple formatera data och transformera dem till andra format, t.ex. Azure-blobbar och Azure-filer. Detta omvandlas data används direkt av Azure HDInsight och Azure Media services. Data transformering kan därför dessa tjänster ska åtgärdas StorSimple omvandlade data från StorSimple 8000-serien lokala enhet. Detta flöde illustreras i följande diagram.

![Översiktsdiagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Användningsområden för data Manager

Du kan använda Data Manager med Azure Functions, Azure Automation och Azure Data Factory har arbetsflöden som körs på dina data eftersom det kommer i StorSimple. Du kanske vill bearbeta innehåll att lagra på StorSimple med Azure Media Services eller kör en Machine Learning-algoritm på dessa data eller skapar ett Hadoop-kluster för att analysera data som lagras på StorSimple media. Du kan använda mängder av tjänster som är tillgängliga på Azure som kombineras med data på StorSimple, för att låsa upp kraften i dina data.


## <a name="region-availability"></a>Regional tillgänglighet

Datahanteraren StorSimple finns i följande 7 områden:

 - Sydostasien
 - Östra USA
 - Västra USA
 - Västra USA 2
 - Västra centrala USA
 - Norra Europa
 - Västra Europa

StorSimple Data Manager kan dock användas för att omvandla data i följande regioner. 

![Regioner som är tillgängliga för data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Den här uppsättningen är större eftersom resursen distributionen i någon av de ovanstående regionerna kan ta fram omvandling av processen i den nedan regioner. Så som dina data finns i någon av regionerna som 19 kommer transformera du dina data med hjälp av den här tjänsten.


## <a name="choosing-a-region"></a>Att välja en region

Vi rekommenderar att:
 - Ditt lagringskonto för källa (ett som är associerade med din StorSimple-enhet) och mål-lagringskontot (där du vill att data i native-format) i samma Azure-region.
 - Du kan ta upp dina Data Manager och jobbet definition i den region som innehåller StorSimple-lagringskonto. Om det inte är möjligt, sätta upp Data Manager i närmaste Azure-region och sedan skapa jobbdefinitionen i samma region som din StorSimple-lagringskonto. 

    Om din StorSimple-lagringskonto inte är i 26 regioner som har stöd för skapande av jobb, rekommenderar vi att du inte kör StorSimple Data Manager som du ser långa fördröjningar och potentiellt hög utgående kostnader.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Datahanteraren StorSimple måste krypteringsnyckeln för tjänstdata att omvandla från StorSimple format till native-format. Krypteringsnyckel för tjänstdata skapas när den första enheten registreras med StorSimple-tjänsten. Mer information om den här nyckeln går du till [StorSimple-säkerhet](storsimple-8000-security.md).

Den krypteringsnyckel för tjänstdata angavs som indata lagras i ett nyckelvalv som skapas när du skapar en Data-hanterare. Valvet finns i samma Azure-region som din StorSimple Data Manager. Den här nyckeln tas bort när du tar bort Data Manager-tjänsten.

Den här nyckeln används av beräkningsresurserna för att utföra omvandlingen. Dessa beräkningar resurser som finns i samma Azure-region som din jobbdefinitionen. Den här regionen kan eller kan inte vara samma som den region där du kan ta upp Data Manager.

Om din Data Manager region skiljer sig från jobbet definition region, är det viktigt att du förstår vilka data/metadata finns i var och en av dessa regioner. Följande diagram illustrerar effekten av att olika regioner Data Manager och jobbet definition.

![Tjänsten och jobbet definition i olika regioner](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Hantering av personlig information

Datahanteraren StorSimple inte samla in och visar inte någon personlig information. Mer information finns i Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md).
