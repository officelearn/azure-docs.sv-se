---
title: Förbered virtuella Hyper-V-datorer för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av virtuella Hyper-V-datorer med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f93528e2a35661f8a233aea476a958a079d7cd59
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196265"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Förbered för utvärdering och migrering av virtuella Hyper-V-datorer till Azure

Den här artikeln beskriver hur du förbereder för utvärdering och migrering av lokala virtuella Hyper-V-datorer till Azure med [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).

Den här självstudien är den första i en serie som visar hur du kan utvärdera och migrera virtuella Hyper-V-datorer till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure. Konfigurera behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbereda lokala Hyper-V-värdar och virtuella datorer för Server utvärdering.
> * Förbered lokala Hyper-V-värdar och virtuella datorer för Server migrering.


> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen för Hyper-V-utvärdering och migrering.


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


## <a name="prepare-for-hyper-v-assessment"></a>Förbered för Hyper-V-utvärdering

Gör så här för att förbereda för Hyper-V-utvärdering:

1. Verifiera inställningarna för Hyper-V-värden.
2. Konfigurera PowerShell-fjärrkommunikation på varje värd, så att Azure Migrate-installationen kan köra PowerShell-kommandon på värden via en WinRM-anslutning.
3. Om VM-diskar finns i fjärr-SMB-lagring krävs delegering av autentiseringsuppgifter.
    - Aktivera CredSSP-delegering så att Azure Migrate-installationen kan fungera som klienten och delegera autentiseringsuppgifter till en värd.
    - Du gör det möjligt för varje värd att fungera som ett ombud för enheten, enligt beskrivningen nedan.
    - När du senare konfigurerar installationen aktiverar du delegering på enheten.
4. Granska kraven på installationen och den URL/port-åtkomst som krävs för installationen av enheten.
5. Konfigurera ett konto som ska användas av enheten för att identifiera virtuella datorer.
6. Konfigurera Hyper-V integrerings tjänster på varje virtuell dator som du vill identifiera och utvärdera.


Du kan konfigurera dessa inställningar manuellt med hjälp av anvisningarna nedan. Du kan också köra konfigurations skriptet för Hyper-V-krav.

### <a name="hyper-v-prerequisites-configuration-script"></a>Konfigurations skript för Hyper-V-krav

Skriptet verifierar Hyper-V-värdar och konfigurerar de inställningar du behöver för att identifiera och utvärdera virtuella Hyper-V-datorer. Så här gör du:

- Kontrollerar att du kör skriptet på en PowerShell-version som stöds.
- Verifierar att du (användaren som kör skriptet) har administratörs behörighet för Hyper-V-värden.
- Gör att du kan skapa ett lokalt användar konto (inte administratör) som används för tjänsten Azure Migrate för att kommunicera med Hyper-V-värden. Det här användar kontot har lagts till i dessa grupper på värden:
    - Fjärrstyrda användare
    - Hyper-V-administratörer
    - Användare av prestanda övervakning
- Kontrollerar att värden kör en version av Hyper-V som stöds och Hyper-V-rollen.
- Aktiverar WinRM-tjänsten och öppnar portarna 5985 (HTTP) och 5986 (HTTPS) på värden (krävs för metadata-insamling).
- Aktiverar PowerShell-fjärrkommunikation på värden.
- Kontrollerar att integrerings tjänsten för Hyper-V är aktive rad på alla virtuella datorer som hanteras av värden.
- Aktiverar CredSSP på värden vid behov.

Kör skriptet på följande sätt:

1. Kontrol lera att du har PowerShell version 4,0 eller senare installerad på Hyper-V-värden.
2. Hämta skriptet från [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skriptet har signerats kryptografiskt av Microsoft.
3. Verifiera skript integriteten med hjälp av antingen MD5-eller SHA256-hash-filer. Värdena för hashtagg anges nedan. Kör det här kommandot för att generera hashen för skriptet:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exempel på användning:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  När du har verifierat skript integriteten kör du skriptet på varje Hyper-V-värd med följande PowerShell-kommando:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Värden för hashtagg

Hash-värden är:

| **Beräkna** | **Värde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### <a name="verify-hyper-v-host-settings"></a>Verifiera inställningarna för Hyper-V-värden

1. Kontrol lera [kraven för Hyper-V-värden](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) för Server bedömning.
2. Se till att de [nödvändiga portarna](migrate-support-matrix-hyper-v.md#assessment-port-requirements) är öppna på Hyper-V-värdar.

### <a name="enable-powershell-remoting-on-hosts"></a>Aktivera PowerShell-fjärrkommunikation på värdar

Konfigurera PowerShell-fjärrkommunikation på varje värd enligt följande:

1. Öppna en PowerShell-konsol som administratör på varje värd.
2. Kör följande kommando:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Aktivera CredSSP på värdar

Om värden har virtuella datorer med diskar som finns på SMB-resurser, slutför du det här steget på värden.

- Du kan köra det här kommandot via fjärr anslutning på alla Hyper-V-värdar.
- Om du lägger till nya noder i ett kluster läggs de automatiskt till för identifiering, men du måste aktivera CredSSP manuellt på de nya noderna om det behövs.

Aktivera enligt följande:

1. Identifiera Hyper-V-värdar som kör virtuella Hyper-V-datorer med diskar på SMB-resurser.
2. Kör följande kommando på varje identifierad Hyper-V-värd:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

När du konfigurerar den installation som du har slutfört konfigurerar du CredSSP genom att [aktivera det på enheten](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Detta beskrivs i nästa självstudie i den här serien.


### <a name="verify-appliance-settings"></a>Verifiera inställningar för enheten

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Kontrol lera](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) krav för produkten.
2. [Granska](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) de Azure-URL: er som krävs för att komma åt installations programmet.
3. Granska de data som installeras av enheten under identifiering och utvärdering.
4. [Antecknings](migrate-support-matrix-hyper-v.md#assessment-port-requirements) portens åtkomst krav för produkten.


### <a name="set-up-an-account-for-vm-discovery"></a>Konfigurera ett konto för VM-identifiering

Azure Migrate behöver behörighet för att identifiera lokala virtuella datorer.

- Konfigurera en domän eller ett lokalt användar konto med administratörs behörighet på Hyper-V-värdarna/-klustret.

    - Du behöver ett enda konto för alla värdar och kluster som du vill inkludera i identifieringen.
    - Kontot kan vara ett lokalt konto eller ett domän konto. Vi rekommenderar att den har administratörs behörighet för Hyper-V-värdar eller-kluster.
    - Alternativt, om du inte vill tilldela administratörs behörighet, krävs följande behörigheter:
        - Fjärrstyrda användare
        - Hyper-V-administratörer
        - Användare av prestanda övervakning

### <a name="enable-integration-services-on-vms"></a>Aktivera integrerings tjänster på virtuella datorer

Integrerings tjänsterna måste vara aktiverade på varje virtuell dator så att Azure Migrate kan avbilda information om operativ systemet på den virtuella datorn.

På virtuella datorer som du vill identifiera och utvärdera aktiverar du [integrerings tjänster för Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) på varje virtuell dator.

## <a name="prepare-for-hyper-v-migration"></a>Förbered för Hyper-V-migrering

1. [Granska](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Krav för Hyper-V-värd för migrering.
2. [Granska](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) kraven för virtuella Hyper-V-datorer som du vill migrera till Azure.
3. [Observera](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) de Azure-URL: er som Hyper-V-värdar och-kluster behöver åtkomst för VM-migrering.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera behörigheter för Azure-kontot.
> * För beredda Hyper-V-värdar och virtuella datorer för utvärdering och migrering.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate-projekt och utvärdera virtuella Hyper-V-datorer för migrering till Azure

> [!div class="nextstepaction"]
> [Utvärdera virtuella Hyper-V-datorer](./tutorial-assess-hyper-v.md)
