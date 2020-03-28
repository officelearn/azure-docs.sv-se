---
title: Förbereda virtuella virtuella datorer för VMware för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder för utvärdering/migrering av virtuella datorer med Virtuella VMware-datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: aecc82ff4403c044fae95eb9b7c8c7561fcb82b6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336671"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Förbereda virtuella VMware-datorer för utvärdering och migrering till Azure

Den här artikeln hjälper dig att förbereda för bedömning och/eller migrering av lokala virtuella datorer med VMware till Azure med [Azure Migrate](migrate-services-overview.md).



Den här självstudien är den första i en serie som visar hur du bedömer och migrerar virtuella virtuella datorer för VMware. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered VMware för vm-utvärdering med verktyget Azure Migrate:Server Assessment.
> * Förbered VMware för VM-migrering med verktyget Azure Migrate:Server Migration. 

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De är användbara när du lär dig hur du konfigurerar en distribution och som ett snabbt proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Du behöver dessa behörigheter för dessa uppgifter i Azure, innan du kan bedöma eller migrera virtuella virtuella datorer för VMware.

**Aktivitet** | **Detaljer** 
--- | --- 
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver behörigheter förtributör eller ägare för att skapa ett projekt. 
**Registrera resursleverantörer** | Azure Migrate använder en lätt Azure Migrate-installation för att identifiera och bedöma virtuella datorer med VMware och migrera dem till Azure med Azure Migrate:Server Assessment.<br/><br/> Vid registrering av apparater registreras resursleverantörer med det abonnemang som valts i apparaten. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Om du vill registrera resursleverantörerna behöver du en deltagar- eller ägarroll i prenumerationen.
**Skapa Azure AD-appar** | När du registrerar installationen skapar Azure Migrate Azure Active Directory -appar (Azure AD). <br/><br/> - Den första appen används för kommunikation mellan agenter som körs på installationen och deras respektive tjänster som körs på Azure.<br/><br/> - Den andra appen används uteslutande för att komma åt KeyVault som skapats i användarens prenumeration för agentlös VMware VM-migrering. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Du behöver behörigheter för att skapa Azure AD-appar (tillgängliga i rollen Programutvecklare).
**Skapa ett nyckelvalv** | Om du vill migrera virtuella virtuella datorer med hjälp av agentlös migrering skapar Azure Migrate ett nyckelvalv för att hantera åtkomstnycklar till replikeringslagringskontot i din prenumeration.<br/><br/> För att skapa valvet behöver du behörigheter för rolltilldelning för resursgruppen där Azure Migrate-projektet finns.




### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure-portalen och välj **Åtkomstkontroll (IAM)**.
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter.
3. Du bör ha **behörigheten Deltagare** eller **Ägare.**
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerationsägare arbetar du med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera apparaten

Om du vill registrera installationen tilldelar du behörigheter för Azure Migrate för att skapa Azure AD-apparna under installationens registrering. Behörigheterna kan tilldelas med någon av följande metoder:

- **Bevilja behörigheter**: En klient/global administratör kan bevilja behörigheter till användare i klienten för att skapa och registrera Azure AD-appar.
- **Tilldela rollen programutvecklare**: En klient/global administratör kan tilldela rollen Programutvecklare (som har behörigheterna) till kontot.

> [!NOTE]
> - Apparna har inga andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
> - Du behöver bara dessa behörigheter när du registrerar en ny apparat. Du kan ta bort behörigheterna när apparaten har konfigurerats.


#### <a name="grant-account-permissions"></a>Bevilja kontobehörigheter

Om du vill att klient/global administratör ska bevilja behörigheter gör du så här:

1. I Azure AD bör klienten/den globala administratören navigera till**Användarinställningar för** **Azure Active Directory** > **Users** > .
2. Administratören ska ange **Appregistreringar** till **Ja**. Det här är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Tilldela rollen Programutvecklare

Alternativt kan klient-/globala administratören tilldela rollen Programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) om att tilldela en roll.

### <a name="assign-permissions-to-create-a-key-vault"></a>Tilldela behörigheter för att skapa ett nyckelvalv

Om du vill aktivera Azure Migrate för att skapa ett Nyckelvalv tilldelar du behörigheter enligt följande:

1. Välj **Åtkomstkontroll (IAM)** i resursgruppen i Azure-portalen .
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter.

    - För att köra serverutvärdering räcker **det med deltagarbehörighet.**
    - Om du vill köra agentlös servermigrering bör du ha behörigheter **för ägare** (eller **deltagare** och **användaråtkomstadministratör).**

