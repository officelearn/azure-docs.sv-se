---
title: Förbereda virtuella VMware-datorer för bedömning och migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du förbereder för bedömning och migrering av lokala virtuella VMware-datorer till Azure med Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840349"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Förbereda virtuella VMware-datorer för bedömning och migrering till Azure

Den här artikeln beskriver hur du förbereder för bedömning och migrering av lokala virtuella VMware-datorer till Azure, med hjälp av [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV). 


Den här självstudien är först i en serie som visar hur du utvärdera och migrera virtuella VMware-datorer. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbereda Azure. Ställ in behörigheter för Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbereda lokala VMware-servrar och virtuella datorer för utvärdering av virtuella datorer.
> * Förbereda lokala VMware-servrar och virtuella datorer för VM-migrering.

> [!NOTE]
> Självstudiekurser visar den enklaste distribution sökvägen för ett scenario så att du snabbt kan ställa in proof-of-concept. Använd standardalternativen där det är möjligt självstudier och visas inte alla möjliga inställningar och sökvägar. Granska detaljerade förklaringar för VMware-bedömning och migrering detaljerade anvisningar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Du behöver dessa Azure-behörigheter:

- Azure-kontot måste ha behörighet att skapa ett Azure Migrate-projekt för bedömning och migrering. 
- För bedömning och migrering av virtuella VMware-datorer utan agenter körs Azure Migrate en förenklad installation som identifierar virtuella datorer och skickar data för VM-metadata och prestanda till Azure Migrate. I Azure behöver du behörighet för att registrera Azure Migrate-installationen.
- Om du vill migrera virtuella VMware-datorer med hjälp av Azure Migrate-servermigrering skapar Azure Migrate ett Nyckelvalv i resursgruppen, som ska hantera åtkomstnycklar till lagringskontot replikering i din prenumeration. Om du vill skapa valvet behöver du behörigheter för tilldelning på resursgruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter att skapa projekt

1. Ange prenumerationen i Azure-portalen och välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter.
3. Du bör ha **deltagare** eller **ägare** behörigheter.
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare av din prenumeration.
    - Om du inte är prenumerationsägaren, tillsammans med ägare att tilldela rollen.

### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörighet för att registrera installationen

Om du distribuerar Azure Migrate-installation för att utvärdera eller köra en agentlös migrering av virtuella datorer, måste du registrera den.

- Under installationen registrering skapar Azure Migrate två Azure Active Directory (Azure AD)-appar som unikt identifierar installationen
    - Den första appen kommunicerar med Azure Migrate tjänstslutpunkter.
    - Den andra appen har åtkomst till ett Azure Key Vault skapas under registreringen för att lagra Azure AD app info och appliance konfigurationsinställningar.
- Du kan tilldela behörigheter för Azure Migrate för att skapa de här Azure AD-appar med någon av följande metoder:
    - En klient/global administratör kan ge behörigheter till användare i klienten, skapa och registrera Azure AD-appar.
    - En klient/global administratör kan tilldela rollen programutvecklare (som har behörigheter) till kontot.

Det är värt som:

- Apparna har inte några andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheter när installationen har konfigurerats. 


#### <a name="grant-account-permissions"></a>Bevilja behörighet

Klient/global administratör kan bevilja behörigheter på följande sätt

1. I Azure AD-klient/globala administratören bör gå till **Azure Active Directory** > **användare** > **användarinställningar**.
2. Administratören bör ange **appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känsliga. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Tilldela programutvecklare roll 

Klient/global administratör kan tilldela rollen programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Tilldela behörigheter för tilldelning

Tilldela behörigheter för tilldelning på resursgruppen där Azure Migrate-projektet finns, enligt följande:

1. I resursgrupp i Azure portal, Välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter.

    - Att köra server-utvärdering **deltagare** behörigheter är tillräckligt.
    - Om du vill köra utan Agent servermigrering, bör du ha **ägare** (eller **deltagare** och **administratör för användaråtkomst**) behörigheter.

3. Om du inte har behörigheterna som krävs, begär du dem från resursgruppägare. 



