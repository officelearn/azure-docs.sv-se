---
title: Förbered virtuella Hyper-V-datorer för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder utvärderingen/migreringen av virtuella Hyper-V-datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 8ecb886b5d5cd9d6811788043b924880b4c366c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928929"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Förbered för utvärdering och migrering av virtuella Hyper-V-datorer till Azure

Den här artikeln hjälper dig att förbereda för utvärdering och migrering av lokala virtuella Hyper-V-datorer till Azure med hjälp av [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool)och [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool).


Den här självstudien är den första i en serie som visar hur du kan utvärdera och migrera virtuella Hyper-V-datorer till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered Azure för att arbeta med Azure Migrate.
> * Förbered för att utvärdera virtuella Hyper-V-datorer.
> * Förbered för migrering av virtuella Hyper-V-datorer 

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

Tabellen sammanfattar de uppgifter som du måste utföra i Azure. Anvisningarna följer tabellen.

**Uppgift** | **Information** | **Behörigheter**
--- | --- | ---
**Skapa ett Azure Migrate-projekt** | Ett Azure Migrate projekt är en central plats för att dirigera och hantera utvärderingar och migreringar med Azure Migrate verktyg, Microsoft-verktyg och erbjudanden från tredje part. | Ditt Azure-konto behöver deltagar-eller ägar behörigheter i resurs gruppen där projektet finns.
**Registrera apparat** | Azure Migrate använder en förenklad Azure Migrate-apparat för att identifiera och utvärdera virtuella Hyper-V-datorer. [Läs mer](migrate-appliance-architecture.md#appliance-registration). | För att registrera installationen behöver ditt Azure-konto deltagar-eller ägar behörigheter för Azure-prenumerationen.
**Skapa Azure AD-app** | När du registrerar installationen skapar Azure Migrate en Azure Active Directory (Azure AD)-app som används för kommunikation mellan de agenter som körs på enheten och Azure Migrate. | Ditt Azure-konto måste ha behörighet att skapa Azure AD-appar.
**Skapa en Key Vault-lösning** | Key Vault skapas som en del av installations registreringen och används för hantering av det certifikat som laddats ned på enheten under konfigurationen.| För att Azure Migrate ska kunna skapa Key Vault måste ditt Azure-konto ha deltagar behörighet för resurs gruppen där Azure Migrate-projektet finns.
**Skapa en virtuell dator** | Du måste ha behörighet att skapa en virtuell dator i resurs gruppen och det virtuella nätverket och skriva till en Azure-hanterad disk. | Du måste ha rollen virtuell dator deltagare i Azure-kontot.


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

Kontrol lera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Tilldela behörigheter för att skapa Azure AD-appar

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


### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela rollen virtuell dator deltagare till kontot så att du har behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriv till en Azure-hanterad disk. 


### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokala datorer replikeras till Azure Managed disks. När du växlar över till Azure för migrering skapas virtuella Azure-datorer från dessa hanterade diskar och ansluts till det Azure-nätverk som du har konfigurerat.


## <a name="prepare-for-assessment"></a>Förbered för utvärdering

Du kan förbereda Hyper-V för VM-bedömning manuellt eller med ett konfigurations skript. Dessa är förberedelse stegen. Om du förbereder med ett skript konfigureras dessa automatiskt.

**Steg** | **Skript** | **Manuell**
--- | --- | ---
**Kontrol lera krav för Hyper-V-värden** | Skript kontrollerar att värden kör en version av Hyper-V som stöds och Hyper-V-rollen.<br/><br/> Aktiverar WinRM-tjänsten och öppnar portarna 5985 (HTTP) och 5986 (HTTPS) på värden (krävs för metadata-insamling). | Kontrol lera [kraven för Hyper-V-värden](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) för Server bedömning.<br/><br/> Se till att de [nödvändiga portarna](migrate-support-matrix-hyper-v.md#port-access) är öppna på Hyper-V-värdar.
**Verifiera PowerShell-version** | Kontrollerar att du kör skriptet på en PowerShell-version som stöds. | Kontrol lera att du kör PowerShell version 4,0 eller senare på Hyper-V-värden.
**Skapa ett konto** | Verifierar att du (användaren som kör skriptet) har administratörs behörighet för Hyper-V-värden.<br/><br/>  Gör att du kan skapa ett lokalt användar konto (inte administratör) som Azure Migrate tjänsten använder för att kommunicera med Hyper-V-värden. Det här användar kontot har lagts till i dessa grupper på värden:<br/><br/> – Fjärrhantering av användare<br/><br/> – Hyper-V-administratörer<br/><br/>– Användare av prestanda övervakning | Konfigurera en domän eller ett lokalt användar konto med administratörs behörighet på Hyper-V-värdarna/-klustret.<br/><br/> -Du behöver ett enda konto för alla värdar och kluster som du vill inkludera i identifieringen.<br/><br/> -Kontot kan vara ett lokalt konto eller ett domän konto. Vi rekommenderar att den har administratörs behörighet för Hyper-V-värdar eller-kluster.<br/><br/> Alternativt, om du inte vill tilldela administratörs behörighet krävs följande behörigheter: fjärrhanterings användare; Hyper-V-administratörer; Användare av prestanda övervakning.
**Aktivera PowerShell-fjärrkommunikation** | Aktiverar PowerShell-fjärrkommunikation på värden, så att Azure Migrate-installationen kan köra PowerShell-kommandon på värden via en WinRM-anslutning.| Om du vill konfigurera, öppnar du en PowerShell-konsol som administratör på varje värd och kör det här kommandot:<br/><br/>``` Enable-PSRemoting -force ```
**Konfigurera Hyper-V integrerings tjänster** | Kontrollerar att integrerings tjänsterna för Hyper-V är aktiverade på alla virtuella datorer som hanteras av värden. |  [Aktivera Hyper-V integrations tjänster](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) på varje virtuell dator.<br/><br/> Om du kör Windows Server 2003 följer du [dessa anvisningar](prepare-windows-server-2003-migration.md).
**Delegera autentiseringsuppgifter om VM-diskar finns på fjärr-SMB-resurser** | Skriptet delegerar autentiseringsuppgifter. | [Aktivera CredSSP](#enable-credssp-to-delegate-credentials) för att delegera autentiseringsuppgifter.

### <a name="run-the-script"></a>Kör skriptet

Kör skriptet på följande sätt:

1. Kontrol lera att du har PowerShell version 4,0 eller senare installerad på Hyper-V-värden.
2. Hämta skriptet från [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skriptet har signerats kryptografiskt av Microsoft.
3. Verifiera skript integriteten med hjälp av antingen MD5-eller SHA256-hash-filer. Värdena för hashtagg anges nedan. Kör det här kommandot för att generera hashen för skriptet:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exempel på användning:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. När du har verifierat skript integriteten kör du skriptet på varje Hyper-V-värd med följande PowerShell-kommando:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Värden för hashtagg

Hash-värden är:

| **Hash** | **Värde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Aktivera CredSSP för att delegera autentiseringsuppgifter

Om värden har virtuella datorer med diskar som finns på SMB-resurser, slutför du det här steget på värden.

- Du kan köra det här kommandot via fjärr anslutning på alla Hyper-V-värdar.
- Om du lägger till nya noder i ett kluster läggs de automatiskt till för identifiering, men du måste aktivera CredSSP manuellt på de nya noderna om det behövs.

Aktivera enligt följande:

1. Identifiera Hyper-V-värdar som kör virtuella Hyper-V-datorer med diskar på SMB-resurser.
2. Kör följande kommando på varje identifierad Hyper-V-värd:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

När du konfigurerar installationen slutför du installationen av CredSSP genom att [Aktivera den på enheten](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Detta beskrivs i nästa självstudie i den här serien.


## <a name="prepare-for-appliance-deployment"></a>Förbereda för distribution av installationer

Innan du konfigurerar Azure Migrate-installationen och påbörjar utvärderingen i nästa självstudie förbereder du installationen av enheten.

1. [Kontrol lera](migrate-appliance.md#appliance---hyper-v) krav för produkten.
2. Granska de Azure-URL: er som krävs för att få åtkomst till molnet [offentliga](migrate-appliance.md#public-cloud-urls) och [myndigheter](migrate-appliance.md#government-cloud-urls) . Om du använder en URL-baserad brand vägg eller proxy, se till att den tillåter åtkomst till de nödvändiga URL: erna.
3. Granska de data som installeras av enheten under identifiering och utvärdering.
4. [Granska](migrate-appliance.md#collected-data---hyper-v) port åtkomst kraven för enheten.


## <a name="prepare-for-hyper-v-migration"></a>Förbered för Hyper-V-migrering

1. [Granska](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Krav för Hyper-V-värden för migrering och de Azure-URL: er som Hyper-V-värdar och-kluster behöver åtkomst för VM-migrering.
2. [Granska](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) kraven för virtuella Hyper-V-datorer som du vill migrera till Azure.
3. Vissa ändringar krävs på virtuella datorer innan du migrerar dem till Azure.
    - Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
    - Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.



## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera behörigheter för Azure-kontot.
> * För beredda Hyper-V-värdar och virtuella datorer för utvärdering och migrering.
> * För beredd för distribution av Azure Migrates enheten.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate-projekt, distribuera installationen och identifiera och utvärdera virtuella Hyper-V-datorer för migrering till Azure.

> [!div class="nextstepaction"]
> [Utvärdera virtuella Hyper-V-datorer](./tutorial-assess-hyper-v.md)
