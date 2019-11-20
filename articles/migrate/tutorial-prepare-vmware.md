---
title: Förbered virtuella VMware-datorer för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av virtuella VMware-datorer med Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cc1eb4c4fce1398365145b2f3d63db984635d667
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196234"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Förbered virtuella VMware-datorer för utvärdering och migrering till Azure

Den här artikeln hjälper dig att förbereda för utvärdering och/eller migrering av lokala virtuella VMware-datorer till Azure med hjälp av [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).


Den här självstudien är den första i en serie som visar hur du kan utvärdera och migrera virtuella VMware-datorer. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered VMware för VM-utvärdering.
> * Förbered VMware för migrering av virtuella datorer.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De är användbara när du lär dig hur du konfigurerar en distribution och som ett snabbt koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen för VMware-utvärdering och migrering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Du behöver dessa behörigheter.

**Aktivitet** | **Behörigheter**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto måste ha behörighet att skapa ett projekt.
**Registrera Azure Migrates apparaten** | Azure Migrate använder en förenklad Azure Migrate-apparat för att utvärdera virtuella VMware-datorer med Azure Migrate Server utvärdering och för att köra en [agent lös migrering](server-migrate-overview.md) av virtuella VMware-datorer med Azure Migrate Server-migrering. Den här installationen identifierar virtuella datorer och skickar VM-metadata och prestanda data till Azure Migrate.<br/><br/>Under registreringen skapar Azure Migrate två Azure Active Directory-appar (Azure AD) som unikt identifierar installationen och behöver behörighet att skapa dessa appar.<br/> – Den första appen kommunicerar med Azure Migrate tjänstens slut punkter.<br/> -Den andra appen får åtkomst till en Azure Key Vault som skapas under registreringen för att lagra konfigurations inställningar för Azure AD-appen.
**Skapa en Key Vault** | Om du vill migrera virtuella VMware-datorer med Azure Migrate Server-migrering skapar Azure Migrate en Key Vault för att hantera åtkomst nycklar till replikeringens lagrings konto i din prenumeration. Om du vill skapa valvet behöver du Roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera produkten

För att registrera installationen kan du tilldela behörigheter för Azure Migrate för att skapa Azure AD-appar under installationen av produkten. Behörigheterna kan tilldelas med någon av följande metoder:

- En klient/global-administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

> [!NOTE]
> - Apparna har inte någon annan åtkomst behörighet för prenumerationen förutom de som beskrivs ovan.
> - Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheterna när installationen har kon figurer ATS.


#### <a name="grant-account-permissions"></a>Bevilja konto behörigheter

Klient organisationen/den globala administratören kan bevilja behörigheter enligt följande

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory** > **användare** > **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**. Detta är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare

Klient organisationen/den globala administratören kan tilldela rollen programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-role-assignment-permissions"></a>Tilldela behörigheter för roll tilldelning

Om du vill aktivera Azure Migrate skapa en Key Vault tilldelar du Roll tilldelnings behörigheter enligt följande:

1. I resurs gruppen i Azure Portal väljer du **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.

    - **Deltagar** behörigheterna räcker för att köra Server utvärderingen.
    - Om du vill köra en programbegränsad Server-migrering, måste du ha behörighet som **ägare** (eller **deltagare** och **användar åtkomst administratör**).

3. Om du inte har de behörigheter som krävs kan du begära dem från resurs gruppens ägare.



## <a name="prepare-for-vmware-vm-assessment"></a>Förbered för utvärdering av virtuella VMware-datorer

För att förbereda för virtuell VMware VM-utvärdering måste du:

- **Verifiera VMware-inställningar**. Kontrol lera att de vCenter Server och de virtuella datorer som du vill migrera uppfyller kraven.
- **Konfigurera ett utvärderings konto**. Azure Migrate behöver åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering.
- **Kontrol lera krav för produkten**. Kontrol lera distributions kraven för den Azure Migrate-utrustning som används för utvärdering.

