---
title: StorSimple regiontillgänglighet | Microsoft Docs
description: Beskriver vilka Azure-regioner där de olika modellerna för StorSimple-enheten är tillgängliga.
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
ms.openlocfilehash: ff2ea89c257a4941c801f3056450e005a9b37039
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996058"
---
# <a name="available-regions-for-your-storsimple"></a>Tillgängliga regioner för StorSimple

## <a name="overview"></a>Översikt

Azure-Datacenter fungera i flera geografiska områden runtom i världen att uppfylla kundens krav på prestanda, krav och önskemål gällande dataplacering. En Azure geografiskt område är en definierad del av världen som innehåller minst en Azure-Region. En Azure-region är ett område inom ett geografiskt område som innehåller ett eller flera datacenter.

Det är mycket viktigt att välja en Azure-region och val av region påverkas av faktorer som dataplacering och landsbaserad placering, tjänstens tillgänglighet, prestanda, kostnad och redundans. Mer information om hur du väljer en region går du till [som Azure-region passar bäst för mig?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

För StorSimple-lösningen bestäms specifikt val av region av följande faktorer:

- Regioner med StorSimple Device Manager-tjänsten.
- De länder där den fysiska StorSimple, i molnet eller virtuell enhet är tillgänglig.
- De regioner där de lagringskonton som lagrar StorSimple data ska vara belägen för optimala prestanda.

Den här självstudien beskrivs de regional tillgängligheten för StorSimple Device Manager-tjänsten, den lokala fysiskt och cloud-enheter. Informationen i den här artikeln gäller för StorSimple 8000 och enheter i 1200-serien.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Regiontillgänglighet för StorSimple Device Manager-tjänsten

StorSimple Device Manager-tjänsten stöds för närvarande i 12 offentliga regioner och 2 Azure Government-regioner.

Du definierar en region eller plats när du skapar StorSimple Device Manager-tjänsten. I allmänhet väljs en plats som är närmast den geografiska region där enheten distribueras. Men enheten och tjänsten kan också distribueras på olika platser.

Här är en lista över regioner där StorSimple Device Manager-tjänsten är tillgänglig för offentliga Azure-molnet och kan distribueras.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

StorSimple Device Manager-tjänsten är tillgänglig i datacenter i Iowa (USA-förvaltad region) och Virginia (USA-förvaltad region) för Azure Government-molnet.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Regiontillgänglighet för data som lagras i StorSimple

StorSimple data lagras fysiskt i Azure storage-konton och dessa konton är tillgängliga i alla Azure-regioner. När du skapar ett Azure storage-konto kan den primära platsen för lagringskontot väljs och som anger regionen där data finns.

När du först skapa en StorSimple Device Manager-tjänst och associera ett lagringskonto med kan din StorSimple Device Manager-tjänsten och Azure-lagring vara på två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat.

I allmänhet väljer du den närmaste regionen till din tjänst för ditt lagringskonto. Men kanske den närmaste Microsoft Azure-regionen faktiskt inte regionen med kortast svarstid. Det är den svarstid som bestämmer nätverksprestanda för tjänsten och prestanda för lösningen. Om du väljer ett lagringskonto i en annan region, är det därför viktigt att veta vad befinner sig mellan din tjänst och den region som är associerade med ditt lagringskonto.

Om du använder en StorSimple Cloud Appliance sedan rekommenderar vi att tjänsten och det associerade lagringskontot finns i samma region. Storage-konton i en annan region kan resultera i sämre prestanda.

## <a name="availability-of-storsimple-device"></a>Tillgängligheten för StorSimple-enhet

Beroende på vilken modell, kan StorSimple-enheter finnas i olika geografiska områden eller länder.

### <a name="storsimple-physical-device-models-81008600"></a>Fysiska StorSimple-enheten (modeller 8100/8600)

Om du använder en StorSimple 8100 eller 8600 fysisk enhet, är enheten tillgänglig i följande länder.

| #  | Land/region        | #  | Land/region     | #  | Land/region      | #  | Land/region              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Australien      | 16 | Hongkong SAR| 31 | Nya Zeeland  | 46 | Sydafrika         |
| 2  | Österrike        | 17 | Ungern     | 32 | Nigeria      | 47 | Sydkorea          |
| 3  | Bahrain        | 18 | Island     | 33 | Norge       | 48 | Spanien                |
| 4  | Belgien        | 19 | Indien       | 34 | Peru         | 49 | Sri Lanka            |
| 5  | Brasilien         | 20 | Indonesien   | 35 | Filippinerna  | 50 | Sverige               |
| 6  | Kanada         | 21 | Irland     | 36 | Polen       | 51 | Schweiz          |
| 7  | Chile          | 22 | Israel      | 37 | Portugal     | 52 | Taiwan (Taiwan)               |
| 8  | Colombia       | 23 | Italien       | 38 | Puerto Rico  | 53 | Thailand             |
| 9  | Tjeckien | 24 | Japan       | 39 | Qatar        | 54 | Turkiet               |
| 10 | Danmark        | 25 | Kenya       | 40 | Rumänien      | 55 | Ukraina              |
| 11 | Egypten          | 26 | Kuwait      | 41 | Ryssland       | 56 | Förenade Arabemiraten |
| 12 | Finland        | 27 | Macao SAR   | 42 | Saudiarabien | 57 | Storbritannien       |
| 13 | Frankrike         | 28 | Malaysia    | 43 | Singapore    | 58 | USA        |
| 14 | Tyskland        | 29 | Mexiko      | 44 | Slovakien     | 59 | Vietnam              |
| 15 | Grekland         | 30 | Nederländerna | 45 | Slovenien     | 60 | Kroatien              |

Den här listan ändras när flera länder/regioner har lagts till. För en uppdaterad lista över geografiska områden, går du till Storage matris villkoren för tillägg i den [villkoren för](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Microsoft kan leverera fysisk maskinvara och ge maskinvara reservdelar ersättning för StorSimple för geografiska områden i listan ovan.

> [!IMPORTANT]
> Placera inte en fysisk StorSimple-enhet i en region där StorSimple inte stöds. Microsoft kommer inte att kunna leverera reservdelar alla länder där StorSimple inte stöds.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modeller 8010/8020)

Om du använder en StorSimple Cloud Appliance 8010 eller 8020, sedan är enheten tillgängliga och stöds i alla regioner där den underliggande virtuella datorn stöds. 8010 använder en _Standard_A3_ VM som stöds i alla Azure-regioner.

8020 använder premium storage och _Standard_DS3_ virtuell dator för att skapa en molninstallation. 8020 stöds i Azure-regioner som har stöd för Premium Storage och _Standard_DS3_ virtuella Azure-datorer. Använd [listan](https://azure.microsoft.com/regions/services/) för att se om både **virtuella datorer > DS-serien** och **lagring > disklagring** är tillgängligt i din region.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (modell 1200)

Om du använder en 1200-serien StorSimple Virtual Array, stöds den virtuella diskavbildningen i alla Azure-regioner.

## <a name="next-steps"></a>Nästa steg

* Läs mer om den [priser för olika StorSimple modeller](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Läs mer om [hantera ditt lagringskonto i StorSimple](storsimple-8000-manage-storage-accounts.md).
* Mer information om hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
