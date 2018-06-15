---
title: StorSimple regional tillgänglighet | Microsoft Docs
description: Beskriver vilka Azure-regioner där olika StorSimple enhetsmodeller är tillgängliga.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
ms.locfileid: "24879066"
---
# <a name="available-regions-for-your-storsimple"></a>Tillgängliga regioner för din StorSimple

## <a name="overview"></a>Översikt

Azure-Datacenter köras i flera områden runtom i världen för att möta kundens behov av prestanda, krav och inställningar för plats. Ett Azure geografi är ett område som definierats av världen som innehåller minst en Azure-Region. En Azure-region är en del i en geografisk plats, som innehåller en eller flera datacenter.

Att välja en Azure-region är mycket viktigt och val av region påverkas av faktorer, till exempel data land och suveränitet, tjänstetillgänglighet, prestanda, kostnad och redundans. Mer information om hur du väljer en region, gå till [som Azure-region är rätt för mig?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

För StorSimple-lösning beror val av region specifikt på följande faktorer:

- Regioner där StorSimple Device Manager-tjänsten är tillgänglig.
- De länder där fysiska StorSimple, molnet eller virtuell enhet är tillgänglig.
- De regioner där de lagringskonton som lagrar data i StorSimple ska hittas för optimal prestanda.

Den här självstudiekursen beskriver region tillgängligheten för tjänsten StorSimple Enhetshanteraren, den lokala fysiskt och moln-enheter. Informationen i den här artikeln gäller StorSimple 8000 och 1200 serien enheter.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Regional tillgänglighet för StorSimple Device Manager-tjänsten

StorSimple enheten Manager-tjänsten stöds för närvarande i 12 offentliga och 2 offentliga Azure-regioner.

Du definierar en region eller plats när du skapar StorSimple enheten Manager-tjänsten. I allmänhet väljs en plats som är närmast den geografiska region där enheten har distribuerats. Men enheten och tjänsten kan också distribueras på olika platser.

Här är en lista över regioner där StorSimple enheten Manager-tjänsten är tillgänglig för offentliga Azure-molnet och kan distribueras.

![storsimple-enhet-manager-tjänsten-regioner](./media/storsimple-region/storsimple-device-manager-service-regions.png)

StorSimple Enhetshanteraren-tjänsten är tillgänglig i oss Gov Iowa och oss Gov Virginia Datacenter för Azure Government-molnet.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Regional tillgänglighet för data som lagras i StorSimple

StorSimple data lagras fysiskt i Azure storage-konton och dessa konton är tillgängliga i alla regioner som Azure. När du skapar ett Azure storage-konto kan den primära platsen för lagringskontot är valt och som fastställer den region där data finns.

När du först skapar en StorSimple Device Manager-tjänst och associera ett lagringskonto med kan din StorSimple-enheten Manager-tjänsten och Azure-lagring vara på två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat.

I allmänhet välja den närmaste regionen till din tjänst för ditt lagringskonto. Men kanske den närmaste Microsoft Azure-regionen faktiskt inte region med den lägsta fördröjningen. Det är fördröjningen som bestämmer nätverksprestanda för tjänsten och därför prestanda för lösningen. Om du väljer ett lagringskonto i en annan region, är det därför viktigt att veta vad befinner sig mellan din tjänst och den region som är kopplad till ditt lagringskonto.

Om du använder en StorSimple-enhet för molnet, sedan rekommenderar vi att tjänsten och det associerade lagringskontot är i samma region. Storage-konton i en annan region kan resultera i sämre prestanda.

## <a name="availability-of-storsimple-device"></a>Tillgängligheten för StorSimple-enhet

Beroende på vilken modell kan StorSimple-enheter finnas i olika geografiska områden eller länder.

### <a name="storsimple-physical-device-models-81008600"></a>Fysiska StorSimple-enheten (modeller 8100/8600)

Om du använder en StorSimple-8100 eller 8600 fysisk enhet är enheten tillgänglig i följande länder.

| #  | Land/region        | #  | Land/region     | #  | Land/region      | #  | Land/region              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Australien      | 16 | Hongkong SAR   | 31 | Nya Zeeland  | 46 | Sydafrika         |
| 2  | Österrike        | 17 | Ungern     | 32 | Nigeria      | 47 | Sydkorea          |
| 3  | Bahrain        | 18 | Island     | 33 | Norge       | 48 | Spanien                |
| 4  | Belgien        | 19 | Indien       | 34 | Peru         | 49 | Sri Lanka            |
| 5  | Brasilien         | 20 | Indonesien   | 35 | Filippinerna  | 50 | Sverige               |
| 6  | Kanada         | 21 | Irland     | 36 | Polen       | 51 | Schweiz          |
| 7  | Chile          | 22 | Israel      | 37 | Portugal     | 52 | Taiwan               |
| 8  | Colombia       | 23 | Italien       | 38 | Puerto Rico  | 53 | Thailand             |
| 9  | Tjeckien | 24 | Japan       | 39 | Qatar        | 54 | Turkiet               |
| 10 | Danmark        | 25 | Kenya       | 40 | Rumänien      | 55 | Ukraina              |
| 11 | Egypten          | 26 | Kuwait      | 41 | Ryssland       | 56 | Förenade Arabemiraten |
| 12 | Finland        | 27 | Macao       | 42 | Saudiarabien | 57 | Storbritannien       |
| 13 | Frankrike         | 28 | Malaysia    | 43 | Singapore    | 58 | USA        |
| 14 | Tyskland        | 29 | Mexiko      | 44 | Slovakien     | 59 | Vietnam              |
| 15 | Grekland         | 30 | Nederländerna | 45 | Slovenien     | 60 | Kroatien              |

Den här listan ändras när flera länder har lagts till. För en lista över de geografiska områden, gå till Storage matris villkoren tillägg i den [produkten villkoren](https://www.microsoft.com/en-us/Licensing/product-licensing).

Microsoft kan leverera fysisk maskinvara och ge ersättning av maskinvara delar för StorSimple för geografiska områden i listan ovan.

> [!IMPORTANT]
> Placera inte fysiska StorSimple-enheten i en region där StorSimple inte stöds. Microsoft kommer inte att kunna leverera alla ersättningsdelar till länder där StorSimple inte stöds.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Moln för StorSimple-enhet (modellerna 8010/8020)

Om du använder en StorSimple moln installation 8010 och 8020 sedan fungerar enheten och tillgänglig i alla regioner där den underliggande virtuella datorn stöds. 8010 använder ett _Standard_A3_ VM som stöds i alla Azure-regioner.

8020 använder premium-lagring och _Standard_DS3_ VM för att skapa en moln-installation. 8020 stöds i Azure-regioner som har stöd för Premium-lagring och _Standard_DS3_ virtuella Azure-datorer. Använd [listan](https://azure.microsoft.com/regions/services/) för att se om både **virtuella datorer > DS-serien** och **lagring > disklagring** är tillgängligt i din region.

### <a name="storsimple-virtual-array-model-1200"></a>Virtuella StorSimple-matris (Model 1200)

Om du använder en serie med 1200 virtuella StorSimple-matris, stöds virtuell disk image i alla Azure-regioner.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [priser för olika StorSimple modeller](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Lär dig mer om [hantera ditt lagringskonto i StorSimple](storsimple-8000-manage-storage-accounts.md).
* Mer information om hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
