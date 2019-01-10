---
title: Översikt över Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Översikt över StorSimple Data Manager-tjänsten
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
ms.openlocfilehash: fe4d332859b3ec0ba03e64f9a830b7710586aa17
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158825"
---
# <a name="storsimple-data-manager-solution-overview"></a>Översikt över lösning för StorSimple Data Manager

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple använder lagringsutrymmet i molnet som ett tillägg av lokal lösning och nivåer data mellan lokal lagring och molnet. Data lagras i molnet i ett deduplicerade och komprimerade format för maximal effektivitet och sänka kostnaderna. Eftersom data lagras i StorSimple-format, är det inte enkelt att använda för andra molnprogram som du kanske vill använda.

StorSimple Data Manager kan du sömlöst kan komma åt och använda StorSimple formatera data i molnet. Det gör du genom att omvandla StorSimple-format till interna blobar och filer som du kan använda med andra tjänster som Azure Media Services, Azure HDInsights och Azure Machine Learning.

Den här artikeln innehåller en översikt över StorSimple Data Manager-lösning. Den förklarar hur du kan använda den här tjänsten för att skriva program som använder StorSimple data och andra Azure-tjänster i molnet.

## <a name="how-it-works"></a>Så här fungerar det?

StorSimple Data Manager-tjänsten identifierar StorSimple data i molnet från en StorSimple 8000-serien en lokal enhet. StorSimple-data i molnet är deduplicerade, komprimerade StorSimple-format. Data Manager-tjänsten tillhandahåller API: er för att extrahera StorSimple formatera data och omvandla det till andra format, t.ex. för Azure BLOB och Azure Files. Detta omvandlade data är sedan lätt som används av Azure HDInsight och Azure medietjänster. Transformering av data kan därför tjänsterna ska åtgärdas StorSimple omvandlade data från en lokal enhet i StorSimple 8000-serien. Det här flödet illustreras i följande diagram.

![Översiktsdiagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Användningsområden för data Manager

Du kan använda Data Manager med Azure Functions, Azure Automation och Azure Data Factory har arbetsflöden som körs på dina data när de kommer in StorSimple. Du kanske vill bearbeta ditt medieinnehåll att lagra på StorSimple med Azure Media Services, eller kör en Machine Learning-algoritm på dessa data eller ta fram ett Hadoop-kluster för att analysera data som du lagrar på StorSimple. Du kan använda många olika tjänster som är tillgängliga på Azure i kombination med data på StorSimple, för att låsa upp kraften i dina data.


## <a name="region-availability"></a>Regional tillgänglighet

StorSimple Data Manager är tillgängligt i följande 7 regioner:

 - Sydostasien
 - Östra USA
 - Västra USA
 - Västra USA 2
 - Västra centrala USA
 - Norra Europa
 - Västra Europa

StorSimple Data Manager kan dock användas för att omvandla data i följande regioner. 

![Regioner som är tillgängliga för data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Den här uppsättningen är större eftersom resursdistributionen i någon av ovanstående regioner kan föra in omvandling processen i den nedan regioner. Så länge dina data finns i någon av de 19 regionerna, så kan du omvandla dina data med den här tjänsten.


## <a name="choosing-a-region"></a>Välja en region

Vi rekommenderar att:
 - Din källagringskontot (ett som är associerade med din StorSimple-enhet) och mål-lagringskontot (där du vill att data i ursprungligt format) finnas i samma Azure-region.
 - Du sätta upp dina Data och definition i den region som innehåller StorSimple-lagringskonto. Om det inte är möjligt, sätta upp Data Manager i den närmaste Azure-regionen och sedan skapa jobbdefinitionen i samma region som ditt lagringskonto i StorSimple. 

    Om din StorSimple-lagringskonto inte är i 26 regioner som har stöd för skapande av jobb definition, rekommenderar vi att du inte kör StorSimple Data Manager som du ser långa fördröjningar och kostnader för utgående potentiellt hög trafik.

## <a name="security-considerations"></a>Säkerhetsöverväganden

StorSimple Data Manager måste tjänstdatakrypteringsnyckel att omvandla från StorSimple-format till ursprungligt format. Krypteringsnyckeln för tjänstdata skapas när den första enheten som registreras med tjänsten StorSimple. Mer information om den här nyckeln går du till [StorSimple-säkerhet](storsimple-8000-security.md).

Den krypteringsnyckeln för tjänstdata tillhandahålls som en inmatning lagras i ett nyckelvalv som skapas när du skapar en Data Manager. Valvet finns i samma Azure-region som din StorSimple Data Manager. Den här nyckeln tas bort när du tar bort dina Data Manager-tjänsten.

Den här nyckeln används av beräkningsresurserna för att utföra omvandlingen. Dessa beräkningsalternativ resurser som finns i samma Azure-region som din jobbdefinition. Den här regionen kan eller kan inte vara samma som den region där du kan ta upp Data Manager.

Om din Data Manager region skiljer sig från din region för definition av jobb, är det viktigt att du förstår vilka data/metadata finns i var och en av dessa regioner. Följande diagram illustrerar effekten av att ha olika regioner för Data och definition.

![Definition av tjänsten och jobbet i olika regioner](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Data Manager inte samla in och visa någon personlig information. Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Kända begränsningar

Tjänsten har för närvarande följande begränsningar:
- StorSimple Data Manager fungerar för närvarande inte med volymer som är bitlocker-krypterad. Misslyckade jobb visas om du försöker köra tjänsten med en krypterad enhet.
- Vissa metadata för filer (inklusive ACL: er) ska inte behållas i transformerade data.
- Den här tjänsten fungerar bara med NTFS-volymer.
- Filen sökväg längd måste vara mindre än 256 tecken annan jobbet misslyckas.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI och omvandla data](storsimple-data-manager-ui.md).
