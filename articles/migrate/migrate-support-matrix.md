---
title: Supportmatris för Azure Migrate
description: Innehåller en sammanfattning av supportinställningar och begränsningar för Azure Migrate-tjänsten.
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: raynew
ms.openlocfilehash: 44a971894f53a3f31c068b3c3ed4912bc7e00dab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680698"
---
# <a name="azure-migrate-support-matrix"></a>Supportmatris för Azure Migrate

Du kan använda [Azure Migrate-tjänsten](migrate-overview.md) för att bedöma och migrera datorer till Microsoft Azure-molnet. Den här artikeln sammanfattar allmänna supportinställningar och begränsningar för Azure Migrate-scenarier och distributioner.

## <a name="supported-assessmentmigration-scenarios"></a>Scenarier för utvärdering/migrering som stöds

Tabellen sammanfattar scenarier för identifiering, utvärdering och migrering som stöds.

**Distribution** | **Detaljer** 
--- | --- 
**Appspecifik identifiering** | Du kan identifiera appar, roller och funktioner som körs på virtuella datorer med VMware. För närvarande är den här funktionen endast begränsad till identifiering. Bedömningen sker för närvarande på maskinnivå. Vi erbjuder ännu inte app-, roll- eller funktionsspecifik bedömning. 
**Lokal bedömning** | Utvärdera lokala arbetsbelastningar och data som körs på virtuella datorer med VMware, virtuella datorer med hyper-vm och fysiska servrar. Utvärdera med Hjälp av Azure Migrate Server Assessment och Microsoft Data Migration Assistant (DMA), samt andra verktyg och ISV-erbjudanden.
**Lokal migrering till Azure** | Migrera arbetsbelastningar och data som körs på fysiska servrar, virtuella VMware-datorer, virtuella datorer med hyper-v. fysiska servrar och molnbaserade VMS till Azure. Migrera med Azure Migrate Server Assessment och Azure Database Migration Service (DMS) och andra verktyg och ISV-erbjudanden.

> [!NOTE]
> Isv-verktyg kan för närvarande inte skicka data till Azure Migrate i Azure Government. Du kan använda integrerade Microsoft-verktyg eller använda partnerverktyg oberoende av annat.

## <a name="supported-tools"></a>Verktyg som stöds

Särskilt verktygsstöd sammanfattas i tabellen.

