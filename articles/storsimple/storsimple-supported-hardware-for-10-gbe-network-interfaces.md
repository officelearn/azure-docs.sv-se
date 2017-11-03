---
title: "Maskinvara för StorSimple 10 GbE-gränssnitt | Microsoft Docs"
description: "Beskriver stöds liten formfaktor pluggable (SFP) mottagarna, kablar och växlar för 10 GbE-nätverkskort på StorSimple-enheten."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Maskinvara som stöds för 10 GbE-nätverkskort på din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här artikeln innehåller information om kompletterande maskinvara som fungerar med din Microsoft Azure StorSimple-enhet.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista över enheter som har testats av Microsoft
Microsoft har testat följande liten formfaktor pluggable (SFP) mottagarna, kablar och växlar för att säkerställa att de fungerar optimalt med enheter. (Följande tabeller kommer att uppdateras när ny maskinvara har testats.)

### <a name="sfp-transceivers"></a>SFP + mottagarna
| Kontrollera | Modellen |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kablar
| S. Nej. | Kontrollera | Modellen |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820 - 05M (OM3) |

### <a name="switches"></a>Växlar
| S. Nej. | Kontrollera | Modellen |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-MI |

## <a name="list-of-devices-tested-in-the-field"></a>Lista över enheter som ska testas i fältet
Det här avsnittet innehåller en lista med enheter som har distribuerats i fältet av StorSimple-kunder. Dessa inte har testats av Microsoft, men kan komma att arbeta med din StorSimple-enhet.

| Parameter | Värde |
| --- | --- |
| Växla se |Juniper |
| Växeln-modellen |ex4550 32F |
| Växeln operativsystemets version |JunOS 12.3R9.4 |
| Bladet modellen |Publicera portar (PIC 0) |
| Sändtagare se |Juniper |
| Sändtagare modellen |Artikelnumret 740 021308 <br></br> Artikelnumret 740 030658 |
| Sändtagare version på inbyggd programvara |Rev. 01 Version 0,0 (rapporterat) |
| Kabel modellen |Duplex-omkopplare LC/LC 50/125µ, OM3, LSZH |
| StorSimple-modellen |8600 |
| StorSimple programvaruversion |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista över enheter som har testats av OEM-provider (Mellanox)
Mellanox har testat följande liten formfaktor pluggable (SFP) mottagarna, kablar och växlar för att säkerställa att de fungerar optimalt med Mellanox nätverksgränssnitt, till exempel 10 GbE-nätverksgränssnitt på StorSimple-enheten.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kablar och moduler som stöds av Mellanox
I följande tabell visas kablar och moduler som stöds av Mellanox. Dessa inte har testats av Microsoft, men kan komma att arbeta med din StorSimple-enhet.

| S. Nej. | Hastighet | Modellen | Beskrivning | Kontrollera |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP - 1M |passiva kopparkabel SFP + 10 Gbit/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP - 2M |passiva kopparkabel SFP + 10 Gbit/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP - 3M |passiva kopparkabel SFP + 10 Gbit/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP - 5M |passiva kopparkabel SFP + 10 Gbit/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10 G |SFP + till SFP + 1 miljon direktansluten kopparkabel |HP |
| 9. |10 GbE |455883 B21 HP BLc |10Gb SR SFP + välja |HP |
| 10. |10 GbE |455886 B21 HP BLc |10Gb LR SFP + välja |HP |
| 11. |10 GbE |487649 B21 HP BLc |SFP + 0,5 m 10GbE koppar kabel |HP |
| 12. |10 GbE |487652 B21 HP BLc |1 miljon 10GbE SFP + koppar kabel |HP |
| 13. |10 GbE |487655 B21 HP BLc |SFP + 3m 10GbE koppar kabel |HP |
| 14. |10 GbE |487658 B21 HP BLc |SFP + 7m 10GbE koppar kabel |HP |
| 15. |10 GbE |537963 B21 HP BLc |SFP + 5m 10GbE koppar kabel |HP |
| 16. |10 GbE |AP784A HP |3m C-serien passiva koppar SFP + kabel |HP |
| 17. |10 GbE |AP785A HP |5m C-serien passiva koppar SFP + kabel |HP |
| 18. |10 GbE |AP818A HP |1 miljon B-serien Active koppar SFP + kabel |HP |
| 19. |10 GbE |AP819A HP |3m B-serien Active koppar SFP + kabel |HP |
| 20. |10 GbE |J9150A HP |X132 10 G SFP + LC SR sändtagare |HP |
| 21. |10 GbE |J9151A HP |X132 10 G SFP + LC LR sändtagare |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3 m DAC-kabel |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 m DAC-kabel |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0.65 m DAC-kabel |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + 1.2 m DAC-kabel |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3 m DAD-kabel |HP |
| 27. |10 GbE |MAM1Q00A QSA Mellanox |QSFP till SFP + kort |Mellanox tekniker |
| 28. |10 GbE |MC2309124-006 Huvudmålservern |Passiva kopparkabel 1 x SFP+ till QSFP 10 Gbit/s 24awg 7 m |Mellanox tekniker |
| 29. |10 GbE |MC2309124 007 Huvudmålservern |Passiva kopparkabel 1 x SFP+ till QSFP 10 Gbit/s 24awg 7 m |Mellanox tekniker |
| 30. |10 GbE |MC2309130 003 Huvudmålservern |Passiva kopparkabel 1 x SFP+ till QSFP 10 Gbit/s 30awg 3 m |Mellanox tekniker |
| 31. |10 GbE |MC2309130 00A Huvudmålservern |Passiva kopparkabel 1 x SFP+ till QSFP 10 Gbit/s 30awg 0,5 m |Mellanox tekniker |
| 32. |10 GbE |MC3309124 005 Huvudmålservern |Passiva koppar kabel 1 x SFP+ 10 Gbit/s 24awg 5 m |Mellanox tekniker |
| 33. |10 GbE |MC3309124 007 Huvudmålservern |Passiva koppar kabel 1 x SFP+ 10 Gbit/s 24awg 7 m |Mellanox tekniker |
| 34. |10 GbE |MC3309130 003 Huvudmålservern |Passiva koppar kabel 1 x SFP+ 10 Gbit/s 30awg 3 m |Mellanox tekniker |
| 35. |10 GbE |MC3309130 00A Huvudmålservern |Passiva koppar kabel 1 x SFP+ 10 Gbit/s 30awg 0,5 m |Mellanox tekniker |

### <a name="switches-supported-by-mellanox"></a>Växlar som stöds av Mellanox
I följande tabell visas de växlar som stöds av Mellanox. Dessa inte har testats av Microsoft, men kan komma att arbeta med din StorSimple-enhet.

| S. Nej. | Hastighet | Modellen | Beskrivning | Kontrollera |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733 B21 |HP ProCurve 6120XG 10GbE Ethernet-bladswitch |HP |
| 2. |10GbE |538113 B21 |HP 10GbE direkt modul (PTM) |HP |
| 3. |10GbE |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit skalbara Switch-modul |IBM |
| 4. |1GbE |3020 |Cisco Catalyst 3020 1GbE växel bladet |Cisco |
| 5. |1GbE |3020 X |Cisco Catalyst 3020 X 1GbE växel bladet |Cisco |
| 6. |1GbE |438030 B21 |HP 1GbE switch-modul - GbE2c Layer 2/3 Ethernet-bladswitch |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G/XG 1GbE växel bladet |HP |

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om StorSimple maskinvarukomponenter och status](storsimple-monitor-hardware-status.md).

