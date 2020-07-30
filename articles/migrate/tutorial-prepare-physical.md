---
title: Förbereda fysiska servrar för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av fysiska servrar med Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 55e6039e5844c575808210cde7ee348f658b40ec
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420795"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Förbereda för utvärdering och migrering av fysiska servrar till Azure

Den här artikeln beskriver hur du förbereder för utvärdering av lokala fysiska servrar med [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](./migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 

Den här självstudien är den första i en serie som visar hur du bedömer fysiska servrar med Azure Migrate. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure. Konfigurera behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered lokala fysiska servrar för Server utvärdering.


> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen för att utvärdera fysiska servrar.


Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure-for-server-assessment"></a>Förbered Azure för Server-utvärdering

Konfigurera Azure för att arbeta med Azure Migrate. 

**Uppgift** | **Information** 
--- | --- 
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver deltagar-eller ägar behörighet för att skapa ett projekt. 
**Registrera resurs leverantörer (endast utvärdering)** | Azure Migrate använder en förenklad Azure Migrate-apparat för att identifiera och utvärdera datorer med Azure Migrate: Server utvärdering.<br/><br/> Under produkt registrering registreras resurs leverantörer med den prenumeration som valts i installationen. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Om du vill registrera resurs leverantörer behöver du en deltagar-eller ägar roll för prenumerationen.
**Skapa Azure AD-App (endast utvärdering)** | När du registrerar installationen skapar Azure Migrate en Azure Active Directory (Azure AD)-app som används för kommunikation mellan de agenter som körs på enheten med deras respektive tjänster som körs på Azure. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Du måste ha behörighet för att skapa Azure AD-appar (tillgängliga i rollen program utvecklare).


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt 

Kontrol lera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.


### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera produkten 

Du kan tilldela behörigheter för Azure Migrate för att skapa Azure AD-appen under registreringen av produkten med någon av följande metoder:

- En klient/global-administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

> [!NOTE]
> - Appen har inte någon annan åtkomst behörighet för den prenumeration som beskrivs ovan.
> - Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheterna när installationen har kon figurer ATS.


#### <a name="grant-account-permissions"></a>Bevilja konto behörigheter

Klient organisationen/den globala administratören kan bevilja behörigheter enligt följande:

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory**  >  **användares**  >  **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Detta är en standardinställning som inte är känslig. [Läs mer](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare

Klient organisationen/den globala administratören kan tilldela rollen programutvecklare till ett konto. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="prepare-azure-for-physical-server-migration"></a>Förbered Azure för migrering av fysiska servrar

Förbered Azure för att migrera fysiska servrar med hjälp av Server migrering.

**Uppgift** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto måste ha Contributes eller ägar behörigheter för att skapa ett projekt.
**Verifiera behörigheter för ditt Azure-konto** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.
**Skapa ett Azure-nätverk** | Konfigurera ett nätverk i Azure.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.


### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela Azure-kontot rollen virtuell dator deltagare. Detta ger behörighet att:
  - Skapa en virtuell dator i den valda resursgruppen.
  - Skapa en virtuell dator i det valda virtuella nätverket.
  - Skriv till en Azure-hanterad disk. 

### <a name="create-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ett virtuellt Azure-nätverk (VNet). När du replikerar till Azure skapas och ansluts virtuella Azure-datorer till det virtuella Azure-nätverk som du anger när du konfigurerar migrering.


## <a name="prepare-for-physical-server-assessment"></a>Förbered för fysisk server-utvärdering

För att förbereda för fysisk server-utvärdering måste du kontrol lera inställningarna för den fysiska servern och kontrol lera inställningarna för installationen av enheten:

### <a name="verify-physical-server-settings"></a>Verifiera fysiska Server inställningar

1. Kontrol lera [krav för fysisk server](migrate-support-matrix-physical.md#physical-server-requirements) för Server bedömning.
2. Se till att de [nödvändiga portarna](migrate-support-matrix-physical.md#port-access) är öppna på fysiska servrar.


### <a name="verify-appliance-settings"></a>Verifiera inställningar för enheten

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Kontrol lera](migrate-appliance.md#appliance---physical) installations kraven för fysiska servrar.
2. Granska de Azure-URL: er som krävs för att få åtkomst till de [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) molnen.
3. [Granska](migrate-appliance.md#collected-data---vmware) att installationen kommer att samlas in under identifiering och utvärdering.
4. Den fysiska server utvärderingen för [antecknings](migrate-support-matrix-physical.md#port-access) port åtkomst krav.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Konfigurera ett konto för identifiering av fysiska servrar

Azure Migrate behöver behörighet att identifiera lokala servrar.

- **Windows:** Du behöver ett domän konto för att upptäcka domänanslutna servrar och ett lokalt konto för att identifiera datorer som inte är domänanslutna. Användar kontot ska läggas till i dessa grupper: fjärrhanterings användare, prestanda övervaknings användare och användare av prestanda loggar.
- **Linux:** Du behöver ett rot konto på de Linux-servrar som du vill identifiera.

## <a name="prepare-for-physical-server-migration"></a>Förbered för migrering av fysiska servrar

Granska kraven för migrering av fysiska servrar.

> [!NOTE]
> När du migrerar fysiska datorer Azure Migrate: Server migrering använder samma replikeringsprincip som agentbaserade haveri beredskap i Azure Site Recovery-tjänsten, och vissa komponenter delar samma kodbas. En del innehåll kan länkas till Site Recovery-dokumentationen.

1. [Granska](migrate-support-matrix-physical-migration.md#physical-server-requirements) krav för fysisk server för migrering.
2. Azure Migrate: Server migrering använder en replikeringsfil för fysisk server-migrering:
    - [Granska](migrate-replication-appliance.md#appliance-requirements) distributions kraven för replikeringstjänsten och [alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på-enheten.
    - Granska de [Azure-URL: er](migrate-appliance.md#url-access) som krävs för att replikeringssystemet ska kunna komma åt offentliga och offentliga moln.
    - Granska [port] (Migrate-Replication-installation. MD # port-Access) åtkomst krav för replikerings enheten.
3. Vissa ändringar krävs på virtuella datorer innan du migrerar dem till Azure.
    - Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
    - Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.



## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera behörigheter för Azure-kontot.
> * För beredda fysiska servrar för utvärdering.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate projekt och utvärdera fysiska servrar för migrering till Azure

> [!div class="nextstepaction"]
> [Utvärdera fysiska servrar](./tutorial-assess-physical.md)
