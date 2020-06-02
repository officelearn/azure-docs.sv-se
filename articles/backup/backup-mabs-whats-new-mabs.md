---
title: Nyheter i Microsoft Azure Backup Server
description: Microsoft Azure Backup Server ger dig förbättrade säkerhets kopierings funktioner för att skydda virtuella datorer, filer och mappar, arbets belastningar med mera. Lär dig hur du installerar eller uppgraderar till Azure Backup Server v3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 358de6f2986036621accde90550e2afcc47f9135
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247554"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Nyheter i Microsoft Azure Backup Server

Microsoft Azure Backup Server version 3 (MABS v3) är den senaste uppgraderingen och innehåller viktiga fel korrigeringar, Windows Server 2019-support, SQL 2017-support och andra funktioner och förbättringar. Om du vill visa en lista över program fel som har åtgärd ATS och Installationsinstruktioner för MABS v3, se KB-artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Följande funktioner ingår i MABS v3:

## <a name="volume-to-volume-migration"></a>Volym-till-volym-migrering

Med Modern Backup Storage (MB) i MABS v2 presenterade vi arbets belastnings medveten lagring, där du konfigurerar vissa arbets belastningar som ska säkerhets kopie ras till specifika lagrings enheter, baserat på lagrings egenskaper. Efter konfigurationen kan du dock behöva flytta säkerhets kopior av vissa data källor till annan lagrings plats för optimerad resursutnyttjande. MABS v3 ger dig möjlighet att migrera dina säkerhets kopior och konfigurera dem så att de lagras på en annan volym i [tre steg](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Förhindra oväntad data förlust

I företag hanteras MABS av ett team av administratörer. Även om det finns rikt linjer för lagring som ska användas för säkerhets kopieringar kan en felaktig volym ges till MABS som säkerhets kopierings lagring leda till förlust av kritiska data. Med MABS v3 kan du förhindra sådana scenarier genom att konfigurera volymerna som de som inte är tillgängliga för lagring med hjälp av [dessa PowerShell-cmdletar](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Allokering av anpassad storlek

Modern Backup Storage (MB) förbrukar lagringen tunt, som och vid behov. För att göra det, beräknar MABS storleken på de data som säkerhets kopie ras när den har kon figurer ATS för skydd. Men om många filer och mappar säkerhets kopie ras tillsammans, som i fallet med en fil server, kan storleks beräkningen ta lång tid. Med MABS v3 kan du konfigurera MABS för att godkänna volymens storlek som standard, i stället för att beräkna storleken på varje fil, vilket sparar tid.

## <a name="optimized-cc-for-rct-vms"></a>Optimerad CC för virtuella RCT-datorer

MABS använder RCT (den interna ändrings spårningen i Hyper-V), vilket minskar behovet av konsekvens kontroller i scenarier som VM kraschar. RCT ger bättre flexibilitet än den ändringsspårning som tillhandahålls av säkerhetskopiering med VSS-ögonblicksbild. MABS v3 optimerar användningen av nätverks-och lagrings utrymme ytterligare genom att endast överföra ändrade data under en konsekvens kontroll.

## <a name="support-to-tls-12"></a>Stöd till TLS 1,2

TLS 1,2 är det säkra sättet för kommunikation som föreslås av Microsoft med förstklassig kryptering. MABS stöder nu TLS 1,2-kommunikation mellan MABS och skyddade servrar, för certifikatbaserad autentisering och för moln säkerhets kopieringar.

## <a name="vmware-vm-protection-support"></a>Stöd för VMware VM-skydd

VM-säkerhetskopiering för virtuella VMware-datorer stöds nu för produktions distributioner. MABS v3 erbjuder följande för VMware VM-skydd:

- Stöd för vCenter och ESXi 6,5, tillsammans med stöd för 5,5 och 6,0.
- Automatiskt skydd av virtuella VMware-datorer till molnet. Om nya virtuella VMware-datorer läggs till i en skyddad mapp, skyddas de automatiskt till disk och moln.
- Förbättringar av återställnings effektivitet för alternativ plats återställning i VMware.

## <a name="sql-2017-support"></a>Stöd för SQL 2017

MABS v3 kan installeras med SQL 2017 som MABS-databas. Du kan uppgradera SQL-servern från SQL 2016 till SQL 2017 eller installera den på ett nytt. Du kan också säkerhetskopiera SQL 2017-arbetsbelastningar både i klustrade och icke-klustrade miljöer med MABS v3.

## <a name="windows-server-2019-support"></a>Stöd för Windows Server 2019

MABS v3 kan installeras på Windows Server 2019. Om du vill använda MABS v3 med WS2019 kan du antingen uppgradera operativ systemet till WS2019 innan du installerar/uppgraderar till MABS v3, eller så kan du uppgradera operativ systemet efter installation/uppgradering av v3 på WS2016.

MABS v3 är en fullständig version och kan installeras direkt på Windows Server 2016, Windows Server 2019 eller kan uppgraderas från MABS v2. Innan du uppgraderar till eller installerar backup server v3 bör du läsa om installations kraven.
Mer information om installations-och uppgraderings stegen för MABS finns [här](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS har samma kodbas som System Center-Data Protection Manager. MABS v3 motsvarar Data Protection Manager 1807.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du förbereder servern eller börjar skydda en arbets belastning:

- [Kända problem i MABS v3](backup-mabs-release-notes-v3.md)
- [Förbered arbets belastningar för säkerhets kopierings Server](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-Server med hjälp av backup server](backup-azure-backup-server-vmware.md)
- [Använd backup server för att säkerhetskopiera SQL Server](backup-azure-sql-mabs.md)
- [Använd Modern Backup Storage med säkerhets kopierings Server](backup-mabs-add-storage.md)
