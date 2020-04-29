---
title: Ange omfattning för identifiering av VMware VM med Azure Migrate
description: Beskriver hur du ställer in identifierings omfånget för utvärdering av virtuella VMware-datorer och migrering med Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337641"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Ange identifierings omfång för virtuella VMware-datorer

I den här artikeln beskrivs hur du begränsar identifierings området för virtuella VMware-datorer som identifieras av [Azure Migrate-enheten](migrate-appliance-architecture.md).

Azure Migrates enheten identifierar lokala virtuella VMware-datorer när du: 

- Använd verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) för att utvärdera virtuella VMware-datorer för migrering till Azure.
- Använd verktyget [Azure Migrate: Migreringsverktyg](migrate-services-overview.md#azure-migrate-server-migration-tool) för migrering utan [agent](server-migrate-overview.md) för virtuella VMware-datorer till Azure.

## <a name="set-discovery-scope"></a>Ange identifierings omfång


När du har konfigurerat Azure Migrate-installationen för utvärdering eller migrering av virtuella VMware-datorer börjar installations programmet identifiera virtuella datorer och skicka VM-metadata till Azure. Innan du ansluter installationen till vCenter Server för identifiering kan du ange identifierings omfånget så att identifieringen begränsas till vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer.

Om du vill ange omfånget tilldelar du behörigheter för kontot som Azure Migrate använder för att få åtkomst till vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Skapa ett vCenter-användarkonto

Om du inte redan har konfigurerat ett vCenter-användarkonto som Azure Migrate-enheten använder för att identifiera, utvärdera och migrera virtuella VMware-datorer gör du det först.

1.    Logga in på vSphere-webbklient som vCenter Server administratör.
2.    Välj **Administration** > av**SSO-användare och grupper**och klicka på fliken **användare** .
3.    Välj ikonen **ny användare** .
4.    Fyll i den nya användar informationen > **OK**.

Konto behörigheterna beror på vad du distribuerar.

**Funktion** | **Konto behörigheter**
--- | ---
[Beräknas](tutorial-assess-vmware.md)| Kontot behöver skrivskyddad åtkomst.
[Identifiera appar/roller/funktioner](how-to-discover-applications.md) | Kontot behöver skrivskyddad åtkomst, med behörigheter som är aktiverade för virtuella datorer > gäst åtgärder.
[Analysera beroenden (utan agent)](how-to-create-group-machine-dependencies-agentless.md) | Kontot behöver skrivskyddad åtkomst, med behörigheter som är aktiverade för virtuella datorer > gäst åtgärder.
[Migrera (utan agent)](tutorial-migrate-vmware.md) | Du behöver en roll som har tilldelats rätt behörigheter.<br/><br/> Om du vill skapa en roll loggar du in på vSphere-webbklient som vCenter Server administratör. Välj vCenter Server-instansen > **skapa roll**. Ange ett rollnamn, till exempel <em>Azure_Migrate</em>, och tilldela de [behörigheter som krävs](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) för rollen.


## <a name="assign-permissions-on-vcenter-objects"></a>Tilldela behörigheter för vCenter-objekt

Du kan tilldela behörigheter för lager objekt på något av två sätt:

- Tilldela en roll med de behörigheter som krävs, till det konto som du använder, för alla överordnade objekt som är värdar för de virtuella datorer som du vill identifiera/migrera.
- Alternativt kan du tilldela rollen och användar kontot på data center nivå och sprida dem till de underordnade objekten. Ge sedan kontot **Ingen åtkomst** roll för varje objekt som du inte vill identifiera/migrera. Vi rekommenderar inte den här metoden eftersom den är besvärlig och kan exponera åtkomst kontroller, eftersom varje nytt underordnat objekt automatiskt beviljas åtkomst som ärvs från den överordnade.

Gör så här om du vill tilldela en roll till det konto som du använder för alla relevanta objekt:

- **För bedömning**: Använd den **skrivskyddade** rollen för vCenter-användarkontot för alla överordnade objekt som är värdar för virtuella datorer som du vill identifiera för utvärdering av virtuell dator. Överordnade objekt som ingår: värd, mapp med värdar, kluster och mapp för kluster i hierarkin, upp till data centret. Sprid dessa behörigheter till underordnade objekt i hierarkin.

    ![Tilldela behörigheter](./media/tutorial-assess-vmware/assign-perms.png)

- **För migrering**utan agent: Använd en användardefinierad roll med [nödvändiga behörigheter](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) till användar kontot för alla överordnade objekt som är värdar för virtuella datorer som du vill identifiera. Du kan namnge roll <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Stöd för omfattning

För närvarande kan Server utvärderings verktyget inte identifiera virtuella datorer om vCenter-kontot har åtkomst som beviljats på den virtuella vCenter-mappens nivå. Mappar med värdar och kluster stöds.

Om du vill begränsa identifieringen av VM-mappar slutför du nästa procedur för att se till att vCenter-kontot har skrivskyddad åtkomst som tilldelats på en VM-nivå.

1. Tilldela skrivskyddade behörigheter för alla virtuella datorer i de VM-mappar som du vill använda för identifiering.
2. Bevilja skrivskyddad åtkomst till alla överordnade objekt som är värdar för virtuella datorer.
    - Alla överordnade objekt (värd, mapp med värdar, kluster, kluster kluster) i hierarkin till data centret ingår.
    - Du behöver inte sprida behörigheterna till alla underordnade objekt.
3. Använd autentiseringsuppgifterna för identifiering genom att välja Data Center som **samlings omfång**. Med den rollbaserad åtkomst kontroll inställningen ser du till att motsvarande vCenter-användarkonto har åtkomst till endast klient datorer som är användarspecifika.


## <a name="next-steps"></a>Nästa steg

[Konfigurera enheten](how-to-set-up-appliance-vmware.md) och [Starta kontinuerlig identifiering](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