**Verktyg** | **Bedöma** | **Migrera** 
--- | --- | ---
Utvärdering av Azure Migrate Server | Utvärdera [virtuella datorer för VMware,](tutorial-prepare-vmware.md)virtuella datorer med [hyper-v och](tutorial-prepare-hyper-v.md)fysiska [servrar](tutorial-prepare-physical.md). |  Inte tillgängligt (NA)
Azure Migrate-servermigrering | Ej tillämpligt | Migrera virtuella datorer för [VMware,](tutorial-migrate-vmware.md) [virtuella hyper-virtuella datorer](tutorial-migrate-hyper-v.md)och fysiska [servrar](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Ej tillämpligt | Migrera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, offentliga molnarbetsbelastningar. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Utvärdera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, offentliga molnarbetsbelastningar. | Ej tillämpligt
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Utvärdera och migrera virtuella virtuella datorer, virtuella hyper-virtuella datorer, fysiska servrar, offentliga molnarbetsbelastningar. |  Migrera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, offentliga molnarbetsbelastningar.
[Enhet 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Utvärdera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, offentliga molnarbetsbelastningar.| Ej tillämpligt
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Utvärdera lokala SQL Server-databaser. | Ej tillämpligt
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | Ej tillämpligt | Migrera SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Utvärdera virtuell skrivbordsinfrastruktur (VDI) | Ej tillämpligt
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Utvärdera VMWare-virtuella datorer, virtuella hyper-virtuella datorer, Xen-virtuella datorer, fysiska datorer, arbetsstationer (inklusive VDI), offentliga molnarbetsbelastningar | Ej tillämpligt
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Ej tillämpligt | Migrera VMWare virtuella datorer, virtuella hyper-virtuella datorer, Xen virtuella datorer, virtuella KVM-datorer, fysiska datorer, offentliga molnarbetsbelastningar 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Utvärdera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, offentliga molnarbetsbelastningar. | Ej tillämpligt
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Utvärdera virtuella datorer med VMware, virtuella hyper-v-datorer, fysiska servrar, offentliga molnarbetsbelastningar och SQL Server-databaser. | Ej tillämpligt
[Migreringsassistent för webbapp](https://appmigration.microsoft.com/) | Utvärdera webbappar | Migrera webbappar.


## <a name="azure-migrate-projects"></a>Azure Migrera projekt

**Support** | **Detaljer**
--- | ---
Prenumeration | Du kan ha flera Azure Migrate-projekt i en prenumeration.
Azure-behörigheter | Du behöver behörigheter för deltagare eller ägare i prenumerationen för att skapa ett Azure Migrate-projekt.
VMwares virtuella datorer  | Utvärdera upp till 35 000 virtuella datorer i ett enda projekt.
Hyper-V:s virtuella datorer    | Utvärdera upp till 35 000 virtuella hyper-virtuella datorer i ett enda projekt.

Ett projekt kan omfatta både virtuella datorer och virtuella datorer med hyper-v, upp till bedömningsgränserna.

## <a name="azure-permissions"></a>Azure-behörigheter

För att Azure Migrate ska fungera med Azure behöver du dessa behörigheter innan du börjar utvärdera och migrera datorer.

**Aktivitet** | **Behörigheter** | **Detaljer**
--- | --- | ---
Skapa ett Azure Migrate-projekt | Ditt Azure-konto behöver behörigheter för att skapa ett projekt. | Konfigurera för [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)eller [fysiska servrar](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Registrera Azure Migrate-enheten| Azure Migrate använder en lätt [Azure Migrate-installation](migrate-appliance.md) för att bedöma datorer med Azure Migrate Server Assessment och för att köra [agentlös migrering](server-migrate-overview.md) av virtuella VMware-datorer med Azure Migrate Server Migration. Den här installationen identifierar datorer och skickar metadata och prestandadata till Azure Migrate.<br/><br/> Under registreringen registreras registerleverantörer (Microsoft.OffAzure, Microsoft.Migrate och Microsoft.KeyVault) med den prenumeration som valts i installationen, så att prenumerationen fungerar med resursleverantören. För att registrera dig behöver du åtkomst till Deltagare eller Ägare i prenumerationen.<br/><br/> **VMware**-Under introduktion skapar Azure Migrate två Azure Active Directory-appar (Azure AD). Den första appen kommunicerar mellan apparatagenterna och Azure Migrate-tjänsten. Appen har inte behörighet att ringa Azure-resurshanteringsanrop eller ha RBAC-åtkomst för resurser. Den andra appen har åtkomst till ett Azure Key Vault som skapats i användarprenumerationen för endast agentlös VMware-migrering. I agentlös migrering skapar Azure Migrate ett nyckelvalv för att hantera åtkomstnycklar till replikeringslagringskontot i din prenumeration. Den har RBAC-åtkomst på Azure Key Vault (i kundens klientorganisation) när identifiering initieras från installationen.<br/><br/> **Hyper-V**-Under introduktion. Azure Migrate skapar en Azure AD-app. Appen kommunicerar mellan apparatagenterna och Azure Migrate-tjänsten. Appen har inte behörighet att ringa Azure-resurshanteringsanrop eller ha RBAC-åtkomst för resurser. | Konfigurera för [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)eller [fysiska servrar](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Skapa ett nyckelvalv för VMware agentless migrering | Om du vill migrera virtuella virtuella datorer med VMware med agentlös Azure Migrate Server-migrering skapar Azure Migrate ett nyckelvalv för att hantera åtkomstnycklar till replikeringslagringskontot i din prenumeration. Om du vill skapa valvet anger du behörigheter (ägare eller deltagare och administratör för användaråtkomst) i resursgruppen där Azure Migrate-projektet finns. | [Ställ in](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) behörigheter.

## <a name="supported-geographies-public-cloud"></a>Geografiska områden som stöds (offentligt moln)

Du kan skapa ett Azure Migrate-projekt i ett antal geografiska områden i det offentliga molnet. Även om du bara kan skapa projekt i dessa geografiska områden kan du bedöma eller migrera datorer för andra målplatser. Projektets geografi används endast för att lagra de identifierade metadata.

**Geografi** | **Lagringsplats för metadata**
--- | ---
Asien och stillahavsområdet | Östasien eller Sydostasien
Australien | Östra Australien eller Sydöstra Australien
Brasilien | Brasilien, södra
Kanada | Kanada Central eller Kanada Öst
Europa | Europa, norra eller Europa, västra
Frankrike | Frankrike, centrala
Indien | Centrala Indien eller södra Indien
Japan |  Japan Öst eller Västra Japan
Korea | Korea Central eller Korea Syd
Storbritannien | Storbritannien Syd- eller Storbritannien Väst
USA | Centrala USA eller västra USA 2


## <a name="supported-geographies-azure-government"></a>Geografiska områden som stöds (Azure Government)

**Aktivitet** | **Geografi** | **Detaljer**
--- | --- | ---
Skapa projekt | USA | Metadata lagras i US Gov Arizona, US Gov Virginia
Målbedömning | USA | Målregioner: US Gov Arizona, US Gov Virginia, US Gov Texas
Målreplikering | USA | Målregioner: US DoD Central, US DoD East, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>VMware bedömning och migrering

[Granska](migrate-support-matrix-vmware.md) supportmatrisen för utvärdering av Azure Migrate Server Assessment och Server Migration för virtuella datorer med VMware.

## <a name="hyper-v-assessment-and-migration"></a>Utvärdering och migrering av Hyper-V

[Granska](migrate-support-matrix-hyper-v.md) supportmatrisen för utvärdering av Azure Migrate Server och Server Migration för virtuella datorer med hyper-V.



## <a name="azure-migrate-versions"></a>Azure Migrera versioner

Det finns två versioner av Azure Migrate-tjänsten:

- **Aktuell version**: Med den här versionen kan du skapa nya Azure Migrate-projekt, upptäcka lokala utvärderingar och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md).
- **Tidigare version**: För kunder som använder den tidigare versionen av Azure Migrate (endast bedömning av lokala virtuella virtuella datorer med vmware stöddes) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte skapa nya Azure Migrate-projekt eller utföra nya identifieringar.

## <a name="next-steps"></a>Nästa steg

- Utvärdera virtuella datorer med [VMware](tutorial-assess-vmware.md) för migrering.
- [Utvärdera virtuella virtuella hyper-v-datorer](tutorial-assess-hyper-v.md) för migrering.

