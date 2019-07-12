---
title: Azure Migrate-arkitektur | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811432"
---
# <a name="azure-migrate-architecture-and-processes"></a>Azure Migrate-arkitektur och processer

[Azure Migrate](migrate-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV). 

 Den här artikeln beskriver bedömning och migreringsarkitektur och processer för Azure Migrate Server-utvärdering och migrera servermigrering i Azure.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Utvärdering med Azure Migrate Server-utvärdering

Azure Migrate Server-utvärdering kan utvärdera virtuella VMware-datorer och Hyper-V-datorer för migrering till Azure. Bedömning fungerar på följande sätt:

1. **Förbereda för utvärdering**: Lokala, konfigurera en lätt Azure Migrate installation på plats som en VMware VM eller Hyper-V-dator och registrera installationen med Azure Migrate.
2. **Identifiera virtuella datorer**: Azure Migrate-installationen körs för att identifiera lokala virtuella datorer. 
    - Inget behöver installeras på identifierade virtuella datorer.
    - VM-metadata innehåller information om kärnor, minne, diskar, diskstorlekar och nätverkskort.
    - Prestandadata innehåller information om processor och minne användning, disk-IOPS, diskgenomflöde (Mbit/s) och nätverksutdata (Mbit/s)
- **Samla in och utvärdera datorer**: När identifieringen har slutförts i Azure identifierade portal som du har samlat in virtuella datorer i grupper som vanligtvis består av virtuella datorer som du vill migrera tillsammans. Du kan köra en utvärdering för en grupp.


## <a name="vmware-assessment"></a>VMware-utvärdering 

Diagrammet sammanfattar hur VMware VM-utvärderingen fungerar med hjälp av Azure Migrate Server-utvärdering.

![Arkitektur för migreringsutvärdering VMware](./media/migrate-architecture/sas-architecture-vmware.png)

Processen är följande:

1. **Distribuera Azure Migrate-installation**:

    a. Ladda ned mallen (OVA) från Azure-portalen.

    b. Importera den till vCenter Server-datorn för att skapa den virtuella datorn.

    c. Ansluta till den här virtuella datorn, konfigurerar grundläggande inställningar för den och registrera den med Azure Migrate.

2. **Starta identifiering**:

    a. Ansluta till insamlaren appen som körs på installation för att inleda identifieringen.

    b. Enheten som skickar kontinuerligt metadata-och prestandadata från de virtuella datorerna till Azure.

    - Enheten som är alltid ansluten till Azure Migrate-tjänsten.
    - Miljöändringar under identifieringen av kontinuerlig som avbildas. Till exempel att lägga till virtuella datorer i identifieringsomfånget eller lägga till VM-diskar eller nätverkskort.

3. **Utvärdera datorer**:

    a. När identifieringen har slutförts i Azure identifierade portal som du har samlat in virtuella datorer i grupper.  En grupp består vanligtvis av virtuella datorer som du vill migrera tillsammans.

    b. Kör en utvärdering för varje grupp.

4. **Granska utvärdering**: 

    a. När utvärderingen är klar kan du visa den i portalen.

    b. Ladda ned utvärderingen i Excel-format för vidare analys.



### <a name="vmware-ports"></a>VMware-portar
Azure Migrate använder följande anslutningsportar för VMware:

**Källa** | **Mål** | **Port** | **Detaljer**
--- | --- | --- | ---
Azure Migrate-installation | Tjänsten Azure Migrate | Mål-TCP 443 | Skicka metadata-och prestandadata till Azure Migrate.
Azure Migrate-installation | vCenter Server | Mål-TCP 443 | Ansluta till vCenter-servern för metadata och prestanda. 443 är standard, men kan ändras med vCenter lyssnar på en annan port. 
RDP-klient | Azure Migrate-installation | Target-TCP3389 | RDP-anslutning till utlösaren identifiering av programmet.

### <a name="collected-vmware-metadata"></a>Insamlade metadata för VMware

Enheten som skickar metadata och prestandarelaterade data från de virtuella datorerna till Azure.

