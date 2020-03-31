---
title: Hårdvara för StorSimple 10 GbE-gränssnitt | Microsoft-dokument
description: I artikeln beskrivs sfp-sändtagare, kablar och switchar som stöds för de 10 GbE-nätverksgränssnitten på StorSimple-enheten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965018"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Maskinvara som stöds för 10 GbE-nätverksgränssnitten på StorSimple-enheten

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller information om extra maskinvara som fungerar med din Microsoft Azure StorSimple-enhet.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista över enheter som testats av Microsoft
Microsoft har testat följande SFP-sändtagare, kablar och växlar för att säkerställa att de fungerar optimalt med enheter. (Följande tabeller uppdateras när ny maskinvara testas.)

### <a name="sfp-transceivers"></a>SFP+ SFP-sändtagare
| Tillverkning | Modell |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kablar
| S. Nej. | Tillverkning | Modell |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Switchar
| S. Nej. | Tillverkning | Modell |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista över enheter som testats i fältet
Det här avsnittet innehåller en lista över enheter som har distribuerats i fältet av StorSimple-kunder. Dessa har inte testats av Microsoft men kommer sannolikt att fungera med din StorSimple-enhet.

| Parameter | Värde |
| --- | --- |
| Växla märke |Juniper |
| Switch-modell |ex4550-32F |
| Växla operativsystemversion |JunOS 12.3R9.4 |
| Bladmodell |Portar ombord (BILD 0) |
| Transceiver gör |Juniper |
| Transceiver modell |Artikelnummer 740-021308 <br></br> Artikelnummer 740-030658 |
| Transceiver firmware-version |Rev 01 Version 0.0 (rapporterat) |
| Kabelmodell |Duplex bygel LC/LC 50/125μ, OM3, LSZH |
| StorSimple-modell |8600 |
| StorSimple mjukvaruversion |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista över enheter som testats av OEM-leverantör (Mellanox)
Mellanox har testat följande SFP-sändtagare, kablar och switchar (Small Form-Factor plugable) för att säkerställa att de fungerar optimalt med Mellanox-nätverksgränssnitt som 10 GbE-nätverksgränssnitten på din StorSimple-enhet.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kablar och moduler som stöds av Mellanox
I följande tabell visas kablar och moduler som stöds av Mellanox. Dessa har inte testats av Microsoft men kommer sannolikt att fungera med din StorSimple-enhet.

