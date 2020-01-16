---
title: Stöd för Hyper-V-utvärdering i Azure Migrate
description: Läs mer om stöd för Hyper-V-utvärdering med Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028779"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Stöd mat ris för Hyper-V-utvärdering

I den här artikeln sammanfattas support inställningar och begränsningar för utvärdering av virtuella Hyper-V-datorer med [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Om du letar efter information om hur du migrerar virtuella Hyper-V-datorer till Azure läser du [matrisen migration support](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Översikt

Om du vill utvärdera lokala datorer för migrering till Azure med den här artikeln lägger du till verktyget Azure Migrate: Server utvärderings verktyg i ett Azure Migrate projekt. Du distribuerar [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala datorer och skickar konfigurations-och prestanda data till Azure. Efter dator identifiering samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp.


## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Utvärderings gränser**| Identifiera och utvärdera upp till 35 000 virtuella Hyper-V-datorer i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration. Ett projekt kan omfatta virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar, upp till utvärderings gränserna.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 5000 virtuella Hyper-V-datorer.<br/><br/> Enheten kan ansluta till upp till 300 Hyper-V-värdar.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.



## <a name="hyper-v-host-requirements"></a>Krav för Hyper-V-värd

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Värd distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. <br/> Om du inte vill tilldela administratörs behörighet skapar du ett lokalt konto eller domän användar konto och lägger till användaren i dessa grupper – fjärrhanterings användare, Hyper-V-administratörer och användare av prestanda övervakning. |
| **Värd operativ system** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2.<br/> Du kan inte utvärdera virtuella datorer som finns på Hyper-V-värdar som kör Windows Server 2012. |
| **PowerShell-fjärrkommunikation**   | Måste vara aktiverat på varje värd. |
| **Hyper-V-replikering**       | Om du använder Hyper-V-replikering (eller om du har flera virtuella datorer med samma VM-identifierare) och identifierar både de ursprungliga och replikerade virtuella datorerna med hjälp av Azure Migrate, är det inte säkert att utvärderingen som genereras av Azure Migrate är korrekt. |


## <a name="hyper-v-vm-requirements"></a>Krav för virtuell Hyper-V-dator

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure. |
| **Integrerings tjänster**       | [Integrerings tjänsterna för Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på de virtuella datorer som du bedömer, för att kunna avbilda information om operativ systemet. |


## <a name="azure-migrate-appliance-requirements"></a>Krav för Azure Migrate utrustning

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Installationen av Hyper-V körs på en virtuell Hyper-V-dator och distribueras med hjälp av en komprimerad virtuell Hyper-V-hårddisk som du hämtar från Azure Portal. 

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---hyper-v) för Hyper-V.
- Lär dig mer om [webb adresser](migrate-appliance.md#url-access) som behövs för att få åtkomst till enheten.

## <a name="port-access"></a>Port åtkomst

I följande tabell sammanfattas port kraven för utvärdering.

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/> Utgående anslutningar på portarna 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Hyper-V-värd/-kluster** | Inkommande anslutningar på WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations-och prestanda-metadata för virtuella Hyper-V-datorer med en Common Information Model CIM-session.



## <a name="next-steps"></a>Nästa steg

[Förbered för utvärdering av Hyper-V VM](tutorial-prepare-hyper-v.md)