### <a name="verify-vmware-settings"></a>Verifiera VMware-inställningar

1. [Kontrol lera](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Krav för VMware Server för utvärdering.
2. [Kontrol lera](migrate-support-matrix-vmware.md#assessment-port-requirements) att portarna som du behöver är öppna på vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Konfigurera ett konto för utvärdering

Azure Migrate behöver åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering och agent lös migrering.

- Om du planerar att identifiera program eller visualisera beroenden på ett agent sätt skapar du ett vCenter Server-konto med skrivskyddad åtkomst tillsammans med de behörigheter som har Aktiver ATS för **virtuella datorer** > **gäst åtgärder**.

  ![vCenter Server konto privilegier](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Om du inte planerar att utföra program identifiering och visualisering av beroenden för agenter, ställer du in ett skrivskyddat konto för vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Kontrol lera enhets inställningarna för utvärdering

Kontrol lera kraven för installationen innan du distribuerar installationen.

1. [Kontrol lera](migrate-support-matrix-vmware.md#assessment-appliance-requirements) krav och begränsningar för produkten.
2. Om du använder en URL-baserad brand Väggs-proxy [granskar](migrate-support-matrix-vmware.md#assessment-url-access-requirements) du de Azure-URL: er som installationen behöver för att komma åt. Kontrol lera att proxyn matchar alla CNAME-poster som tagits emot när URL: erna genomsöks.
3. Granska [prestanda data](migrate-appliance.md#collected-performance-data-vmware)] och [metadata](migrate-appliance.md#collected-metadata-vmware) som installationen samlar in under identifiering och bedömning.
4. [Notera](migrate-support-matrix-vmware.md#assessment-port-requirements) de portar som används av enheten.
5. På vCenter Server kontrollerar du att ditt konto har behörighet att skapa en virtuell dator med en ägg fil. Du distribuerar Azure Migrate-apparaten som en virtuell VMware-dator med hjälp av en ägg fil.

Om du använder en URL-baserad brand vägg. proxy kan du ge åtkomst till de nödvändiga [Azure-URL: erna](migrate-support-matrix-vmware.md#assessment-url-access-requirements).




## <a name="prepare-for-agentless-vmware-migration"></a>Förbered för en agent utan VMware-migrering

Granska kraven för migrering utan agent för virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Krav för VMware-servrar.
2. Konfigurera ett konto med de [behörigheter som krävs](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), så att Azure Migrate kan komma åt vCenter Server för migrering utan agent med hjälp av Azure Migrate Server-migrering.
3. [Granska](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) kraven för virtuella VMware-datorer som du vill migrera till Azure med hjälp av en agent lös migrering.
4. [Granska](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) kraven för att använda Azure Migrate-enheten för att migrera utan agent.
5. Observera [URL-åtkomst](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) och [port åtkomst](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) som Azure Migrates utrustning behöver för att migrera utan agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Förbereda för agent-baserad VMware-migrering

Granska kraven för [agent-baserad migrering](server-migrate-overview.md) av virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Krav för VMware-servrar.
2. Konfigurera ett konto med de [behörigheter som krävs](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions). så att Azure Migrate kan komma åt vCenter Server för agent-baserad migrering med hjälp av Azure Migrate Server-migrering.
3. [Granska](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) kraven för virtuella VMware-datorer som du vill migrera till Azure med hjälp av en agent-baserad migrering, inklusive installation av mobilitets tjänsten på varje virtuell dator som du vill migrera.
4. Observera [URL-åtkomst](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Granska [port åtkomst](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) som Azure Migrate komponenter behöver för agent-baserad åtkomst.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera Azure-behörigheter.
> * För beredd VMware för utvärdering och migrering.


Fortsätt till den andra själv studie kursen för att skapa ett Azure Migrate-projekt och utvärdera virtuella VMware-datorer för migrering till Azure.

> [!div class="nextstepaction"]
> [Utvärdera virtuella VMware-datorer](./tutorial-assess-vmware.md)