| S. Nej. | Hastighet | Modell | Beskrivning | Tillverkning |
| --- | --- | --- | --- | --- |
| 1. |10 Gbe (svenska) |CAB-SFP-SFP-1M |passiv kopparkabel SFP+ 10 Gb/s 1m |Arista |
| 2. |10 Gbe (svenska) |CAB-SFP-SFP-2M |passiv kopparkabel SFP+ 10 Gb/s 2m |Arista |
| 3. |10 Gbe (svenska) |CAB-SFP-SFP-3M |passiv kopparkabel SFP+ 10 Gb/s 3m |Arista |
| 4. |10 Gbe (svenska) |CAB-SFP-SFP-5M |passiv kopparkabel SFP+ 10 Gb/s 5m |Arista |
| 5. |10 Gbe (svenska) |Cisco SFP-H10GBCU1M |Cisco SFP+-kabel |Cisco |
| 6. |10 Gbe (svenska) |Cisco SFP-H10GBCU3M |Cisco SFP+-kabel |Cisco |
| 7. |10 Gbe (svenska) |Cisco SFP-H10GBCU5M |Cisco SFP+-kabel |Cisco |
| 8. |10 Gbe (svenska) |J9281B HP X242 10G |SFP+ till SFP+ 1m Direktansatt kopparkabel |Hp |
| 9. |10 Gbe (svenska) |455883-B21 HP BLc |10 Gb SR SFP+ Opt |Hp |
| 10. |10 Gbe (svenska) |455886-B21 HP BLc |10 Gb LR SFP+-opt |Hp |
| 11. |10 Gbe (svenska) |487649-B21 HP BLc |SFP+ 0,5 m 10 GbE kopparkabel |Hp |
| 12. |10 Gbe (svenska) |487652-B21 HP BLc |SFP+ 1m 10GbE kopparkabel |Hp |
| 13. |10 Gbe (svenska) |487655-B21 HP BLc |SFP+ 3m 10GbE kopparkabel |Hp |
| 14. |10 Gbe (svenska) |487658-B21 HP BLc |SFP+ 7m 10GbE kopparkabel |Hp |
| 15. |10 Gbe (svenska) |537963-B21 HP BLc |SFP+ 5m 10GbE kopparkabel |Hp |
| 16. |10 Gbe (svenska) |AP784A HP |3m C-serien passiv koppar SFP + kabel |Hp |
| 17. |10 Gbe (svenska) |AP785A HP |5m C-serien passiv koppar SFP + kabel |Hp |
| 18. |10 Gbe (svenska) |AP818A HP |1m B-serien Active Copper SFP + Kabel |Hp |
| 19. |10 Gbe (svenska) |AP819A HP |3m B-serien Active Copper SFP + Kabel |Hp |
| 20. |10 Gbe (svenska) |J9150A HP |X132 10G SFP+ LC SR-sr-sändtagare |Hp |
| 21. |10 Gbe (svenska) |J9151A HP |X132 10G SFP+ LC LR-sändtagare |Hp |
| 22. |10 Gbe (svenska) |J9283B HP |X242 10G SFP+ SFP+ 3m DAC-kabel |Hp |
| 23. |10 Gbe (svenska) |J9285B HP |X242 10G SFP+ SFP+ 7m DAC-kabel |Hp |
| 24. |10 Gbe (svenska) |JD095B HP |X240 10G SFP+ SFP+ 0,65 m DAC-kabel |Hp |
| 25. |10 Gbe (svenska) |JD096B HP |X240 10G SFP+ SFP+ 1,2 m DAC-kabel |Hp |
| 26. |10 Gbe (svenska) |JD097B HP |X240 10G SFP + SFP + 3m DAD Kabel |Hp |
| 27. |10 Gbe (svenska) |MAM1Q00A-QSA Mellanox |QSFP till SFP+-adapter |Mellanox-teknik |
| 28. |10 Gbe (svenska) |MC2309124-006 Mt |Passiv kopparkabel 1x SFP+ Till QSFP 10Gb/s 24awg 7m |Mellanox-teknik |
| 29. |10 Gbe (svenska) |MC2309124-007 Mt |Passiv kopparkabel 1x SFP+ Till QSFP 10Gb/s 24awg 7m |Mellanox-teknik |
| 30. |10 Gbe (svenska) |MC2309130-003 Mt |Passiv kopparkabel 1x SFP + till QSFP 10Gb / s 30awg 3m |Mellanox-teknik |
| 31. |10 Gbe (svenska) |MC2309130-00A Mt |Passiv kopparkabel 1x SFP + till QSFP 10Gb / s 30awg 0,5 m |Mellanox-teknik |
| 32. |10 Gbe (svenska) |MC3309124-005 Mt |Passiv kopparkabel 1x SFP+ 10Gb/s 24awg 5m |Mellanox-teknik |
| 33. |10 Gbe (svenska) |MC3309124-007 Mt |Passiv kopparkabel 1x SFP+ 10Gb/s 24awg 7m |Mellanox-teknik |
| 34. |10 Gbe (svenska) |MC3309130-003 Mt |Passiv kopparkabel 1x SFP+ 10Gb/s 30awg 3m |Mellanox-teknik |
| 35. |10 Gbe (svenska) |MC3309130-00A Mt |Passiv kopparkabel 1x SFP+ 10Gb/s 30awg 0,5 m |Mellanox-teknik |

### <a name="switches-supported-by-mellanox"></a>Växlar som stöds av Mellanox
I följande tabell visas de växlar som stöds av Mellanox. Dessa har inte testats av Microsoft men kommer sannolikt att fungera med din StorSimple-enhet.

| S. Nej. | Hastighet | Modell | Beskrivning | Tillverkning |
| --- | --- | --- | --- | --- |
| 1. |10gbe |516733-B21 |HP ProCurve 6120XG 10 Gb Ethernet-bladbrytare |Hp |
| 2. |10gbe |538113-B21 |HP 10Gbe genomströmningsmodul (PTM) |Hp |
| 3. |10gbe |EN4093 |IBM PureFlex System Tyg EN4093 10 Gigabit Skalbar Switch Modul |IBM |
| 4. |1Gbe (1 GN)1 G. |3020 |Cisco Catalyst 3020 1GbE switch blad |Cisco |
| 5. |1Gbe (1 GN)1 G. |3020X (3020X) |Cisco Catalyst 3020X 1GbE switch blad |Cisco |
| 6. |1Gbe (1 GN)1 G. |438030-B21 |HP 1GbE switch modul - GbE2c Layer 2/3 Ethernet Blade Switch |Hp |
| 7. |1Gbe (1 GN)1 G. |6120G (på andra sätt) |HP ProCurve 6120G/XG 1GbE-kopplingsblad |Hp |

## <a name="next-steps"></a>Nästa steg
[Läs mer om StorSimples maskinvarukomponenter och status](storsimple-monitor-hardware-status.md).

