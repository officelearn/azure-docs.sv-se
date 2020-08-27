---
title: Förbered virtuella VMware-datorer för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av virtuella VMware-datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8d4d6ac1149c397442a8ca7dd01f46f04ffc89b4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927314"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Förbereda virtuella VMware-datorer för utvärdering och migrering till Azure

Den här artikeln hjälper dig att förbereda för utvärdering och migrering av lokala virtuella VMware-datorer till Azure med hjälp av [Azure Migrate](migrate-services-overview.md).

Den här självstudien är den första i en serie som visar hur du kan utvärdera och migrera virtuella VMware-datorer. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered för att utvärdera virtuella VMware-datorer med verktyget Azure Migrate: Server bedömning.
> * Förbered migreringen av virtuella VMware-datorer med verktyget Azure Migrate: Migreringsverktyg. 

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De är användbara som en snabb proof-of-Concept. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Tabellen sammanfattar de uppgifter som du måste utföra i Azure. Instruktioner för varje aktivitet följer tabellen.

**Uppgift** | **Information** | **Behörigheter**
--- | --- | ---
**Skapa ett Azure Migrate-projekt** | Ett Azure Migrate projekt är en central plats för att dirigera och hantera utvärderingar och migreringar med Azure Migrate verktyg, Microsoft-verktyg och erbjudanden från tredje part. | Ditt Azure-konto behöver deltagar-eller ägar behörigheter i resurs gruppen där projektet finns.
**Registrera apparat** | Azure Migrate använder en förenklad Azure Migrate-enhet för att identifiera virtuella datorer, för att utvärdera dem med Server bedömnings verktyget och för att migrera dem med hjälp av återställning utan agent med Migreringsverktyg för Server. [Läs mer](migrate-appliance-architecture.md#appliance-registration) om registrering. | För att registrera installationen behöver ditt Azure-konto deltagar-eller ägar behörigheter för Azure-prenumerationen.
**Skapa Azure AD-appar** | När du registrerar en installation skapar Azure Migrate Azure AD-appar med två Active Directory (Azure AD). <br/><br/> – Den första appen används för kommunikation mellan de agenter som körs på enheten och Azure Migrate. <br/><br/> – Den andra appen används exklusivt för att få åtkomst till nyckel valvet som skapats i användarens prenumeration för migrering av virtuella VMware-datorer.   | Ditt Azure-konto behöver de här [behörigheterna](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware#assign-permissions-to-create-azure-ad-apps) för att skapa Azure AD-appar.
**Skapa en Key Vault-lösning** | – Den första Key Vault skapas som en del av installations registreringen och används för hantering av det certifikat som laddats ned på enheten under konfigurationen. <br/><br/> – Om du vill migrera virtuella VMware-datorer med hjälp av utan agent skapar Azure Migrate ytterligare Key Vault för att hantera åtkomst nycklar till replikeringssystemet i din prenumeration.| Om du vill tillåta Azure Migrate att skapa Key Vault ställer du in behörigheter (ägare eller deltagare och administratör för användar åtkomst) på resurs gruppen där Azure Migrate-projektet finns.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Tilldela behörigheter för att skapa Azure AD-appar

För att registrera installationen måste ditt Azure-konto ha behörighet att skapa Azure AD-appar. Tilldela behörigheterna med någon av följande metoder:

- **Metod 1: bevilja behörighet till kontot**: en klient/global administratör kan bevilja behörigheter till användar konton i klienten för att skapa och registrera Azure AD-appar.
- **Metod 2: tilldela en roll med behörighet till ett användar konto**: en klient/global administratör kan tilldela rollen programutvecklare (som har de behörigheter som krävs) till användar kontot.

> [!NOTE]
> Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheterna när installationen har kon figurer ATS.


#### <a name="method-1-grant-permissions-to-the-account"></a>Metod 1: bevilja behörighet till kontot

Bevilja behörighet till kontot på följande sätt:

1. Se till att du är en klient organisation eller global administratör. I Azure AD navigerar du sedan till **Azure Active Directory**  >  **Users**  >  **användar inställningar**för användare.
2. Ange **Appregistreringar** till **Ja**. Detta är en standardinställning som inte är känslig. [Läs mer](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Metod 2: tilldela rollen program utvecklare

Alternativt kan klient organisationen/den globala administratören tilldela rollen program utvecklare till ett konto. [Lär dig mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) om att tilldela en roll.

### <a name="assign-permissions-to-create-a-key-vault"></a>Tilldela behörigheter för att skapa en Key Vault

Om du vill aktivera Azure Migrate skapa en Key Vault tilldelar du behörighet enligt följande:

1. I resurs gruppen i Azure Portal väljer du **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.

    - **Deltagar** behörigheterna räcker för att köra Server utvärderingen.
    - Om du vill köra en programbegränsad Server-migrering, måste du ha behörighet som **ägare** (eller **deltagare** och **användar åtkomst administratör**).

3. Om du inte har de behörigheter som krävs kan du begära dem från resurs gruppens ägare.

## <a name="prepare-for-assessment"></a>Förbered för utvärdering

För att förbereda för virtuell VMware VM-utvärdering måste du:

1. **Verifiera VMware-inställningar**. Kontrol lera att de vCenter Server och de virtuella datorer som du vill migrera uppfyller kraven.
2. **Konfigurera behörigheter för utvärdering**. Azure Migrate använder ett vCenter-konto för att komma åt vCenter Server, för att identifiera och utvärdera virtuella datorer.
3. **Kontrol lera krav för produkten**. Kontrol lera distributions kraven för Azure Migrate-installationen innan du distribuerar den i nästa självstudie.

### <a name="verify-vmware-settings"></a>Verifiera VMware-inställningar

1. [Kontrol lera VMware-kraven](migrate-support-matrix-vmware.md#vmware-requirements) för utvärdering.
2. [Kontrol lera](migrate-support-matrix-vmware.md#port-access-requirements) att portarna som du behöver är öppna på vCenter Server.
3. På vCenter Server kontrollerar du att ditt konto har behörighet att skapa en virtuell dator med en ägg fil. Detta krävs när du distribuerar Azure Migrate-installationen som en virtuell VMware-dator med hjälp av en ägg fil.
4. Vissa ändringar krävs på virtuella datorer innan du migrerar dem till Azure.

    - För vissa operativ system gör Azure Migrate dessa ändringar automatiskt. 
    - Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
    - Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.


### <a name="set-up-permissions-for-assessment"></a>Konfigurera behörigheter för utvärdering

Azure Migrate behöver åtkomst till vCenter Server så att Azure Migrate-installationen kan identifiera virtuella datorer för utvärdering och agent lös migrering. Konfigurera ett konto enligt följande:

1. Öppna **administrations**  >  **åtkomst till**  >  **SSO-användare och-grupper**i vSphere-webbklienten.
2. I **användare**klickar du på ikonen **ny användare** .
3. Ange den nya användar informationen.
4. Välj behörigheter i enlighet med tabell värden.

    **Funktion** | **Konto behörigheter**
    --- | ---
    [Utvärdera virtuella datorer](tutorial-assess-vmware.md) | För utvärdering måste kontot ha skrivskyddad åtkomst.
    [Identifiera VM-appar, roller och funktioner](how-to-discover-applications.md) | Om du vill använda app-Discovery måste det skrivskyddade kontot som används för utvärdering vara aktiverat för **virtuella datorer**  >  **gäst åtgärder**.
    [Analysera beroenden på virtuella datorer (utan agent)](how-to-create-group-machine-dependencies-agentless.md) | Om du vill analysera beroenden måste det skrivskyddade kontot som används för utvärdering vara aktiverat för **virtuella datorer**  >  **gäst åtgärder**.
    
> [!NOTE]
> Läs [den här artikeln](set-discovery-scope.md#assign-a-role-for-assessment)om du vill begränsa identifieringen av virtuella datorer för utvärdering till en bestämd omfattning.

### <a name="verify-appliance-settings-for-assessment"></a>Kontrol lera enhets inställningarna för utvärdering

I [Nästa självstudie](tutorial-assess-vmware.md) ställer du in Azure Migrate-installationen och påbörjar utvärderingen. Innan du gör det granskar du kraven för installationen enligt följande: 

1. [Granska kraven](migrate-appliance.md#appliance---vmware) för att distribuera Azure Migrate-enheten.
2. Granska de Azure-URL: er som krävs för att få åtkomst till de [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) molnen.
3. [Notera](migrate-support-matrix-vmware.md#port-access-requirements) vilka portar som används av enheten.
4. [Granska de data](migrate-appliance.md#collected-data---vmware) som enheten samlar in under identifiering och bedömning.

## <a name="prepare-for-agentless-vmware-migration"></a>Förbered för en agent utan VMware-migrering

Du kan migrera virtuella VMware-datorer med hjälp av en agent utan agent [eller en agent-baserad migrering](server-migrate-overview.md). I det här avsnittet sammanfattas kraven för migrering utan agent.

1. [Bestäm](server-migrate-overview.md#compare-migration-methods) om du vill använda migrering utan agent.
2. [Granska](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) hypervisor-krav för datorer som du vill migrera.
3. [Granska](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) kraven för virtuella VMware-datorer som du vill migrera med hjälp av en agent lös migrering.
4. [Granska](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) Azure Migrate installations krav för migrering utan agent.
5. Observera URL-åtkomst som krävs för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.
6. Granska [port åtkomst](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) krav.
7. Konfigurera behörigheter för migrering utan agent, enligt beskrivningen i nästa procedur.


### <a name="assign-permissions-to-an-account"></a>Tilldela behörigheter till ett konto

Azure Migrate-enheten ansluter till vCenter Server för att identifiera och migrera virtuella datorer med en agent lös migrering. Du kan tilldela de behörigheter som krävs för installationen av ett användar konto, eller skapa en roll med behörigheterna och tilldela rollen till ett användar konto.

1. Öppna **administrations**  >  **åtkomst till**  >  **SSO-användare och-grupper**i vSphere-webbklienten.
2. I **användare**klickar du på ikonen **ny användare** .
3. Ange den nya användar informationen.
4. Tilldela [dessa behörigheter](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>Skapa en roll och tilldela till ett konto

Du kan också skapa ett konto. Skapa sedan en roll och tilldela den till kontot enligt följande:

1. Logga in på sfärens webb klient som vCenter Server administratör
2. Välj vCenter Server-instansen >  **skapa roll**.
3. Ange ett rollnamn, till exempel <em>Azure_Migrate</em>, och tilldela de [behörigheter som krävs](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) för rollen.

    ![vCenter Server konto privilegier](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Skapa ett konto och tilldela rollen till kontot.

> [!NOTE]
> Läs [den här artikeln](set-discovery-scope.md#assign-a-role-for-agentless-migration)om du vill begränsa VM-identifiering för agent lös migrering till en bestämd omfattning.



## <a name="prepare-for-agent-based-vmware-migration"></a>Förbereda för agent-baserad VMware-migrering

Du kan migrera virtuella VMware-datorer med hjälp av en agent utan agent [eller en agent-baserad migrering](server-migrate-overview.md). I det här avsnittet sammanfattas kraven för agent-baserad migrering.

1. [Bestäm](server-migrate-overview.md#compare-migration-methods) om du vill använda agent-baserad migrering.
1. [Granska](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) hypervisor-krav för datorer som du vill migrera.
2. [Granska](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Krav för virtuella VMware-datorer, inklusive installation av mobilitets tjänsten på varje virtuell dator som du vill migrera.
3. En agent-baserad migrering använder en replikeringsfil:
    - [Granska](migrate-replication-appliance.md#appliance-requirements) distributions kraven för replikerings enheten.
    - [Granska alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på-enheten.
    - Observera URL-åtkomst som krävs för [offentliga](migrate-replication-appliance.md#url-access) och [offentliga](migrate-replication-appliance.md#azure-government-url-access) moln.
    - Granska [port åtkomst](migrate-replication-appliance.md#port-access) kraven för replikerings enheten.
4. Vissa ändringar krävs på virtuella datorer innan du kan migrera dem till Azure. Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.



## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera Azure-behörigheter.
> * För beredd VMware för utvärdering och migrering.


Fortsätt till den andra själv studie kursen för att skapa ett Azure Migrate-projekt och utvärdera virtuella VMware-datorer för migrering till Azure.

> [!div class="nextstepaction"]
> [Utvärdera virtuella VMware-datorer](./tutorial-assess-vmware.md)
