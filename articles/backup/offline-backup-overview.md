---
title: Översikt över offline säkerhetskopiering
description: Läs mer om komponenterna för offline säkerhetskopiering. De inkluderar offline säkerhetskopiering baserat på Azure Data Box och offline backup baserat på Azure Import/Export-tjänsten.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196184"
---
# <a name="overview-of-offline-backup"></a>Översikt över offline säkerhetskopiering

Den här artikeln innehåller en översikt över säkerhetskopiering offline.

Inledande fullständiga säkerhetskopior till Azure överför vanligtvis stora mängder data online och kräver mer nätverksbandbredd jämfört med efterföljande säkerhetskopior som endast överför inkrementella ändringar. Fjärrkontor eller datacenter i vissa geografiska områden har inte alltid tillräcklig nätverksbandbredd. Av denna anledning tar dessa inledande säkerhetskopior flera dagar. Under den här tiden använder säkerhetskopiorna kontinuerligt samma nätverk som har etablerats för program som körs i det lokala datacentret.

Azure Backup stöder säkerhetskopiering offline, som överför initiala säkerhetskopieringsdata offline, utan användning av nätverksbandbredd. Det ger en mekanism för att kopiera säkerhetskopierade data till fysiska lagringsenheter. Enheterna levereras sedan till ett närliggande Azure-datacenter och överförs till ett Recovery Services-valv. Den här processen säkerställer robust överföring av säkerhetskopierade data utan att använda någon nätverksbandbredd.

## <a name="offline-backup-options"></a>Alternativ för säkerhetskopiering offline

Offline säkerhetskopiering erbjuds i två lägen baserat på ägandet av lagringsenheter:

- Offlinesäkerhetskopiering baserat på Azure Data Box (förhandsversion)
- Offline säkerhetskopiering baserad på Azure Import/Export-tjänsten

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offlinesäkerhetskopiering baserat på Azure Data Box (förhandsversion)