**Åtgärd** | **Detaljer**
--- | ---
**Insamlade metadata** | vCenter VM-namn<br/> vCenter VM-sökväg (värdkluster/mapp)<br/> IP- och MAC-adresser<br/> Operativsystem<br/> Antalet diskar/kärnor/nätverkskort<br/> Minnes- och storlek.
**Insamlade prestandadata** | Användning av processor/minne<br/> Per diskdata (Läs/Skriv diskdataflöde; disk läsningar/skrivningar per sekund)<br/> NIC-data (network i nätverk ut).<br/><br/> Prestandadata samlas in kontinuerligt när enheten som ansluter till vCenter-servern. Historiska data samlas inte in.

## <a name="hyper-v-assessment"></a>Hyper-V-utvärdering

Diagrammet sammanfattar hur Hyper-V bedömning fungerar registrerar Azure Migrate Server-utvärdering.

![Arkitektur för migreringsutvärdering Hyper-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

Processen är följande:

1. **Skapa Azure Migrate**:

    a. Ladda ned den virtuella datorn i komprimerat format från Azure-portalen.

    b. Importera den till en Hyper-V-värd.

    c. Anslut till den virtuella datorn-installationen. Konfigurera grundläggande inställningar för den och registrera den med Azure Migrate.

2. **Starta identifiering**:

    a. Anslut till Azure Migrate-installation för att inleda identifieringen. Inget behöver installeras på identifierade virtuella datorer.

    b. Enheten som skickar kontinuerligt VM metadata och prestanda till Azure Migrate.

    - Enheten som är alltid ansluten till tjänsten Azure Migrate (med CIM-sessioner).
    - Miljöändringar under identifieringen av kontinuerlig som avbildas. Till exempel att lägga till virtuella datorer i identifieringsomfånget eller lägga till VM-diskar eller nätverkskort.


3. **Utvärdera datorer**:

    a. När identifieringen har slutförts i Azure identifierade portal som du har samlat in virtuella datorer i grupper.  En grupp består vanligtvis av virtuella datorer som du vill migrera tillsammans.

    b. Kör en utvärdering för varje grupp.

4. **Granska utvärdering**:

    a. När utvärderingen är klar kan du visa den i portalen.

    b. Ladda ned utvärderingen i Excel-format för vidare analys.

### <a name="hyper-v-ports"></a>Hyper-V-portar

Azure Migrate-tjänsten använder följande anslutningsportar för Hyper-V:

**Källa** | **Mål** | **Port** | **Detaljer**
--- | --- | --- | ---
Azure Migrate-installation | Tjänsten Azure Migrate | Mål-TCP 443 | Skicka metadata-och prestandadata till Azure Migrate.
Azure Migrate-installation | Hyper-V-värd/kluster | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | Ansluta till värden för metadata och prestanda. CIM-session som används för anslutning
RDP-klient | Azure Migrate-installation | Target-TCP3389 | RDP-anslutning till utlösaren identifiering av programmet.

## <a name="collected-hyper-v-vm-metadata"></a>Insamlade metadata för Hyper-V-dator

Enheten som skickar metadata och prestandarelaterade data från de virtuella datorerna till Azure.


**Åtgärd** | **Detaljer**
--- | ---
**Insamlade metadata** | VM-namn<br/> VM-sökväg (värdkluster/mapp)<br/> IP- och MAC-adresser<br/> Operativsystem<br/> Antalet diskar/kärnor/nätverkskort<br/> Minnes- och storlek<br/> Disk-IOPS, diskgenomflöde (Mbit/s), network-o (Mbit/s)
**Insamlade prestandadata** |  Användning av processor/minne<br/> Per diskdata (Läs/Skriv diskdataflöde; disk läsningar/skrivningar per sekund)<br/> NIC-data (network i nätverk ut).<br/><br/> Prestandadata samlas in kontinuerligt när enheten som ansluter till Hyper-V-värden. Historiska data samlas inte in.




## <a name="migration-with-azure-migrate-server-migration"></a>Migrering med Azure Migrate-servermigrering

Du kan använda Azure Migrate-servermigrering för att migrera följande till Azure:

- Lokala virtuella VMware-datorer
- En lokal Hyper-V-datorer
- Lokala fysiska servrar
- AWS Windows VM-instanser
- GCP Windows VM-instanser

Processen skiljer sig något, beroende på vad du migrerar.


## <a name="migrate-vmware-vms"></a>Migrera virtuella VMware-datorer

Azure Migrate servermigrering erbjuder två metoder för att migrera lokala VMware-datorer:

- **Agentlös replikering**: Migrera virtuella datorer utan att behöva installera något på dem.
- **Agent-baserad replikering**. Installera en agent på den virtuella datorn för replikering.

Agentlös replikeringen har enklare från en distributionsperspektiv, men den har för närvarande ett antal begränsningar. [Läs mer](server-migrate-overview.md) om dessa begränsningar.
 

### <a name="agent-based-migration"></a>Agent-baserad migrering

Agentbaserad migrering av virtuella VMware-datorer kräver ett antal komponenter som ska distribueras, som sammanfattas i tabellen.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Configuration server-dator** | En enda lokal VMware VM som distribueras från en nedladdade OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter, bland annat konfigurationsservern och processervern. | **Konfigurationsservern**: Samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.<br/><br/> **Processerver**: Installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar det till Azure. Processervern också installerar Azure Site Recovery-Mobilitetstjänsten på virtuella datorer och utför automatisk identifiering av lokala datorer.
**Mobilitetstjänsten** | Mobilitetstjänsten måste installeras på varje VMware VM som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en metod för automatisk distribution, till exempel System Center Configuration Manager.





### <a name="agent-based-replication-process"></a>Agentbaserad replikeringsprocessen

![Replikeringsprocessen](./media/migrate-architecture/v2a-architecture-henry.png)

1. När du aktiverar replikering för en virtuell dator, börjar den inledande replikeringen till Azure. 
    - Replikering är på blocknivå, nästan kontinuerlig, med hjälp av mobilitetstjänstagenten som körs på den virtuella datorn.
    - Inställningarna för replikeringsprincipen tillämpas:
        - **Tröskelvärde för Replikeringspunktmål**. Den här inställningen påverkar inte replikering. Det hjälper dig med övervakning. En händelse inträffar och eventuellt ett e-postmeddelande skickas om aktuellt RPO överskrider tröskelvärdet gränsen som du anger.
        - **Kvarhållning av återställningspunkt**. Den här inställningen anger hur långt tillbaka i tiden som du vill gå när avbrott uppstår. Maximal kvarhållning på premium storage är 24 timmar. Det är 72 timmar med standardlagring. 
        - **Appkonsekventa ögonblicksbilder**. Appkompatibel ögonblicksbild kan ta varje 1 till 12 timmar, beroende på behoven för dina appar. Ögonblicksbilder är standard Azure blob-ögonblicksbilder. Mobilitetsagenten som körs på en virtuell dator begär en VSS-ögonblicksbild i enlighet med den här inställningen och bokmärken som point-in-time som ett program som är konsekvent pekar i dataströmmen för replikering.

2. Trafik som replikeras till Azure storage-offentliga slutpunkter för via internet.

    - Alternativt kan du använda Azure ExpressRoute med [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - Replikering av trafik via ett plats-till-plats virtuellt privat nätverk (VPN) från en lokal plats till Azure stöds inte.
3. När den inledande replikeringen är klar börjar replikeringen av deltaändringar till Azure. Spårade ändringar för en dator skickas till processervern.
2. Kommunikation händer följande:

    - Virtuella datorer kommunicera med den lokala konfigurationsservern på port HTTPS 443 inkommande, för replikeringshantering.
    - Konfigurationsservern samordnar replikeringen med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (som körs på configuration server-datorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure storage över port 443 utgående.
5. Replikeringsdata loggar första mark i ett cachelagringskonto i Azure. Dessa loggar bearbetas och data lagras i en Azure hanterade diskar (Azure Site Recovery-dirigering disk). Återställningspunkter skapas på disken.







## <a name="next-steps"></a>Nästa steg

- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
- Hjälp från communityn, finns det [Azure Migrate MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) eller [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

