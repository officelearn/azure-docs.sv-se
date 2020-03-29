---
title: Tillgänglighet för StorSimple-regionen
description: Förklarar Azure-regioner där de olika StorSimple-enhetsmodellerna är tillgängliga.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275113"
---
# <a name="available-regions-for-your-storsimple"></a>Tillgängliga regioner för storsimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Azure-datacenter fungerar i flera geografiska områden runt om i världen för att möta kundens krav på prestanda, krav och inställningar för dataplats. En Azure-geografi är ett definierat område i världen som innehåller minst en Azure-region. En Azure-region är ett område inom en geografi som innehåller ett eller flera datacenter.

Att välja en Azure-region är mycket viktigt och valet av region påverkas av faktorer som datahemvist och suveränitet, tjänsttillgänglighet, prestanda, kostnad och redundans. Mer information om hur du väljer en region finns i [Vilken Azure-region som är rätt för mig?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

För StorSimple-lösning bestäms valet av region specifikt av följande faktorer:

- Regioner där Tjänsten StorSimple Device Manager är tillgänglig.
- De länder/regioner där StorSimple fysiska, moln eller virtuella enheten är tillgänglig.
- De regioner där lagringskonton som lagrar StorSimple-data bör finnas för optimal prestanda.

Den här självstudien beskriver regionens tillgänglighet för Tjänsten StorSimple Device Manager, den lokala fysiska och molnenheterna. Informationen i den här artikeln gäller för StorSimple 8000- och 1200-serieenheter.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Regionens tillgänglighet för Tjänsten StorSimple Device Manager

StorSimple Device Manager-tjänsten stöds för närvarande i 12 offentliga regioner och 2 Azure Government-regioner.

Du definierar en region eller plats när du först skapar Tjänsten StorSimple Enhetshanteraren. I allmänhet väljs en plats närmast det geografiska område där enheten distribueras. Men enheten och tjänsten kan också distribueras på olika platser.

Här är en lista över regioner där StorSimple Device Manager-tjänsten är tillgänglig för offentligt Azure-moln och kan distribueras.

![storsimple-device-manager-service-regioner](./media/storsimple-region/storsimple-device-manager-service-regions.png)

För Azure Government-molnet är StorSimple Device Manager-tjänsten tillgänglig i US Gov Iowa- och US Gov Virginia-datacenter.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Regionens tillgänglighet för data som lagras i StorSimple

StorSimple-data lagras fysiskt i Azure-lagringskonton och dessa konton är tillgängliga i alla Azure-regioner. När du skapar ett Azure-lagringskonto väljs den primära platsen för lagringskontot och som avgör den region där data finns.

När du först skapar en StorSimple Device Manager-tjänst och associerar ett lagringskonto med den kan din StorSimple Device Manager-tjänst och Azure-lagring vara på två separata platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat.

I allmänhet väljer du närmaste region till din tjänst för ditt lagringskonto. Den närmaste Microsoft Azure-regionen kanske dock inte är den region som har den lägsta svarstiden. Det är svarstiden som dikterar nätverkstjänstens prestanda och därmed lösningens prestanda. Så om du väljer ett lagringskonto i en annan region är det viktigt att veta vad svaren är mellan din tjänst och den region som är kopplad till ditt lagringskonto.

Om du använder en StorSimple Cloud Appliance rekommenderar vi att tjänsten och det associerade lagringskontot finns i samma region. Lagringskonton i en annan region kan resultera i dåliga prestanda.

## <a name="availability-of-storsimple-device"></a>Tillgänglighet för StorSimple-enhet

Beroende på modell kan StorSimple-enheterna vara tillgängliga i olika geografiska områden eller länder/regioner.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fysisk enhet (Modeller 8100/8600)

Om du använder en fysisk StorSimple 8100- eller 8600-enhet är enheten tillgänglig i följande länder/regioner.

| #  | Land/region        | #  | Land/region     | #  | Land/region      | #  | Land/region             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australien             | 16 | Hongkong      | 31 | Nya Zeeland         | 46 | Sydafrika               |
| 2  | Österrike               | 17 | Ungern            | 32 | Nigeria             | 47 | Sydkorea                |
| 3  | Bahrain               | 18 | Island            | 33 | Norge              | 48 | Spanien                      |
| 4  | Belgien               | 19 | Indien              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brasilien                | 20 | Indonesien          | 35 | Filippinerna         | 50 | Sverige                     |
| 6  | Kanada                | 21 | Irland            | 36 | Polen              | 51 | Schweiz                |
| 7  | Chile                 | 22 | Israel             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Colombia              | 23 | Italien              | 38 | Puerto Rico         | 53 | Thailand                   |
| 9  | Tjeckien        | 24 | Japan              | 39 | Qatar               | 54 | Turkiet                     |
| 10 | Danmark               | 25 | Kenya              | 40 | Rumänien             | 55 | Ukraina                    |
| 11 | Egypten                 | 26 | Kuwait             | 41 | Ryssland              | 56 | Förenade Arabemiraten       |
| 12 | Finland               | 27 | Folkrepubliken Kinas särskilda administrativa region Macao          | 42 | Saudiarabien        | 57 | Storbritannien             |
| 13 | Frankrike                | 28 | Malaysia           | 43 | Singapore           | 58 | USA              |
| 14 | Tyskland               | 29 | Mexiko             | 44 | Slovakien            | 59 | Vietnam                    |
| 15 | Grekland                | 30 | Nederländerna        | 45 | Slovenien            | 60 | Kroatien                    |

Den här listan ändras när fler länder/regioner läggs till. En aktuell lista över geografiska områden finns i bilagan för lagringsmatristermer i [produkttermerna](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Microsoft kan leverera fysisk maskinvara och tillhandahålla ersättning av reservdelar till maskinvara för StorSimple till geografiska områden i föregående lista.

> [!IMPORTANT]
> Placera inte en fysisk StorSimple-enhet i en region där StorSimple inte stöds. Microsoft kommer inte att kunna leverera några reservdelar till länder/regioner där StorSimple inte stöds.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (Modeller 8010/8020)

Om du använder en StorSimple Cloud Appliance 8010 eller 8020 stöds enheten och är tillgänglig i alla regioner där den underliggande virtuella datorn stöds. 8010 använder en _Standard_A3_ VM som stöds i alla Azure-regioner.

8020 använder premiumlagring och _Standard_DS3_ virtuell dator för att skapa en molninstallation. 8020 stöds i Azure-regioner som stöder Premium Storage och _Standard_DS3_ virtuella Azure-datorer. Använd [listan](https://azure.microsoft.com/regions/services/) för att se om både **virtuella datorer > DS-serien** och **lagring > disklagring** är tillgängligt i din region.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (modell 1200)

Om du använder en StorSimple Virtual Array i 1 200-serien stöds den virtuella diskavbildningen i alla Azure-regioner.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [priser för olika StorSimple-modeller](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Läs mer om [hur du hanterar ditt StorSimple-lagringskonto](storsimple-8000-manage-storage-accounts.md).
* Läs mer om hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
