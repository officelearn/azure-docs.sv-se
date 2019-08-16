---
title: Förbered virtuella VMware-datorer för utvärdering och migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du förbereder för utvärdering och migrering av lokala virtuella VMware-datorer till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 16c0354466d5c2a1207873a1e83e209da9339705
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509993"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Förbered virtuella VMware-datorer för utvärdering och migrering till Azure

Den här artikeln beskriver hur du förbereder för utvärdering och migrering av lokala virtuella VMware-datorer till Azure med hjälp av [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 


Den här självstudien är den första i en serie som visar hur du kan utvärdera och migrera virtuella VMware-datorer. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure. Konfigurera behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered lokala VMware-servrar och virtuella datorer för utvärdering av virtuella datorer.
> * Förbered lokala VMware-servrar och virtuella datorer för VM-migrering.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i så här går du igenom hur TOS för VMware-utvärdering och migrering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Du behöver dessa Azure-behörigheter:

- Ditt Azure-konto måste ha behörighet att skapa ett Azure Migrate-projekt för utvärdering och migrering. 
- För utvärdering och agent lös migrering av virtuella VMware-datorer kör Azure Migrate en förenklad installation som identifierar virtuella datorer och skickar VM-metadata och prestanda data till Azure Migrate. I Azure måste du ha behörighet att registrera Azure Migrate-enheten.
- För att migrera virtuella VMware-datorer med hjälp av Azure Migrate Server-migrering skapar Azure Migrate en Key Vault i resurs gruppen för att hantera åtkomst nycklar till replikeringens lagrings konto i din prenumeration. Om du vill skapa valvet behöver du Roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera produkten

Om du distribuerar Azure Migrate-installationen för att utvärdera eller köra en agent lös migrering av virtuella datorer måste du registrera den.

- Vid registrering av produkten skapar Azure Migrate två Azure Active Directory-appar (Azure AD) som unikt identifierar installationen
    - Den första appen kommunicerar med Azure Migrate tjänst slut punkter.
    - Den andra appen får åtkomst till en Azure Key Vault som skapas under registreringen för att lagra konfigurations inställningar för Azure AD-appen.
- Du kan tilldela behörigheter för Azure Migrate att skapa de här Azure AD-apparna med någon av följande metoder:
    - En klient/global-administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
    - En klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

Det är värt att notera att:

- Apparna har inte någon annan åtkomst behörighet för prenumerationen förutom de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheterna när installationen har kon figurer ATS. 


#### <a name="grant-account-permissions"></a>Bevilja konto behörigheter

Klient organisationen/den globala administratören kan bevilja behörigheter enligt följande

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory** > **användares** > **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Detta är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare 

Klient organisationen/den globala administratören kan tilldela rollen programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Tilldela behörigheter för roll tilldelning

Tilldela roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns, enligt följande:

1. I resurs gruppen i Azure Portal väljer du **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.

    - Deltagar behörigheterna räcker för att köra Server utvärderingen.
    - Om du vill köra en programbegränsad Server-migrering, måste du ha behörighet som **ägare** (eller **deltagare** och **användar åtkomst administratör**).

3. Om du inte har de behörigheter som krävs kan du begära dem från resurs gruppens ägare. 



## <a name="prepare-for-vmware-vm-assessment"></a>Förbered för utvärdering av virtuella VMware-datorer

För att förbereda för virtuell VMware VM-utvärdering måste du verifiera inställningarna för VMware-värd och VM och kontrol lera inställningarna för installationen av enheten.

### <a name="verify-vmware-settings"></a>Verifiera VMware-inställningar

1. [Verifiera](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Krav för VMware-servrar för VM-bedömning.
2. [Kontrol lera](migrate-support-matrix-vmware.md#assessment-port-requirements) att de portar som krävs är öppna på vCenter-servrar.


### <a name="set-up-an-account-for-assessment"></a>Konfigurera ett konto för utvärdering

Azure Migrate behöver åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering och agent lös migrering. För utvärdering behöver du ett skrivskyddat konto för vCenter Server.

Om du använder en URL-baserad brand vägg. proxy kan du ge åtkomst till de nödvändiga [Azure-URL: erna](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Kontrol lera att proxyn matchar alla CNAME-poster som tagits emot när URL: erna genomsöks.


### <a name="verify-appliance-settings-for-assessment"></a>Kontrol lera enhets inställningarna för utvärdering

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Kontrol lera](migrate-support-matrix-vmware.md#assessment-appliance-requirements) kraven för att konfigurera Azure Migrate-installationen i VMware.
2. [Granska](migrate-support-matrix-vmware.md#assessment-url-access-requirements) de Azure-URL: er som krävs för att komma åt installations programmet.
3. Granska de data som installeras av enheten under identifiering och utvärdering.
4. [](migrate-support-matrix-vmware.md#assessment-port-requirements) Antecknings portens åtkomst krav för produkten.
5. Du distribuerar Azure Migrate-installationen som en virtuell VMware-dator med en ägg fil. På vCenter Server kontrollerar du att ditt konto har behörighet att skapa en virtuell dator med en ägg fil.


## <a name="prepare-for-agentless-vmware-migration"></a>Förbered för en agent utan VMware-migrering

Granska kraven för migrering utan agent för virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) VMware Server-krav för migrering utan agent.
2. Konfigurera ett konto för att få åtkomst till vCenter Server med de [behörigheter som krävs](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) för migrering utan agent.
3. [Observera](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) kraven för virtuella VMware-datorer som du vill migrera till Azure med hjälp av en agent lös migrering.
4. [Granska](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) installations kraven för migrering utan agent.
5. Antecknings utrustning [URL åtkomst](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) och [port åtkomst](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) krav för migrering utan agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Förbereda för agent-baserad VMware-migrering

Granska kraven för agent-baserad migrering av virtuella VMware-datorer.

1. [Granska](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware Server-krav för migrering utan agent. 
2. Konfigurera ett konto för att få åtkomst till vCenter Server med de [behörigheter som krävs](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) för migrering utan agent.
3. [Observera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) kraven för virtuella VMware-datorer som du vill migrera till Azure med hjälp av en agent-baserad migrering, inklusive installation av mobilitets tjänsten på varje virtuell dator som du vill migrera.
4. Observera [URL-åtkomst](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Granska [port åtkomst](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) kraven för mobilitets tjänsten som körs på varje virtuell dator och för Azure Migrate konfigurations servern.

## <a name="next-steps"></a>Nästa steg

I den här kursen för du göra följande:
 
> [!div class="checklist"] 
> * Konfigurera Azure-behörigheter.
> * För beredd VMware för utvärdering och migrering.


Fortsätt till den andra själv studie kursen för att skapa ett Azure Migrate-projekt och utvärdera virtuella VMware-datorer för migrering till Azure.

> [!div class="nextstepaction"] 
> [Utvärdera virtuella VMware-datorer](./tutorial-migrate-vmware.md) 