## <a name="prepare-for-vmware-vm-assessment"></a>Förbereda för VMware-VM-utvärdering

För att förbereda för VMware-VM-utvärdering, måste du kontrollera Hyper-V-värd och inställningarna för virtuella datorer och kontrollera inställningarna för distribution av installationen.

### <a name="verify-vmware-settings"></a>Kontrollera inställningarna för VMware

1. [Kontrollera](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements) VMware-serverkraven för VM-utvärdering.
2. [Se till att](migrate-support-matrix-vmware.md#assessment-port-requirements) att nödvändiga portar är öppna på vCenter-servrar.


### <a name="set-up-an-account-for-assessment"></a>Konfigurera ett konto för utvärderingen

Azure Migrate ha åtkomst till vCenter-servern för att identifiera virtuella datorer för bedömning och migrering utan agenter. För utvärdering behöver du ett skrivskyddat konto för vCenter-servern.

Om du använder en URL-baserad firewall.proxy Tillåt åtkomst till de nödvändiga [Azure URL: er](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Se till att proxyn löser eventuella CNAME-poster har tagits emot vid Leta upp de URL: er.


### <a name="verify-appliance-settings-for-assessment"></a>Kontrollera inställningarna för installationen för utvärdering

Förbereda för distribution av installationen innan du konfigurerar Azure Migrate installationer och början utvärdering i nästa självstudie.

1. Kontrollera [Kontrollera](migrate-support-matrix-vmware.md#assessment-appliance-requirements) krav för att installera Azure Migrate-installation i VMware.
2. [Granska](migrate-support-matrix-vmware.md#assessment-url-access-requirements) de URL: er i Azure som installationen ska kunna komma åt.
3. Granska de data som installationen samlar in under identifiering och utvärdering.
4. [Obs](migrate-support-matrix-vmware.md#assessment-port-requirements) port behörigheter som krävs för installationen.
5. Du distribuerar Azure Migrate-installation som en VMware VM med hjälp av OVA-filen. Kontrollera att ditt konto har behörighet att skapa en virtuell dator med en OVA-filen på vCenter-servern.


## <a name="prepare-for-agentless-vmware-migration"></a>Förbereda för migrering utan agenter VMware

Granska kraven för migrering av virtuella VMware-datorer utan agenter.

1. [Granska](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) VMware-serverkraven för migrering utan agenter.
2. Konfigurera ett konto för att få åtkomst till vCenter-servern med den [nödvändiga behörigheter](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) för migrering utan agenter.
3. [Obs](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) kraven för virtuella VMware-datorer som du vill migrera till Azure med hjälp av migrering utan agenter.
4. [Granska](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) installation krav för migrering utan agenter.]
5. Obs installation [URL-åtkomst](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) och [port åtkomst](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) krav för migrering utan agenter.


## <a name="prepare-for-agent-based-vmware-migration"></a>Förbereda för agentbaserad VMware-migrering

Granska kraven för agentbaserad migrering av virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware-serverkraven för migrering utan agenter. 
2. Konfigurera ett konto för att få åtkomst till vCenter-servern med den [nödvändiga behörigheter](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) för migrering utan agenter.
3. [Obs](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) kraven för virtuella VMware-datorer som du vill migrera till Azure med hjälp av agentbaserad migrering, inklusive installation av Mobilitetstjänsten tjänsten på varje virtuell dator som du vill migrera.
4. Obs [URL-åtkomst](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Granska [port åtkomst](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) krav för den mobilitetstjänstversion som körs på varje virtuell dator och för konfigurationsservern Azure Migrate.

## <a name="next-steps"></a>Nästa steg

I den här kursen för du göra följande:
 
> [!div class="checklist"] 
> * Konfigurera Azure-behörigheter.
> * Förberedd VMware för bedömning och migrering.


Fortsätt till andra självstudier för att konfigurera ett Azure Migrate-projekt och utvärdera virtuella VMware-datorer för migrering till Azure.

> [!div class="nextstepaction"] 
> [Utvärdera virtuella VMware-datorer](./tutorial-migrate-vmware.md) 

