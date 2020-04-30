---
title: Förbered virtuella VMware-datorer för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av virtuella VMware-datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677285"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Förbereda virtuella VMware-datorer för utvärdering och migrering till Azure

Den här artikeln hjälper dig att förbereda för utvärdering och/eller migrering av lokala virtuella VMware-datorer till Azure med hjälp av [Azure Migrate](migrate-services-overview.md).



Den här självstudien är den första i en serie som visar hur du kan utvärdera och migrera virtuella VMware-datorer. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered VMware för VM-utvärdering med verktyget Azure Migrate: Server bedömning.
> * Förbered VMware för VM-migrering med verktyget Azure Migrate: Migreringsverktyg för Server. 

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De är användbara när du lär dig hur du konfigurerar en distribution och som ett snabbt koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Du behöver dessa behörigheter för dessa uppgifter i Azure innan du kan utvärdera eller migrera virtuella VMware-datorer.

**Uppgift** | **Information** 
--- | --- 
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver deltagar-eller ägar behörighet för att skapa ett projekt. 
**Registrera resurs leverantörer** | Azure Migrate använder en förenklad Azure Migrate-apparat för att identifiera och utvärdera virtuella VMware-datorer och för att migrera dem till Azure med Azure Migrate: Server utvärdering.<br/><br/> Under produkt registrering registreras resurs leverantörer med den prenumeration som valts i installationen. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Om du vill registrera resurs leverantörer behöver du en deltagar-eller ägar roll för prenumerationen.
**Skapa Azure AD-appar** | När du registrerar enheten skapar Azure Migrate Azure Active Directory (Azure AD)-appar. <br/><br/> – Den första appen används för kommunikation mellan de agenter som körs på produkten och deras respektive tjänster som körs på Azure.<br/><br/> – Den andra appen används exklusivt för att få åtkomst till nyckel valvet som skapats i användarens prenumeration för migrering av virtuella VMware-datorer. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Du måste ha behörighet för att skapa Azure AD-appar (tillgängliga i rollen program utvecklare).
**Skapa en Key Vault-lösning** | Om du vill migrera virtuella VMware-datorer med hjälp av en utan agent, skapar Azure Migrate ett Key Vault för att hantera åtkomst nycklar till replikeringens lagrings konto i din prenumeration.<br/><br/> Om du vill skapa valvet behöver du Roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns.




### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera produkten

För att registrera installationen kan du tilldela behörigheter för Azure Migrate för att skapa Azure AD-appar under installationen av produkten. Behörigheterna kan tilldelas med någon av följande metoder:

- **Bevilja behörigheter**: en klient/global administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
- **Tilldela rollen program utvecklare**: en klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

> [!NOTE]
> - Apparna har inte någon annan åtkomst behörighet för prenumerationen förutom de som beskrivs ovan.
> - Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheterna när installationen har kon figurer ATS.


#### <a name="grant-account-permissions"></a>Bevilja konto behörigheter

Gör så här om du vill att klient organisationen/den globala administratören ska bevilja behörigheter:

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory** > **användares** > **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**. Detta är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare

Alternativt kan klient organisationen/den globala administratören tilldela rollen program utvecklare till ett konto. [Lär dig mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) om att tilldela en roll.

### <a name="assign-permissions-to-create-a-key-vault"></a>Tilldela behörigheter för att skapa en Key Vault

Om du vill aktivera Azure Migrate skapa en Key Vault tilldelar du behörighet enligt följande:

1. I resurs gruppen i Azure Portal väljer du **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.

    - **Deltagar** behörigheterna räcker för att köra Server utvärderingen.
    - Om du vill köra en programbegränsad Server-migrering, måste du ha behörighet som **ägare** (eller **deltagare** och **användar åtkomst administratör**).

3. Om du inte har de behörigheter som krävs kan du begära dem från resurs gruppens ägare.



## <a name="prepare-for-vmware-vm-assessment"></a>Förbered för utvärdering av virtuella VMware-datorer

För att förbereda för virtuell VMware VM-utvärdering måste du:

- **Verifiera VMware-inställningar**. Kontrol lera att de vCenter Server och de virtuella datorer som du vill migrera uppfyller kraven.
- Konfigurera **ett konto för utvärdering**. Azure Migrate använder det här kontot för att komma åt vCenter Server för att identifiera virtuella datorer för utvärdering.
- **Kontrol lera krav för produkten**. Kontrol lera distributions kraven för Azure Migrate-installationen innan du distribuerar den.

### <a name="verify-vmware-settings"></a>Verifiera VMware-inställningar

1. [Kontrol lera](migrate-support-matrix-vmware.md#vmware-requirements) Krav för VMware Server för utvärdering.
2. [Kontrol lera](migrate-support-matrix-vmware.md#port-access) att portarna som du behöver är öppna på vCenter Server.
3. På vCenter Server kontrollerar du att ditt konto har behörighet att skapa en virtuell dator med en ägg fil. Du distribuerar Azure Migrate-apparaten som en virtuell VMware-dator med hjälp av en ägg fil.


### <a name="set-up-an-account-for-assessment"></a>Konfigurera ett konto för utvärdering

Azure Migrate behöver åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering och agent lös migrering.

- Om du planerar att identifiera program eller visualisera beroenden på ett agent sätt skapar du ett vCenter Server-konto med skrivskyddad åtkomst tillsammans med de behörigheter som har Aktiver ATS för**gäst åtgärder**för **virtuella datorer** > .

  ![vCenter Server konto privilegier](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Om du inte planerar att utföra program identifiering och visualisering av beroenden för agenter, ställer du in ett skrivskyddat konto för vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Kontrol lera enhets inställningarna för utvärdering

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Verifiera](migrate-appliance.md#appliance---vmware) Azure Migrate installations krav.
2. Granska de Azure-URL: er som krävs för att få åtkomst till molnet [offentliga](migrate-appliance.md#public-cloud-urls) och [myndigheter](migrate-appliance.md#government-cloud-urls) .
3. [Granska data](migrate-appliance.md#collected-data---vmware) som samlas in under identifiering och bedömning.
4. [Antecknings](migrate-support-matrix-vmware.md#port-access) portens åtkomst krav för produkten.




## <a name="prepare-for-agentless-vmware-migration"></a>Förbered för en agent utan VMware-migrering

Granska kraven för [migrering utan agent](server-migrate-overview.md) för virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Krav för VMware-servrar.
2. [Granska de behörigheter](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) som Azure Migrate behöver för att få åtkomst till vCenter Server.
3. [Granska](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Krav för virtuella VMware-datorer.
4. [Granska](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) kraven för Azure Migrates utrustning.
5. Observera URL-åtkomst som krävs för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.
6. Granska [port åtkomst](migrate-support-matrix-vmware-migration.md#agentless-ports) krav.

## <a name="prepare-for-agent-based-vmware-migration"></a>Förbereda för agent-baserad VMware-migrering

Granska kraven för [agent-baserad migrering](server-migrate-overview.md) av virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Krav för VMware-servrar.
2. [Granska behörigheterna](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate behöver åtkomst till vCenter Server.
2. [Granska](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Krav för virtuella VMware-datorer, inklusive installation av mobilitets tjänsten på varje virtuell dator som du vill migrera.
3. En agent-baserad migrering använder en replikeringsfil:
    - [Granska](migrate-replication-appliance.md#appliance-requirements) distributions kraven för replikerings enheten.
    - [Granska alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på-enheten.
    - Observera URL-åtkomst som krävs för [offentliga](migrate-replication-appliance.md#url-access) och [offentliga](migrate-replication-appliance.md#azure-government-url-access) moln.
    - Granska [port åtkomst](migrate-replication-appliance.md#port-access) kraven för replikerings enheten.
    
## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera Azure-behörigheter.
> * För beredd VMware för utvärdering och migrering.


Fortsätt till den andra själv studie kursen för att skapa ett Azure Migrate-projekt och utvärdera virtuella VMware-datorer för migrering till Azure.

> [!div class="nextstepaction"]
> [Utvärdera virtuella VMware-datorer](./tutorial-assess-vmware.md)
