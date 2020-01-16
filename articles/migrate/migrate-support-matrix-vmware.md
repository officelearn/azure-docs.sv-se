---
title: Stöd för VMware-utvärdering i Azure Migrate
description: Läs mer om stöd för VMware-utvärdering i Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029013"
---
# <a name="support-matrix-for-vmware-assessment"></a>Support mat ris för VMware-utvärdering 

Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera virtuella VMware-datorer med [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-migration-tool). Om du vill ha information om hur du migrerar virtuella VMware-datorer till Azure läser du [matrisen migration support](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Översikt

Om du vill utvärdera lokala datorer för migrering till Azure med den här artikeln lägger du till verktyget Azure Migrate: Server utvärderings verktyg i ett Azure Migrate projekt. Du distribuerar [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala datorer och skickar konfigurations-och prestanda data till Azure. Efter dator identifiering samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp.


## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Utvärderings gränser**| Upptäck och utvärdera upp till 35 000 virtuella VMware-datorer i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration. Ett projekt kan omfatta virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar, upp till utvärderings gränserna.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.


## <a name="application-discovery"></a>Programidentifiering

Förutom att identifiera datorer kan Azure Migrate: Server utvärdering kan identifiera appar, roller och funktioner som körs på datorer. Genom att identifiera din program inventering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar. 

**Support** | **Detaljer**
--- | ---
Identifiering | Identifieringen är agent lösa, använder autentiseringsuppgifter för maskin-gäst och fjärråtkomst till datorer med WMI och SSH-samtal.
Datorer som stöds | Lokala virtuella VMware-datorer.
Datorns operativ system | Alla Windows-och Linux-versioner
Autentiseringsuppgifter | För närvarande har stöd för att använda en autentiseringsuppgift för alla Windows-servrar och en autentiseringsuppgift för alla Linux-servrar.<br/><br/> Du skapar ett gäst användar konto för virtuella Windows-datorer och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla virtuella Linux-datorer.
Begränsningar | För app-Discovery kan du identifiera upp till 10000 per apparat. 

## <a name="vmware-requirements"></a>Krav för VMware

**VMware** | **Detaljer**
--- | ---
**vCenter Server** | Datorer som du vill identifiera och utvärdera måste hanteras av vCenter Server version 5,5, 6,0, 6,5 eller 6,7.
**Behörigheter (utvärdering)** | vCenter Server skrivskyddat konto.
**Behörigheter (app-Discovery)** | vCenter Server konto med skrivskyddad åtkomst och behörigheter som har Aktiver ATS för virtuella datorer > gäst åtgärder.
**Behörigheter (beroende visualisering)** | Center Server-konto med skrivskyddad åtkomst och privilegier som är aktiverade för **virtuella datorer** > **gäst åtgärder**.


## <a name="azure-migrate-appliance-requirements"></a>Krav för Azure Migrate utrustning

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Installationen av VMware distribueras med hjälp av en beredskaps mall som importeras till vCenter Server. 

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- Lär dig mer om [webb adresser](migrate-appliance.md#url-access) som behövs för att få åtkomst till enheten.

## <a name="port-access"></a>Port åtkomst

**Enhet** | **Anslutning**
--- | ---
Enhet | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
vCenter Server | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.

## <a name="dependency-visualization"></a>Visualisering av beroenden

Beroende visualisering hjälper dig att visualisera beroenden mellan datorer som du vill utvärdera och migrera. Du använder vanligt vis beroende mappning när du vill utvärdera datorer med högre Tillförlitlighets nivåer. För virtuella VMware-datorer stöds beroende visualisering enligt följande:

- **Beroende visualisering för agent utan agent**: det här alternativet är för närvarande en för hands version. Du behöver inte installera några agenter på datorer.
    - Det fungerar genom att samla in data för TCP-anslutningen från datorer som den är aktive rad för. När beroende identifiering har startats samlar enheten in data från datorer vid ett avsöknings intervall på fem minuter.
    - Följande data samlas in:
        - TCP-anslutningar
        - Namn på processer som har aktiva anslutningar
        - Namn på installerade program som kör ovanstående processer
        - Nej. anslutningar som identifieras vid varje avsöknings intervall
- **Agent-baserad beroende visualisering**: om du vill använda en agent-baserad beroende visualisering måste du hämta och installera följande agenter på varje lokal dator som du vill analysera.
    - Installera Microsoft Monitoring Agent (MMA) på varje dator. [Läs mer](how-to-create-group-machine-dependencies.md#install-the-mma) om hur du installerar MMA-agenten.
    - Installera beroende agenten på varje dator. [Läs mer](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) om hur du installerar beroende agenten.
    - Om du har datorer utan Internetanslutning måste du dessutom ladda ned och installera Log Analytics-gatewayen på dem.


## <a name="next-steps"></a>Nästa steg

- [Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar.
- [Förbered för VMware](tutorial-prepare-vmware.md) -utvärdering.
