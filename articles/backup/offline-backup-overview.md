---
title: Översikt över säkerhets kopiering offline
description: Lär dig mer om de olika komponenterna i säkerhets kopiering offline, inklusive Azure Data Box baserad offline-säkerhetskopiering och Azure import/export-baserad säkerhets kopiering offline.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027022"
---
# <a name="overview-of-offline-backup"></a>Översikt över säkerhets kopiering offline

Den här artikeln ger en översikt över säkerhets kopiering offline.

Första fullständiga säkerhets kopieringar till Azure överför vanligt vis stora mängder data online och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför stegvisa ändringar. Fjärranslutna kontor eller data Center i vissa geografiska områden har inte alltid tillräckligt med nätverks bandbredd. Därför tar de här första säkerhets kopieringarna flera dagar och under den tiden använder hela tiden samma nätverk som etablerades för program som körs i det lokala data centret.

Azure Backup stöder säkerhets kopiering offline, vilket gör att du kan överföra inledande säkerhets kopierings data offline utan att använda nätverks bandbredd. Den innehåller en mekanism för att kopiera säkerhets kopierings data till fysiska lagrings enheter som sedan levereras till ett närliggande Azure-datacenter och laddas upp till ett Recovery Services-valv. Den här processen säkerställer robust överföring av säkerhets kopierings data utan att använda någon nätverks bandbredd.

## <a name="offline-backup-options"></a>Alternativ för säkerhets kopiering offline

Offline – säkerhets kopiering erbjuds i två lägen baserat på lagrings enheternas ägarskap.

1. Azure Data Box baserad offline-säkerhetskopiering (förhands granskning)
2. Azure import/export-baserad säkerhets kopiering offline

## <a name="azure-data-box-based-offline-backup-preview"></a>Azure Data Box baserad offline-säkerhetskopiering (förhands granskning)

Det här läget stöds för närvarande med MARS-agenten i för hands versionen. Det här alternativet drar nytta av [Azure Data boxs tjänsten](https://azure.microsoft.com/services/databox/) för att leverera Microsoft-patentskyddade, säkra och Manipulerings beständiga överförings anordningar med USB-anslutningar, till ditt data Center eller fjärranslutna kontor och säkerhets kopierings data skrivs direkt till dessa enheter. **Det här alternativet sparar den ansträngning som krävs för att skaffa dina egna Azure-kompatibla diskar och anslutningar eller tillhandahållande av tillfällig lagring som en mellanlagringsplats.** Dessutom hanterar Microsoft heltäckande överförings logistik, som du kan spåra genom Azure Portal. En arkitektur som beskriver flyttningen av säkerhets kopierings data med det här alternativet visas nedan.

![Azure Backup Data Box-enhet arkitektur](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Här är en sammanfattning av arkitekturen:

1. Azure Backup kopierar säkerhetskopierade data direkt till dessa förkonfigurerade enheter.
2. Sedan kan du skicka tillbaka enheterna till ett Azure-datacenter.
3. Tjänsten Azure Data Box kopierar data till ett kundägda lagrings konto.
4. Azure Backup kopierar automatiskt säkerhetskopierade data från lagrings kontot till det angivna Recovery Services valvet och stegvisa säkerhets kopieringar schemaläggs.

Om du vill använda Azure Data Box baserad säkerhets kopiering offline, se [den här artikeln](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Azure import/export-baserad säkerhets kopiering offline

Det här alternativet stöds av Azure Backup Server (MABS)/DPM-A/MARS-agenten. Den använder [tjänsten Azure import/export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) som gör det möjligt att överföra första säkerhets kopierings data till Azure med hjälp av dina egna Azure-kompatibla diskar och anslutningar. Den här metoden kräver etablering av en tillfällig lagrings plats som kallas **mellanlagringsplats** och användning av förbyggda verktyg för att formatera och kopiera säkerhetskopierade data till kundägda diskar. En arkitektur som beskriver förflyttningen av säkerhets kopierings data med det här alternativet visas nedan:

![Azure Backup import/export-arkitektur](./media/offline-backup-overview/azure-backup-import-export.png)

Här är en sammanfattning av arkitekturen:

1. I stället för att skicka säkerhetskopierade data via nätverket skriver Azure Backup säkerhets kopierings data till en mellanlagringsplats.
2. Data på mellanlagringsplatsen skrivs till en eller flera SATA-diskar med ett anpassat verktyg.
3. Som en del av det förberedande arbetet skapar verktyget ett Azure-importerat jobb. SATA-enheterna levereras till närmaste Azure-datacenter och refererar till import jobbet för att ansluta aktiviteterna.
4. På Azure-datacentret kopieras data på diskarna till ett Azure Storage-konto.
5. Azure Backup kopierar säkerhetskopierade data från lagrings kontot till Recovery Services-valvet och stegvisa säkerhets kopieringar schemaläggs.

Information om hur du använder Azure import/export-baserad säkerhets kopiering offline med MARS-agenten finns i [den här artikeln](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Om du vill använda samma tillsammans med MABS/DPM-A, se [den här artikeln](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Översikt över stöd för offline-säkerhetskopiering

I tabellen nedan jämförs de två tillgängliga alternativen så att du kan göra lämpliga val baserat på ditt scenario.

| **Övervägande**                                            | **Azure Data Box baserad offline-säkerhetskopiering**                     | **Azure import/export-baserad säkerhets kopiering offline**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup distributions modeller                              | MARS-agent (för hands version)                                              | MARS-agent, Azure Backup Server (MABS), DPM-A                                           |
| Högsta antal säkerhets kopierings data per server (MARS) eller per skydds grupp (MABS, DPM-A) | [Azure Data Box disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (upp till 10 diskar av 8 TB var)                          |
| Säkerhet (data, enhets & tjänst)                           | [Data](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256-bit krypterad <br> [Enhet](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) – robust hölje, patentskyddat Credential-baserat gränssnitt för att kopiera data <br> [Tjänst](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) – skyddas av säkerhetsfunktioner i Azure | Data-BitLocker-krypterad                                 |
| Tillfällig etablering av lagrings plats                     | Krävs inte                                                | Mer än eller lika med den beräknade säkerhets kopierings data storleken        |
| Regioner som stöds                                           | [Azure Data Box Disk regioner](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box regioner](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure import/export-regioner](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Leverans mellan länder *                                     | **Stöds inte**  <br>    *Käll adress & målets Azure-datacenter måste finnas i samma land* | Stöds                                                    |
| Överför logistik (leverans, transport, hämtning)           | Fullständigt Microsoft-hanterat                                     | Kund hantering                                            |
| Prissättning                                                      | [Azure Data Box priser](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box Disk priser](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prissättning för Azure import/export](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Om ditt land inte har ett Azure-datacenter, måste du leverera diskarna till ett Azure-datacenter i ett annat land.*

## <a name="next-steps"></a>Nästa steg

* [Azure Data Box baserad offline-säkerhetskopiering för MARS-agenten](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure import/export-baserad offline-säkerhetskopiering för MARS-agenten](backup-azure-backup-import-export.md)  
* [Azure import/export-baserad säkerhets kopiering offline för MABS/DPM – A](backup-azure-backup-server-import-export-.md)
