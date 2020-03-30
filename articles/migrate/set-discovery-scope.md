---
title: Ange omfång för VMware VM-identifiering med Azure Migrate
description: Beskriver hur du anger identifieringsomfånget för VMware VM-utvärdering och migrering med Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337641"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Ange identifieringsscope för virtuella datorer med VMware

I den här artikeln beskrivs hur du begränsar omfattningen av identifieringen för virtuella datorer med VMware som identifieras av [Azure Migrate-installationen](migrate-appliance-architecture.md).

Azure Migrate-installationen identifierar lokala virtuella virtuella datorer när du: 

- Använd verktyget [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) för att bedöma virtuella VMware-datorer för migrering till Azure.
- Använd [verktyget Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) för [agentlös migrering](server-migrate-overview.md) av virtuella datorer med VMware till Azure.

## <a name="set-discovery-scope"></a>Ange identifieringsomfattning


När du har konfigurerat Azure Migrate-enheten för VMware-virtuella datorers utvärdering eller migrering börjar installationen identifiera virtuella datorer och skicka VM-metadata till Azure. Innan du ansluter installationen till vCenter Server för identifiering kan du ange identifieringsomfånget så att identifieringen begränsas till vCenter Server-datacenter, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer.

Om du vill ange scopet tilldelar du behörigheter för kontot som Azure Migrate använder för att komma åt vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Skapa ett vCenter-användarkonto

Om du inte redan har konfigurerat ett vCenter-användarkonto som Azure Migrate-enheten använder för att identifiera, bedöma och migrera virtuella virtuella datorer för VMware gör du det först.

1.    Logga in på vSphere Web Client som vCenter Server-administratör.
2.    Välj **Administration** > **SSO-användare och grupper**och klicka på fliken **Användare.**
3.    Välj ikonen **Ny användare.**
4.    Fyll i den nya användarinformationen > **OK**.

Kontobehörigheterna beror på vad du distribuerar.

**Funktion** | **Kontobehörigheter**
--- | ---
[Bedöma](tutorial-assess-vmware.md)| Kontot behöver skrivskyddad åtkomst.
[Identifiera appar/roller/funktioner](how-to-discover-applications.md) | Kontot behöver skrivskyddad åtkomst, med privilegier aktiverade för virtuella datorer > gästoperationer.
[Analysera beroenden (agentless)](how-to-create-group-machine-dependencies-agentless.md) | Kontot behöver skrivskyddad åtkomst, med privilegier aktiverade för virtuella datorer > gästoperationer.
[Migrera (agentless)](tutorial-migrate-vmware.md) | Du behöver en roll som har tilldelats rätt behörigheter.<br/><br/> Om du vill skapa en roll loggar du in på vSphere Web Client som vCenter Server-administratör. Välj rollen vCenter Server > **Skapa**. Ange ett rollnamn, till exempel <em>Azure_Migrate</em>och tilldela de [behörigheter som krävs](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) till rollen.


## <a name="assign-permissions-on-vcenter-objects"></a>Tilldela behörigheter för vCenter-objekt

Du kan tilldela behörigheter för lagerobjekt med någon av två metoder:

- Tilldela en roll med de behörigheter som krävs, till det konto du använder, för alla överordnade objekt som är värdar för virtuella datorer som du vill identifiera/migrera.
- Du kan också tilldela rollen och användarkontot på datacenternivå och sprida dem till de underordnade objekten. Ge sedan kontot en **ingen åtkomstroll** för varje objekt som du inte vill identifiera/migrera. Vi rekommenderar inte den här metoden eftersom den är besvärlig och kan exponera åtkomstkontroller, eftersom varje nytt underordnade objekt automatiskt beviljas åtkomst som ärvts från det överordnade objektet.

Så här tilldelar du en roll till kontot som du använder för alla relevanta objekt:

- **För bedömning:** För vm-utvärdering, tillämpa den **skrivskyddade** rollen på vCenter-användarkontot för alla överordnade objekt som är värdar för virtuella datorer som du vill identifiera. Överordnade objekt ingår: värd, mapp med värdar, kluster och mapp med kluster i hierarkin, upp till datacentret. Sprida dessa behörigheter till underordnade objekt i hierarkin.

    ![Tilldela behörigheter](./media/tutorial-assess-vmware/assign-perms.png)

- **För agentlös migrering**: För agentlös migrering, tillämpa en användardefinierad roll med [nödvändiga behörigheter](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) för användarkontot, för alla överordnade objekt som är värdar för virtuella datorer som du vill identifiera. Du kan namnge rollen <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Stöd för omfattning

Serverbedömningsverktyget kan för närvarande inte identifiera virtuella datorer om vCenter-kontot har åtkomst beviljad på mappnivå för vCenter VM. Mappar med värdar och kluster stöds.

Om du vill begränsa identifieringen efter vm-mappar slutför du nästa procedur för att säkerställa att vCenter-kontot har skrivskyddad åtkomst tilldelad på vm-nivå.

1. Tilldela skrivskyddade behörigheter för alla virtuella datorer i de vm-mappar som du vill begränsa identifieringen.
2. Bevilja skrivskyddad åtkomst till alla överordnade objekt som är värdar för virtuella datorer.
    - Alla överordnade objekt (värd, mapp med värdar, kluster, mapp med kluster) i hierarkin upp till datacentret ingår.
    - Du behöver inte sprida behörigheterna till alla underordnade objekt.
3. Använd autentiseringsuppgifterna för identifiering genom att välja datacentret som **insamlingsomfattning**. Den rollbaserade åtkomstkontrollinställningen säkerställer att motsvarande vCenter-användarkonto endast har åtkomst till klientspecifika virtuella datorer.


## <a name="next-steps"></a>Nästa steg

[Ställ in apparaten](how-to-set-up-appliance-vmware.md) och [starta kontinuerlig identifiering](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