3. Om du inte har de behörigheter som krävs begär du dem från resursgruppens ägare.



## <a name="prepare-for-vmware-vm-assessment"></a>Förbered för VM-bedömning för VMware

För att förbereda för VMware VM-utvärdering måste du:

- **Verifiera VMware-inställningar**. Kontrollera att de virtuella portar och virtuella datorer som du vill migrera uppfyller kraven.
- **Skapa ett konto för bedömning**. Azure Migrate använder det här kontot för att komma åt vCenter Server för att identifiera virtuella datorer för bedömning.
- **Kontrollera apparatens krav**. Verifiera distributionskrav för Azure Migrate-enheten innan du distribuerar den.

### <a name="verify-vmware-settings"></a>Verifiera VMware-inställningar

1. [Kontrollera](migrate-support-matrix-vmware.md#vmware-requirements) VMware server krav för bedömning.
2. [Kontrollera](migrate-support-matrix-vmware.md#port-access) att de portar du behöver är öppna på vCenter Server.
3. På vCenter Server kontrollerar du att ditt konto har behörighet att skapa en virtuell dator med hjälp av en OVA-fil. Du distribuerar Azure Migrate-enheten som en virtuell VMware-dator med hjälp av en OVA-fil.


### <a name="set-up-an-account-for-assessment"></a>Skapa ett konto för bedömning

Azure Migrate måste komma åt vCenter Server för att identifiera virtuella datorer för bedömning och agentlös migrering.

- Om du planerar att identifiera program eller visualisera beroende på ett agentlöst sätt skapar du ett vCenter Server-konto med skrivskyddad åtkomst tillsammans med privilegier som är aktiverade för **virtuella datorer** > **Gästoperationer**.

  ![rättigheter för vCenter Server-konto](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Om du inte planerar att identifiera program och agentlös beroendevisualisering konfigurerar du ett skrivskyddat konto för vCenter-servern.

### <a name="verify-appliance-settings-for-assessment"></a>Verifiera inställningar för apparat för bedömning

Innan du konfigurerar Azure Migrate-enheten och börjar utvärderingen i nästa självstudiekurs förbereder du för distribution av enheter.

1. [Verifiera](migrate-appliance.md#appliance---vmware) Azure Migrera installationskrav.
2. [Granska](migrate-appliance.md#url-access) de Azure-url:er som enheten behöver komma åt. Om du använder en URL-baserad brandvägg eller proxy kontrollerar du att den ger åtkomst till de webbadresser som krävs.
3. [Granska data](migrate-appliance.md#collected-data---vmware) som enheten samlar in under identifiering och bedömning.
4. [Observera](migrate-support-matrix-vmware.md#port-access) tillträdeskrav för porten för apparaten.




## <a name="prepare-for-agentless-vmware-migration"></a>Förbered dig för agentlös VMware-migrering

Granska kraven för [agentless migrering](server-migrate-overview.md) av virtuella datorer för VMware.

1. [Granska](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) VMware-serverkrav.
2. [Granska de behörigheter](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) som Azure Migrate behöver för att komma åt vCenter Server.
3. [Granska](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) VMware virtuella datorer krav.
4. [Granska](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) azure migrate-installationskraven.
5. Observera kraven för [url-åtkomst](migrate-appliance.md#url-access) och [portåtkomst.](migrate-support-matrix-vmware-migration.md#agentless-ports)

## <a name="prepare-for-agent-based-vmware-migration"></a>Förbered för agentbaserad VMware-migrering

Granska kraven för [agentbaserad migrering](server-migrate-overview.md) av virtuella datorer med VMware.

1. [Granska](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware-serverkrav.
2. [Granska behörigheterna](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate måste komma åt vCenter Server.
2. [Granska](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) VMware-virtuella datorer, inklusive installation av mobilitetstjänsten på varje virtuell dator som du vill migrera.
3. Agentbaserad migrering använder en replikeringsverktyget:
    - [Granska](migrate-replication-appliance.md#appliance-requirements) distributionskraven för replikeringsverktyget.
    - [Läs alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på apparaten.
    - Granska kraven för [URL-](migrate-replication-appliance.md#url-access) och [portåtkomst](migrate-replication-appliance.md#port-access) för replikeringsverktyget.
    
## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera Azure-behörigheter.
> * Förberedd VMware för bedömning och migrering.


Fortsätt till den andra självstudien för att konfigurera ett Azure Migrate-projekt och utvärdera virtuella virtuella datorer för VMware för migrering till Azure.

> [!div class="nextstepaction"]
> [Utvärdera virtuella datorer med VMware](./tutorial-assess-vmware.md)
