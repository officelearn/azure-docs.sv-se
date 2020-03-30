---
title: Översikt över Microsoft Azure StorSimple Data Manager | Microsoft-dokument
description: Ger en översikt över Tjänsten StorSimple Data Manager
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
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876109"
---
# <a name="storsimple-data-manager-solution-overview"></a>Lösningsöversikt för StorSimple Data Manager

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple använder molnlagring som ett tillägg till den lokala lösningen och automatiskt nivåer data över lokal lagring och molnet. Data lagras i molnet i ett avdped och komprimerat format för maximal effektivitet och för att sänka kostnaderna. Eftersom data lagras i StorSimple-format är det inte lätt att förbrukningsbara av andra molnprogram som du kanske vill använda.

Med StorSimple Data Manager kan du sömlöst komma åt och använda StorSimple-formatdata i molnet. Detta gör detta genom att omvandla StorSimple-format till inbyggda blobbar och filer, som du kan använda med andra tjänster som Azure Media Services, Azure HDInsights och Azure Machine Learning.

Den här artikeln innehåller en översikt över StorSimple Data Manager-lösningen. Det förklarar också hur du kan använda den här tjänsten för att skriva program som använder StorSimple-data och andra Azure-tjänster i molnet.

## <a name="how-it-works"></a>Hur fungerar det?

StorSimple Data Manager-tjänsten identifierar StorSimple-data i molnet från en lokal StorSimple 8000-serie. StorSimple-data i molnet är eftervakade, komprimerade StorSimple-format. Data manager-tjänsten tillhandahåller API:er för att extrahera StorSimple-formatdata och omvandla dem till andra format som Azure-blobbar och Azure-filer. Den här transformerade datan förbrukas sedan enkelt av Azure HDInsight- och Azure Media-tjänster. Dataomvandlingen gör det möjligt för dessa tjänster att fungera på de transformerade StorSimple-data från StorSimple 8000-seriens lokala enhet. Detta flöde illustreras i följande diagram.

![Diagram på hög nivå](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Användningsfall för DataHanteraren

Du kan använda Datahanteraren med Azure Functions, Azure Automation och Azure Data Factory för att ha arbetsflöden som körs på dina data när de kommer in i StorSimple. Du kanske vill bearbeta ditt medieinnehåll som du lagrar på StorSimple med Azure Media Services, eller köra en Machine Learning-algoritm på dessa data, eller visa ett Hadoop-kluster för att analysera data som du lagrar på StorSimple. Med det stora utbudet av tjänster som finns tillgängliga på Azure i kombination med data på StorSimple kan du låsa upp kraften i dina data.


## <a name="region-availability"></a>Regional tillgänglighet

StorSimple Data Manager är tillgänglig i följande 7 regioner:

 - Sydostasien
 - USA, östra
 - USA, västra
 - USA, västra 2
 - USA, västra centrala
 - Europa, norra
 - Europa, västra

StorSimple Data Manager kan dock användas för att omvandla data i följande regioner. 

![Regioner tillgängliga för data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Den här uppsättningen är större eftersom resursdistributionen i någon av ovanstående regioner kan ta upp omvandlingsprocessen i nedanstående regioner. Så länge dina data finns i någon av de 19 regionerna kan du omvandla dina data med den här tjänsten.


## <a name="choosing-a-region"></a>Välja en region

Vi rekommenderar att du:
 - Ditt källlagringskonto (det som är kopplat till din StorSimple-enhet) och mållagringskontot (där du vill att data i ursprungsformat) finns i samma Azure-region.
 - Du tar upp din Data Manager och jobbdefinition i regionen som innehåller StorSimple-lagringskontot. Om detta inte är möjligt kan du ta upp Datahanteraren i närmaste Azure-region och sedan skapa jobbdefinitionen i samma region som ditt StorSimple-lagringskonto. 

    Om ditt Lagringskonto i StorSimple inte finns i de 26 regioner som stöder skapandet av jobbdefinition rekommenderar vi att du inte kör StorSimple Data Manager eftersom du ser långa fördröjningar och potentiella utgående avgifter.
    
Microsoft strävar efter att se till att Azure-tjänster alltid är tillgängliga i alla regioner. Oplanerade avbrott i tjänsten kan dock förekomma under korta perioder i en viss region. I sådana fall kan du ta upp en Data Manager och jobbdefinition i en region som inte påverkas av avbrottet och köra omvandlingsjobbet. Du kan stöta på ytterligare svarstider i ett sådant scenario, men det kan vara din återställningsstrategi i den sällsynta händelsen av ett regionalt avbrott.

## <a name="security-considerations"></a>Säkerhetsöverväganden

StorSimple Data Manager behöver krypteringsnyckeln för tjänstdata för att omvandla från StorSimple-format till ursprungsformat. Krypteringsnyckeln för tjänstdata genereras när den första enheten registreras med StorSimple-tjänsten. Mer information om den här nyckeln finns i [StorSimple security](storsimple-8000-security.md).

Krypteringsnyckeln för tjänstdata som tillhandahålls som en indata lagras i ett nyckelvalv som skapas när du skapar en Data Manager. Valvet finns i samma Azure-region som din StorSimple Data Manager. Den här nyckeln tas bort när du tar bort tjänsten Data Manager.

Den här nyckeln används av beräkningsresurserna för att utföra omvandlingen. Dessa beräkningsresurser finns i samma Azure-region som jobbdefinitionen. Den här regionen kan, eller kanske inte är samma som den region där du tar upp din Data Manager.

Om datahanterarens region skiljer sig från projektdefinitionsregionen är det viktigt att du förstår vilka data/metadata som finns i var och en av dessa regioner. Följande diagram illustrerar effekten av att ha olika regioner för Datahanteraren och jobbdefinition.

![Service- och jobbdefinition i olika regioner](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Data Manager samlar inte in eller visar någon personlig information. Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Kända begränsningar

Tjänsten har för närvarande följande begränsningar:
- StorSimple Data Manager arbetar för närvarande inte med volymer som är bitlocker krypterade. Du kommer att se jobbfel om du försöker köra tjänsten med en krypterad enhet.
- Vissa metadata för filer (inklusive ACL: er) kommer inte att behållas i transformerade data.
- Den här tjänsten fungerar bara med NTFS-volymer.
- Filsökvägslängder måste vara mindre än 256 tecken annars misslyckas jobbet.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager-användargränssnittet för att omvandla dina data](storsimple-data-manager-ui.md).
