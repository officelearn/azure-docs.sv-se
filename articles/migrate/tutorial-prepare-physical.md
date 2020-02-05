---
title: Förbereda fysiska servrar för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av fysiska servrar med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a487e0c9d565993b45f7e271af23a622e61389ad
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990835"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Förbereda för utvärdering och migrering av fysiska servrar till Azure

Den här artikeln beskriver hur du förbereder för utvärdering av lokala fysiska servrar med [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 

Den här självstudien är den första i en serie som visar hur du bedömer fysiska servrar med Azure Migrate. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Förbered Azure. Konfigurera behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered lokala fysiska servrar för Server utvärdering.


> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen för att utvärdera fysiska servrar.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

### <a name="azure-permissions"></a>Azure-behörigheter

Du måste ange behörigheter för Azure Migrate distribution.

**Aktivitet** | **Behörigheter**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto måste ha behörighet att skapa ett projekt.
**Registrera Azure Migrates apparaten** | Azure Migrate använder en förenklad Azure Migrate-apparat för att identifiera och utvärdera fysiska servrar med Azure Migrate Server bedömning. Den här enheten identifierar servrar och skickar sina metadata och prestanda data till Azure Migrate.<br/><br/>Under installation av utrustning registreras följande registrerade leverantörer med den prenumeration som valts i enheten-Microsoft. OffAzure, Microsoft. Migrate och Microsoft. nyckel-valv. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Om du vill registrera resurs leverantörer behöver du en deltagar-eller ägar roll för prenumerationen.<br/><br/> Som en del av onboarding skapar Azure Migrate en Azure Active Directory (Azure AD)-app:<br/> AAD-appen används för kommunikation (autentisering och auktorisering) mellan de agenter som körs på enheten med deras respektive tjänster som körs på Azure. Den här appen har inte behörighet att göra ARM-anrop eller RBAC-åtkomst på någon resurs.



### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

Kontrol lera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
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

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory** > **användare** > **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Detta är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare

Klient organisationen/den globala administratören kan tilldela rollen programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Förbered för fysisk server-utvärdering

För att förbereda för fysisk server-utvärdering måste du kontrol lera inställningarna för den fysiska servern och kontrol lera inställningarna för installationen av enheten:

### <a name="verify-physical-server-settings"></a>Verifiera fysiska Server inställningar

1. Kontrol lera [krav för fysisk server](migrate-support-matrix-physical.md#physical-server-requirements) för Server bedömning.
2. Se till att de [nödvändiga portarna](migrate-support-matrix-physical.md#port-access) är öppna på fysiska servrar.


### <a name="verify-appliance-settings"></a>Verifiera inställningar för enheten

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Kontrol lera](migrate-appliance.md#appliance---physical) installations kraven för fysiska servrar.
2. [Granska](migrate-appliance.md#url-access) de Azure-URL: er som krävs för att komma åt installations programmet.
3. [Granska](migrate-appliance.md#collected-data---vmware) att installationen kommer att samlas in under identifiering och utvärdering.
4. Den fysiska server utvärderingen för [antecknings](migrate-support-matrix-physical.md#port-access) port åtkomst krav.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Konfigurera ett konto för identifiering av fysiska servrar

Azure Migrate behöver behörighet att identifiera lokala servrar.

- **Windows:** Konfigurera ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Användar kontot måste läggas till i följande grupper:-fjärrhantering användare-prestanda övervakare användare-prestanda loggar användare
- **Linux:** Du behöver ett rot konto på de Linux-servrar som du vill identifiera.

## <a name="prepare-for-physical-server-migration"></a>Förbered för migrering av fysiska servrar

Granska kraven för migrering av fysiska servrar.

- [Granska](migrate-support-matrix-physical-migration.md#physical-server-requirements) krav för fysisk server för migrering.
- Azure Migrate: Server migrering använder en replikeringsfil för fysisk server-migrering:
    - [Granska](migrate-replication-appliance.md#appliance-requirements) distributions kraven för replikeringstjänsten och [alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på-enheten.
    - Granska [URL: en](migrate-replication-appliance.md#url-access) och [port] (Migrate-Replication-installation. MD # port-Access) åtkomst krav för replikerings enheten.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera behörigheter för Azure-kontot.
> * För beredda fysiska servrar för utvärdering.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate projekt och utvärdera fysiska servrar för migrering till Azure

> [!div class="nextstepaction"]
> [Utvärdera fysiska servrar](./tutorial-assess-physical.md)
