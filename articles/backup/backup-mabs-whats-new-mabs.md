---
title: Vad är nytt i Microsoft Azure Backup Server
description: Microsoft Azure Backup Server får du förbättrade funktioner som säkerhetskopiering för att skydda virtuella datorer, filer och mappar, arbetsbelastningar och mycket mer. Lär dig mer om att installera eller uppgradera till Azure Backup Server V3.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 5718064994a80266c216ae6040746be29194adc9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108924"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Vad är nytt i Microsoft Azure Backup Server

Microsoft Azure Backup Server version 3 (V3 MABS) är den senaste uppgraderingen och innehåller viktiga felkorrigeringar, stöd för Windows Server 2019, SQL 2017 support och andra funktioner och förbättringar. Visa listan över buggar och installationsanvisningar för MABS V3, finns i KB-artikel [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

Följande funktioner ingår i MABS V3:

## <a name="volume-to-volume-migration"></a>Volymen volymmigrering
Med Modern Backup Storage (MBS) i MABS V2, presenterade vi arbetsbelastningsmedveten lagring, där du konfigurerar vissa arbetsbelastningar som ska säkerhetskopieras till specifika lagringsutrymmen baserat på Egenskaper. Men efter konfigurationen kan kanske du behöver flytta säkerhetskopior av vissa datakällor till ett andra lager för optimerad Resursanvändning. MABS V3 ger dig möjlighet att migrera dina säkerhetskopior och konfigurera dem för att lagras på en annan volym i [3 steg](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Förhindra förlust av oväntade data
I företag hanteras MABS av ett team av administratörer. Det finns riktlinjer i lagringsutrymme som ska användas för säkerhetskopiering, kan en felaktig volym som ges till MABS som lagringsutrymme för säkerhetskopior leda till förlust av viktiga data. Med MABS V3, kan du förhindra att sådana scenarier genom att konfigurera de volymerna som de som inte är tillgängliga för lagring med hjälp av [dessa PowerShell-cmdletar](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion).

## <a name="custom-size-allocation"></a>Storlek på anpassad allokering
Modern Backup Storage (MBS) förbrukar storage tunt, vid behov. Om du vill göra det beräknar MABS storleken på data som säkerhetskopieras när den är konfigurerad för skydd. Om många filer och mappar säkerhetskopieras tillsammans, vilket till exempel en filserver kan storlek ta lång tid. Med MABS V3, kan du konfigurera MABS för att godkänna volymens storlek som standard i stället för att beräkna storleken på varje fil kan därför spara tid.

## <a name="optimized-cc-for-rct-vms"></a>Optimerad kopia för RCT-datorer
MABS använder RCT (den interna ändringsspårningen i Hyper-V), vilket minskar behovet av tidskrävande konsekvens kontrollerar i scenarier som virtuell dator kraschar. RCT ger bättre flexibilitet än ändringsspårning som tillhandahålls av VSS-ögonblicksbild-baserad säkerhetskopiering. MABS V3 optimerar nätverks- och förbrukning ytterligare genom att överföra endast de ändrade data under en konsekvenskontroll.

## <a name="support-to-tls-12"></a>Stöd för att TLS 1.2
TLS 1.2 är säkert sätt av kommunikation som föreslås av Microsoft med bäst i klassen kryptering. MABS stöder nu TLS 1.2-kommunikation mellan MABS och skyddade servrar för certifikatbaserad autentisering och för säkerhetskopior i molnet.

## <a name="vmware-vm-protection-support"></a>Stöd för VMware VM-skydd
VMware VM backup har nu stöd för Produktionsdistribution. MABS V3 erbjuder följande för VMware VM-skydd:

-   Stöd för vCenter och ESXi 6.5, tillsammans med stöd för 5.5 och 6.0.
- Automatiskt skydd av virtuella VMware-datorer till molnet. Om nya virtuella VMware-datorer läggs till en skyddad mapp, skyddas automatiskt till disk och molnet.
- Förbättrad databasåterställning effektivitet för VMware alternativa platsåterställningen.

## <a name="sql-2017-support"></a>Stöd för SQL-2017
MABS V3 kan installeras med SQL 2017 som MABS-databas. Du kan uppgradera SQLServer från SQL 2016 till SQL 2017 eller installera den nyligen. Du kan också säkerhetskopiera SQL 2017 arbetsbelastning både i klustrade och icke-klustrad miljö med MABS V3.

## <a name="windows-server-2019-support"></a>Stöd för Windows Server 2019
MABS V3 kan installeras på Windows Server 2019. Om du vill använda MABS V3 med WS2019, kan du antingen uppgradera ditt operativsystem till WS2019 innan installation/uppgradering till MABS V3 eller du kan uppgradera ditt operativsystem efter installation/uppgradering av V3 på WS2016.

MABS V3 är en fullständig utgåva och kan installeras direkt på Windows Server 2016, Windows Server 2019 eller kan uppgraderas från MABS V2. Innan du uppgraderar till eller installera Backup Server V3, Läs mer om förutsättningarna för installation.
Hitta mer information om installationen eller uppgraderingen steg för MABS [här](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).


> [!NOTE]
> 
> MABS har samma kod som System Center Data Protection Manager. MABS v3 motsvarar 1807 för Data Protection Manager.

## <a name="next-steps"></a>Nästa steg

Lär dig att förbereda din server eller börjar skydda en arbetsbelastning:
- [Kända problem i MABS V3](backup-mabs-release-notes-v3.md)
- [Förbereda Backup Server-arbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
- [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
- [Använda Modern Backup Storage med Backup Server](backup-mabs-add-storage.md)