Det här läget stöds för närvarande med MICROSOFT Azure Recovery Services (MARS) Agent i förhandsversion. Det här alternativet utnyttjar [Azure Data Box](https://azure.microsoft.com/services/databox/) för att leverera Microsoft-proprietära, säkra och manipuleringssäkra överföringsenheter med USB-kontakter till ditt datacenter eller fjärrkontor. Säkerhetskopieringsdata skrivs direkt på dessa enheter. Det här alternativet sparar den ansträngning som krävs för att skaffa egna Azure-kompatibla diskar och kopplingar eller för att etablera tillfällig lagring som mellanlagringsplats. Microsoft hanterar också överföringslogistiken från till, som du kan spåra via Azure-portalen. 

En arkitektur som beskriver förflyttning av säkerhetskopierade data med det här alternativet visas här.

![Azure Backup Data Box-arkitektur](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Här är en sammanfattning av arkitekturen:

1. Azure Backup kopierar säkerhetskopieringsdata direkt till dessa förkonfigurerade enheter.
2. Du kan sedan leverera dessa enheter tillbaka till ett Azure-datacenter.
3. Azure Data Box kopierar data till ett kundägt lagringskonto.
4. Azure Backup kopierar automatiskt säkerhetskopierade data från lagringskontot till det angivna Recovery Services-valvet. Inkrementella online-säkerhetskopior är schemalagda.

Information om hur du använder offlinesäkerhetskopiering baserat på Azure Data Box finns i [Offline-säkerhetskopiering med Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline säkerhetskopiering baserad på Azure Import/Export-tjänsten

Det här alternativet stöds av Microsoft Azure Backup Server (MABS), DPM -DPM (System Center Data Protection Manager) och MARS-agenten. Den använder [azure import/export-tjänsten](https://docs.microsoft.com/azure/storage/common/storage-import-export-service). Du kan överföra initiala säkerhetskopieringsdata till Azure med hjälp av dina egna Azure-kompatibla diskar och kopplingar. Den här metoden kräver att du etablerar tillfällig lagring som kallas mellanlagringsplatsen och använder fördefinierade verktyg för att formatera och kopiera säkerhetskopierade data till kundägda diskar. 

En arkitektur som beskriver förflyttning av säkerhetskopierade data med det här alternativet visas här.

![Azure Import/Export-tjänstarkitektur för säkerhetskopiering](./media/offline-backup-overview/azure-backup-import-export.png)

Här är en sammanfattning av arkitekturen:

1. I stället för att skicka säkerhetskopierade data över nätverket skriver Azure Backup säkerhetskopieringsdata till en mellanlagringsplats.
2. Data på mellanlagringsplatsen skrivs till en eller flera SATA-diskar med hjälp av ett anpassat verktyg.
3. Som en del av det förberedande arbetet skapar verktyget ett Azure-importjobb. SATA-enheterna levereras till närmaste Azure-datacenter och refererar till importjobbet för att ansluta aktiviteterna.
4. På Azure-data kopieras data på diskarna till ett Azure-lagringskonto.
5. Azure Backup kopierar säkerhetskopieringsdata från lagringskontot till Recovery Services-valvet. Inkrementella säkerhetskopior är schemalagda.

Information om hur du använder offlinesäkerhetskopiering baserat på Azure Import/Export-tjänsten med MARS-agenten finns [i Arbetsflöde för säkerhetskopiering offline i Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Information om hur du använder samma sak tillsammans med MABS eller DPM-A finns i [Arbetsflödet för offlinesäkerhetskopior för DPM och Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Sammanfattning av stöd för offlinesäkerhetskopiering

I följande tabell jämförs de två tillgängliga alternativen så att du kan göra lämpliga val baserat på ditt scenario.

| **Övervägande**                                            | **Offlinesäkerhetskopiering baserat på Azure Data Box**                     | **Offline säkerhetskopiering baserad på Azure Import/Export-tjänsten**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Distributionsmodeller för Azure Backup                              | MARS Agent (förhandsgranskning)                                              | MARS-agent, MABS, DPM-A                                           |
| Maximalt säkerhetskopieringsdata per server (MARS) eller per skyddsgrupp (MABS, DPM-A) | [Azure Data Box-disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (upp till 10 diskar med 8 TB vardera)                          |
| Säkerhet (data, enhet och tjänst)                           | [Data](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256-bitars krypterad <br> [Enhet](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Robust fodral, proprietärt, autentiseringsuppgifter-baserat gränssnitt för att kopiera data <br> [Tjänst](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Skyddad av Azure-säkerhetsfunktioner | Data - BitLocker krypterat                                 |
| Etablerar tillfällig mellanlagringsplats                     | Krävs inte                                                | Mer än eller lika med den uppskattade datastorleken för säkerhetskopiering        |
| Regioner som stöds                                           | [Diskområden i Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box-regioner](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure Import/Export-tjänstområden](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Gränsöverskridande frakt                                     | Stöds inte  <br>    Källadress och mål Azure-datacenter måste finnas i samma land* | Stöds                                                    |
| Överför logistik (leverans, transport, pickup)           | Fullständigt hanterat Microsoft                                     | Hanterade kunder                                            |
| Prissättning                                                      | [Priser för Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Priser för Azure Data Box-disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Priser för Azure Import/Export-tjänst](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Om ditt land inte har ett Azure-datacenter måste du leverera dina diskar till ett Azure-datacenter i ett annat land.

## <a name="next-steps"></a>Nästa steg

* [Azure Backup offline säkerhetskopiering med hjälp av Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Arbetsflöde för offlinesäkerhetskopiering i Azure Backup](backup-azure-backup-import-export.md) 
* [Arbetsflöde för offlinesäkerhetskopior för DPM och Azure Backup Server](backup-azure-backup-server-import-export-.md)
