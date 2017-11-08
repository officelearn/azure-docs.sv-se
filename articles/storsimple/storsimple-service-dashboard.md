---
title: "Instrumentpanel för StorSimple Manager-tjänsten | Microsoft Docs"
description: "Beskriver infopanelen StorSimple Manager-tjänsten och förklarar hur du använder den för att övervaka hälsotillståndet för din StorSimple-lösning."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 62273c0093876f136d97eedf5a509f0b306a1379
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-dashboard"></a>Använd instrumentpanelen för StorSimple Manager-tjänsten
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [instrumentpanel för StorSimple Manager-tjänsten](storsimple-8000-service-dashboard.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Instrumentpanelssidan för StorSimple Manager-tjänsten innehåller en sammanfattning av alla enheter som är anslutna till StorSimple Manager-tjänsten, markera dem som behöver åtgärdas av en systemadministratör. Den här kursen introducerar instrumentpanelens sida, förklarar instrumentpanelinnehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från den här sidan.

![Instrumentpanel](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

Instrumentpanel för StorSimple Manager-tjänsten visar följande information:

* I den **diagram** område, kan du visa diagrammet relevanta mått för dina enheter. Du kan visa den primära lagringsplatsen (lokalt Fäst och nivåer) används över alla enheter, samt molnlagring som används av enheter under en viss tidsperiod. Använd kontrollerna i övre högra hörnet i diagrammet för att ange 1 vecka, 1-månads, 3-månaders eller 1 års skalan.
* Den **användningsöversikten** visar den primära lagring som etablerats och används av alla enheter i förhållande till det totala lagringsutrymmet som är tillgängliga på alla enheter. **Etablerad** avser mängden lagringsutrymme som är förberedd och tilldelat för användning, medan **används** refererar till användning av volymer som visas av initierare som är anslutna till enheterna.
* Den **aviseringar** området innehåller en ögonblicksbild av alla aktiva aviseringar över alla enheter, grupperat efter allvarlighetsgrad. Om du klickar på allvarlighetsgraden öppnas den **aviseringar** sida som gäller för att visa de aviseringarna. På den **aviseringar** kan du klicka på en enskild varning för att visa ytterligare information om den här aviseringen, inklusive alla rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.
* Den **jobb** området innehåller en ögonblicksbild över senaste jobb på alla enheter som är anslutna till din tjänst. Det finns länkar som du kan använda för att titta på jobb som används för närvarande pågår, de som misslyckades under de senaste 24 timmarna, eller som är schemalagda att köras i nästkommande 24 timmar.
* Den **snabböversikten** området innehåller användbar information, till exempel Tjänststatus antalet enheter som är ansluten till tjänsten, plats och information om den prenumeration som är associerad med tjänsten. Det finns också en länk till operations-loggen. Klicka på länken om du vill se en lista över alla slutförda StorSimple Manager-tjänståtgärder.

Du kan använda instrumentpanelen tjänstsidan StorSimple Manager för att initiera följande uppgifter:

* Visa eller återskapa nyckeln för tjänstregistrering.
* Ändra krypteringsnyckeln för tjänstdata.
* Visa åtgärdsloggar.

## <a name="view-or-regenerate-the-service-registration-key"></a>Visa eller återskapa nyckeln för tjänstregistrering
Nyckeln för tjänstregistrering används för att registrera en Microsoft Azure StorSimple-enhet med StorSimple Manager-tjänsten så att enheten visas i den klassiska Azure-portalen för ytterligare hanteringsåtgärder för. Nyckeln skapas på den första enheten och delas med resten av dina enheter.

Klicka på **registreringsnyckel** (längst ned på sidan) öppnas den **nyckel för tjänstregistrering** i dialogrutan där du kan kopiera den aktuella nyckeln för tjänstregistrering till Urklipp eller återskapa nyckeln för tjänstregistrering.

Återskapa nyckeln påverkas inte tidigare registrerade enheter: den påverkar bara de enheter som registreras med tjänsten när nyckeln genereras.

Mer information om visning och generera nyckel för tjänstregistrering går du till [hämta nyckel för tjänstregistrering](storsimple-manage-service.md#get-the-service-registration-key).

## <a name="change-the-service-data-encryption-key"></a>Ändra krypteringsnyckeln för tjänstdata
Tjänsten datakrypteringsnycklar används för att kryptera konfidentiell kundinformation, till exempel lagringskontouppgifter som skickas från din StorSimple Manager-tjänsten till StorSimple-enhet. Du behöver ändra dessa nycklar regelbundet om organisationen har en key rotation princip på lagringsenheter. Process för viktiga förändringar kan variera beroende på om det finns en enstaka enhet eller flera enheter som hanteras av StorSimple Manager-tjänsten.

Krypteringsnyckel för tjänstdata är en process i steg 3:

1. Använder den klassiska Azure-portalen, auktorisera en enhet för att ändra krypteringsnyckeln för tjänstdata.
2. Använder Windows PowerShell för StorSimple, starta tjänsten data encryption key ändras.
3. Om du har mer än en StorSimple-enhet kan du uppdatera krypteringsnyckeln för tjänstdata på andra enheter.

Följande steg beskriver processen förnyelse för krypteringsnyckeln för tjänstdata.

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>Visa operations-loggar
Du kan visa loggarna igen genom att klicka på åtgärden loggar länk som är tillgängliga i den **snabböversikten** rutan i instrumentpanelen. Detta tar du till sidan management-tjänster, där du kan filtrera och finns i loggar som är specifika för din StorSimple Manager-tjänsten.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [felsöka en StorSimple-enhet](storsimple-troubleshoot-operational-device.md).
* Mer information om hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

