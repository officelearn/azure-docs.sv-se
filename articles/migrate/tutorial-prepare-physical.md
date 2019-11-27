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
ms.openlocfilehash: 363549662a17a87513c8426347909142ee405cae
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196389"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Förbereda för utvärdering och migrering av fysiska servrar till Azure

Den här artikeln beskriver hur du förbereder för utvärdering av lokala fysiska servrar med [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 

Den här självstudien är den första i en serie som visar hur du bedömer fysiska servrar med Azure Migrate. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure. Konfigurera behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered lokala fysiska servrar för Server utvärdering.


> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen för att utvärdera fysiska servrar.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

### <a name="azure-permissions"></a>Azure-behörigheter

Du måste ange behörigheter för Azure Migrate distribution.

- Behörigheter för ditt Azure-konto för att skapa ett Azure Migrate-projekt.
- Behörigheter för ditt konto för att registrera Azure Migrate-enheten. Enheten används för identifiering och migrering av Hyper-V. Under installationen av produkten skapar Azure Migrate två Azure Active Directory-appar (Azure AD) som unikt identifierar enheten:
    - Den första appen kommunicerar med Azure Migrate tjänst slut punkter.
    - Den andra appen får åtkomst till en Azure Key Vault som skapas under registreringen, för att lagra konfigurations inställningar för Azure AD-appen.



### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

Kontrol lera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.


### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera produkten

Du kan tilldela behörigheter för Azure Migrate för att skapa Azure AD-appar som skapas under registreringen av produkten med någon av följande metoder:

- En klient/global-administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

Det är värt att notera att:

- Apparna har inte någon annan åtkomst behörighet för prenumerationen förutom de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheterna när installationen har kon figurer ATS.


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

1. Kontrol lera [krav för fysisk server](migrate-support-matrix-physical.md#assessment-physical-server-requirements) för Server bedömning.
2. Se till att de [nödvändiga portarna](migrate-support-matrix-physical.md#assessment-port-requirements) är öppna på fysiska servrar.


### <a name="verify-appliance-settings"></a>Verifiera inställningar för enheten

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Kontrol lera](migrate-support-matrix-physical.md#assessment-appliance-requirements) krav för produkten.
2. [Granska](migrate-support-matrix-physical.md#assessment-appliance-url-access) de Azure-URL: er som krävs för att komma åt installations programmet.
3. Granska de data som installeras av enheten under identifiering och utvärdering.
4. [Antecknings](migrate-support-matrix-physical.md#assessment-port-requirements) portens åtkomst krav för produkten.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Konfigurera ett konto för identifiering av fysiska servrar

Azure Migrate behöver behörighet att identifiera lokala servrar.

- **Windows:** Konfigurera ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Användar kontot måste läggas till i följande grupper:-fjärrhantering användare-prestanda övervakare användare-prestanda loggar användare
- **Linux:** Du behöver ett rot konto på de Linux-servrar som du vill identifiera.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera behörigheter för Azure-kontot.
> * För beredda fysiska servrar för utvärdering.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate projekt och utvärdera fysiska servrar för migrering till Azure

> [!div class="nextstepaction"]
> [Utvärdera fysiska servrar](./tutorial-assess-physical.md)
