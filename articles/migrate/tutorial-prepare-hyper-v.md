---
title: Förbereda Hyper-V-datorer för bedömning och migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du förbereder för bedömning och migrering av Hyper-V-datorer till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840374"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Förbereda för bedömning och migrering av Hyper-V-datorer till Azure

Den här artikeln beskriver hur du förbereder för bedömning och migrering av lokala Hyper-V-datorer till Azure med [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV). 

Den här självstudien är först i en serie som visar hur du utvärdera och migrera Hyper-V-datorer till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbereda Azure. Ställ in behörigheter för Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbereda lokala Hyper-V-värdar och virtuella datorer för server-utvärdering.
> * Förbereda lokala Hyper-V-värdar och virtuella datorer för migrering.


> [!NOTE]
> Självstudiekurser visar den enklaste distribution sökvägen för ett scenario så att du snabbt kan ställa in proof-of-concept. Använd standardalternativen där det är möjligt självstudier och visas inte alla möjliga inställningar och sökvägar. Granska detaljerade förklaringar för Hyper-V-bedömning och migrering detaljerade anvisningar.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

### <a name="azure-permissions"></a>Azure-behörigheter

Behöver du ett par behörighet att distribuera Azure Migrate:

- Azure-kontot måste ha behörighet att skapa ett Azure Migrate-projekt för bedömning och migrering. 
- Azure-kontot måste ha behörighet att registrera Azure Migrate-installationen.
    - För utvärdering av körs Azure Migrate en förenklad installation som identifierar Hyper-V-datorer och skickar VM metadata och prestanda till Azure Migrate.
    - Under installationen registreringen skapar Azure Migrate två Azure Active Directory (Azure AD)-appar som unikt identifierar installationen:
        - Den första appen kommunicerar med Azure Migrate tjänstslutpunkter.
        - Den andra appen har åtkomst till ett Azure Key Vault skapas under registreringen för att lagra Azure AD app info och appliance konfigurationsinställningar.
    - Du kan tilldela behörigheter för Azure Migrate för att skapa de här Azure AD-appar med någon av följande metoder:
        - En klient/global administratör kan ge behörigheter till användare i klienten, skapa och registrera Azure AD-appar.
        - En klient/global administratör kan tilldela rollen programutvecklare (som har behörigheter) till kontot.
    - Det är värt som:
        - Apparna har inte några andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
        - Du behöver bara dessa behörigheter när du registrerar en ny installation. Du kan ta bort behörigheter när installationen har konfigurerats. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter att skapa projekt

Kontrollera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Ange prenumerationen i Azure-portalen och välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter.
3. Du bör ha **deltagare** eller **ägare** behörigheter.
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare av din prenumeration.
    - Om du inte är prenumerationsägaren, tillsammans med ägare att tilldela rollen.


### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörighet för att registrera installationen

Om du distribuerar Azure Migrate-installation för att utvärdera virtuella datorer, måste du registrera den.

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

Klient/global administratör kan bevilja behörigheter på följande sätt:

1. I Azure AD-klient/globala administratören bör gå till **Azure Active Directory** > **användare** > **användarinställningar**.
2. Administratören bör ange **appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känsliga. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Tilldela programutvecklare roll 

Klient/global administratör kan tilldela rollen programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Förbereda för Hyper-V-utvärdering

Om du vill förbereda för Hyper-V-utvärdering, kontrollera Hyper-V-värd och inställningarna för virtuella datorer och kontrollera inställningar för installation, distribution.

### <a name="verify-hyper-v-host-settings"></a>Kontrollera inställningarna för Hyper-V-värd

1. Kontrollera [värdkrav för Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) för server-utvärdering.
2. Kontrollera att den [krävs portar](migrate-support-matrix-hyper-v.md#assessment-port-requirements) är öppna på Hyper-V-värdar.

### <a name="enable-powershell-remoting-on-hosts"></a>Aktivera PowerShell-fjärrkommunikation på värdar

Konfigurera PowerShell-fjärrkommunikation på varje värd enligt följande:

1. På varje värd öppnar du en PowerShell-konsol som administratör.
2. Kör följande kommando:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Aktivera CredSSP på värdar

Om VM-diskar finns på SMB-resurser kan du slutföra det här steget på varje relevant Hyper-V-värd. Det här steget används för att identifiera konfigurationsinformation för Hyper-V-datorer med diskar på SMB-resurser. Om du inte har VM-diskar på SMB-resurser kan du hoppa över steget.

1. Identifiera Hyper-V-värdar som kör Hyper-V-datorer med diskar på SMB-resurser.
2. Kör följande kommando på varje identifierad Hyper-V-värd:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- CredSSP-autentisering kan Hyper-V-värd att delegera autentiseringsuppgifter för Azure Migrate-klientens räkning.
- Du kan köra det här kommandot via fjärranslutning på alla Hyper-V-värdar.
- Om du lägger till nya värdnoder i ett kluster läggs de automatiskt för identifiering, men du måste manuellt Aktivera CredSSP på de nya noderna, om det behövs.

### <a name="verify-appliance-settings"></a>Kontrollera inställningarna för installation

Förbereda för distribution av installationen innan du konfigurerar Azure Migrate installationer och början utvärdering i nästa självstudie.

1. [Kontrollera](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) krav för installation.
2. [Granska](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) de URL: er i Azure som installationen ska kunna komma åt.
3. Granska de data som installationen samlar in under identifiering och utvärdering.
4. [Obs](migrate-support-matrix-hyper-v.md#assessment-port-requirements) port behörigheter som krävs för installationen.


### <a name="set-up-an-account-for-vm-discovery"></a>Konfigurera ett konto för identifiering av VM

Azure Migrate måste ha behörighet att identifiera lokala virtuella datorer.

- Konfigurera en domän eller lokalt konto med administratörsbehörighet på Hyper-V-värdar/kluster.

    - Du behöver ett enda konto för alla värdar och kluster som du vill ta med i identifieringen.
    - Kontot kan vara en lokal eller domänåtkomst konto. Vi rekommenderar att den har administratörsbehörighet på Hyper-V-värdar eller kluster.
    - Om du inte vill tilldela administratörsbehörighet krävs också följande behörigheter:
        - Fjärrhanteringsanvändare
        - Hyper-V-administratörer
        - Användare av prestandaövervakning

### <a name="enable-integration-services-on-vms"></a>Aktivera integreringstjänsterna på virtuella datorer

Integration Services ska aktiveras på varje virtuell dator så att Azure Migrate kan samla in information om operativsystemet på den virtuella datorn.

- På virtuella datorer som du vill identifiera och utvärdera, aktivera [Hyper-V-integreringstjänster](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) på varje virtuell dator. 

## <a name="prepare-for-hyper-v-migration"></a>Förbereda för migrering av Hyper-V

1. [Granska](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) kraven för Hyper-V-värd för migrering.
2. [Granska](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) kraven för Hyper-V-datorer som du vill migrera till Azure.
3. [Obs](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) Azure URL: er till vilken Hyper-V-värdar och kluster behöver åtkomst för VM-migrering.

## <a name="next-steps"></a>Nästa steg

I den här kursen för du göra följande:
 
> [!div class="checklist"] 
> * Ställ in behörigheter för Azure-konto.
> * Förberedd Hyper-V-värdar och virtuella datorer för bedömning och migrering.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate-projekt och utvärdera virtuella Hyper-V-datorer för migrering till Azure

> [!div class="nextstepaction"] 
> [Utvärdera Hyper-V-datorer](./tutorial-assess-hyper-v.md) 
