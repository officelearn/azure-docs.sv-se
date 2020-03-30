---
title: Nyheter i Microsoft Azure Backup Server
description: Microsoft Azure Backup Server ger dig förbättrade funktioner för säkerhetskopiering för att skydda virtuella datorer, filer och mappar, arbetsbelastningar med mera. Lär dig hur du installerar eller uppgraderar till Azure Backup Server V3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582814"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Nyheter i Microsoft Azure Backup Server

Microsoft Azure Backup Server version 3 (MABS V3) är den senaste uppgraderingen och innehåller kritiska buggfixar, Stöd för Windows Server 2019, STÖD för SQL 2017 och andra funktioner och förbättringar. Så här visar du listan över åtgärdade fel och installationsinstruktionerna för MABS V3, se KB artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Följande funktioner ingår i MABS V3:

## <a name="volume-to-volume-migration"></a>Volym till volymmigrering

Med Modern Backup Storage (MBS) i MABS V2 tillkännagav vi arbetsbelastningsmedveten lagring, där du konfigurerar vissa arbetsbelastningar som ska säkerhetskopieras till specifik lagring, baserat på lagringsegenskaper. Efter konfigurationen kan du dock hitta ett behov av att flytta säkerhetskopior av vissa datakällor till annan lagring för optimerad resursanvändning. MABS V3 ger dig möjlighet att migrera dina säkerhetskopior och konfigurera dem som ska lagras till en annan volym i [tre steg](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Förhindra oväntad dataförlust

I företag hanteras MABS av ett team av administratörer. Det finns riktlinjer för lagring som ska användas för säkerhetskopior, men en felaktig volym som ges till MABS eftersom lagring för säkerhetskopiering kan leda till förlust av kritiska data. Med MABS V3 kan du förhindra sådana scenarier genom att konfigurera dessa volymer som de som inte är tillgängliga för lagring med hjälp av [dessa PowerShell-cmdlets](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Anpassad storleksallokering

Modern Backup Storage (MBS) förbrukar lagring tunt, om och när det behövs. För att göra det beräknar MABS storleken på de data som säkerhetskopieras när de är konfigurerade för skydd. Men om många filer och mappar säkerhetskopieras tillsammans, som i fallet med en filserver, kan storleksberäkningen ta lång tid. Med MABS V3 kan du konfigurera MABS så att den accepterar volymstorleken som standard, i stället för att beräkna storleken på varje fil, vilket sparar tid.

## <a name="optimized-cc-for-rct-vms"></a>Optimerad CC för virtuella RCT-datorer

MABS använder RCT (den inbyggda ändringsspårningen i Hyper-V), vilket minskar behovet av tidskrävande konsekvenskontroller i scenarier som vm kraschar. RCT ger bättre flexibilitet än den ändringsspårning som tillhandahålls av säkerhetskopiering med VSS-ögonblicksbild. MABS V3 optimerar nätverks- och lagringsförbrukningen ytterligare genom att endast överföra ändrade data under eventuella konsekvenskontroller.

## <a name="support-to-tls-12"></a>Stöd till TLS 1.2

TLS 1.2 är det säkra kommunikationssätt som föreslås av Microsoft med kryptering i bäst klass. MABS stöder nu TLS 1.2-kommunikation mellan MABS och de skyddade servrarna, för certifikatbaserad autentisering och för säkerhetskopiering i molnet.

## <a name="vmware-vm-protection-support"></a>Stöd för VMware VM-skydd

VMware VM-säkerhetskopiering stöds nu för produktionsdistributioner. MABS V3 erbjuder följande för VMware VM-skydd:

- Stöd för vCenter och ESXi 6.5, tillsammans med stöd för 5.5 och 6.0.
- Automatiskt skydd av virtuella VMware-datorer till molnet. Om nya virtuella datorer med VMware läggs till i en skyddad mapp skyddas de automatiskt till disk och moln.
- Förbättringar av återställningseffektiviteten för VMware alternativ platsåterställning.

## <a name="sql-2017-support"></a>Support för SQL 2017

MABS V3 kan installeras med SQL 2017 som MABS-databas. Du kan uppgradera SQL-servern från SQL 2016 till SQL 2017 eller installera den nyligen. Du kan också säkerhetskopiera SQL 2017-arbetsbelastningen både i klustermiljö och icke-grupperad miljö med MABS V3.

## <a name="windows-server-2019-support"></a>Support för Windows Server 2019

MABS V3 kan installeras på Windows Server 2019. Om du vill använda MABS V3 med WS2019 kan du antingen uppgradera ditt operativsystem till WS2019 innan du installerar/uppgraderar till MABS V3 eller så kan du uppgradera ditt OS-inlägg med installation/uppgradering av V3 på WS2016.

MABS V3 är en fullständig version och kan installeras direkt på Windows Server 2016, Windows Server 2019 eller uppgraderas från MABS V2. Innan du uppgraderar till eller installerar Backup Server V3 kan du läsa om installationsförutsättningarna.
Mer information om installations-/uppgraderingsstegen för MABS [finns här](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS har samma kodbas som System Center Data Protection Manager. MABS v3 motsvarar Data Protection Manager 1807.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du förbereder servern eller börjar skydda en arbetsbelastning:

- [Kända problem i MABS V3](backup-mabs-release-notes-v3.md)
- [Förbereda arbetsbelastningar för säkerhetskopieringsserver](backup-azure-microsoft-azure-backup.md)
- [Använda Säkerhetskopieringsserver för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md)
- [Använda Säkerhetskopieringsserver för säkerhetskopiering av SQL Server](backup-azure-sql-mabs.md)
- [Använda modern säkerhetskopieringslagring med säkerhetskopieringsserver](backup-mabs-add-storage.md)
