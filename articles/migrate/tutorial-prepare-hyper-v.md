---
title: Förbereda virtuella virtuella hyper-v-datorer för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder för utvärdering/migrering av virtuella hyper-virtuella datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 03/31/2020
ms.custom: mvc
ms.openlocfilehash: d14ae4282afb610d025d08419a69c6d10c2f1d08
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436237"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Förbereda för bedömning och migrering av virtuella hyper-virtuella datorer till Azure

I den hÃ¤r artikeln beskrivs hur du förbereder sig fÃ¤r utvärdering av lokala virtuella virtuella fÃ¤r hyper-V-fÃ¤r med Azure Migrate:Server Assessment(migrate-services-overview.md#azure-migrate-server-assessment-tool) och migrering av virtuella hyper-V-datorer med [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).


Den här självstudien är den första i en serie som visar hur du bedömer och migrerar virtuella hyper-virtuella datorer till Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förbered Azure. Ställ in behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered lokala Hyper-V-värdar och virtuella datorer för serverutvärdering. Du kan förbereda med hjälp av ett konfigurationsskript eller manuellt.
> * Förbered för distribution av Azure Migrate-enheten. Apparaten används för att upptäcka och bedöma lokala virtuella datorer.
> * Förbered lokala Hyper-V-värdar och virtuella datorer för servermigrering.


> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktioner för Hyper-V-bedömning och migrering.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

### <a name="azure-permissions"></a>Azure-behörigheter

Du måste ställa in behörigheter för Azure Migrate-distribution.

**Aktivitet** | **Detaljer** 
--- | --- 
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver Contributer- eller ägarbehörigheter för att skapa ett projekt. | 
**Registrera resursleverantörer** | Azure Migrate använder en lätt Azure Migrate-installation för att identifiera och bedöma virtuella hyper-v-datorer med Azure Migrate Server Assessment.<br/><br/> Vid registrering av apparater registreras resursleverantörer med det abonnemang som valts i apparaten. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Om du vill registrera resursleverantörerna behöver du en deltagar- eller ägarroll i prenumerationen.
**Skapa Azure AD-app** | När du registrerar installationen skapar Azure Migrate en Azure Active Directory-app (Azure AD) som används för kommunikation mellan agenter som körs på installationen med sina respektive tjänster som körs på Azure. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Du behöver behörigheter för att skapa Azure AD-appar (tillgängliga i rollen Programutvecklare).



### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

Kontrollera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure-portalen och välj **Åtkomstkontroll (IAM)**.
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter.
3. Du bör ha **behörigheten Deltagare** eller **Ägare.**
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerationsägare arbetar du med ägaren för att tilldela rollen.


### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera apparaten

Du kan tilldela behörigheter för Azure Migrate för att skapa Azure AD-appen under registrering av installationer med någon av följande metoder:

- En klient/global administratör kan bevilja behörigheter till användare i klienten för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen Programutvecklare (som har behörigheterna) till kontot.

> [!NOTE]
> - Appen har inga andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
> - Du behöver bara dessa behörigheter när du registrerar en ny apparat. Du kan ta bort behörigheterna när apparaten har konfigurerats.


#### <a name="grant-account-permissions"></a>Bevilja kontobehörigheter

Klienten/den globala administratören kan bevilja behörigheter enligt följande:

1. I Azure AD bör klienten/den globala administratören navigera till**Användarinställningar för** **Azure Active Directory** > **Users** > .
2. Administratören ska ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Tilldela rollen Programutvecklare

Klienten/den globala administratören kan tilldela rollen Programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Förbered Hyper-V för bedömning

Du kan förbereda Hyper-V för vm-bedömning manuellt eller använda ett konfigurationsskript. Förberedelsesteg är följande:
- [Verifiera](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Hyper-V-värdinställningar och se till att de [nödvändiga portarna](migrate-support-matrix-hyper-v.md#port-access) är öppna på Hyper-V-värdar.
- Konfigurera PowerShell-omvärdering på varje värd, så att Azure Migrate-enheten kan köra PowerShell-kommandon på värden, via en WinRM-anslutning.
- Delegera autentiseringsuppgifter om VM-diskar finns på fjärr-SMB-resurser.
- Konfigurera ett konto som enheten ska använda för att identifiera virtuella datorer på Hyper-V-värdar.
- Konfigurera Hyper-V-integrationstjänster på varje virtuell dator som du vill identifiera och utvärdera. Standardinställningarna när du aktiverar Integrationstjänster är tillräckliga för Azure Migrate.

    ![Aktivera integrationstjänster](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Förbered med ett skript

Skriptet gör följande:

- Kontrollerar att du kör skriptet på en PowerShell-version som stöds.
- Verifierar att du (användaren som kör skriptet) har administratörsbehörighet på Hyper-V-värden.
- Gör att du kan skapa ett lokalt användarkonto (inte administratör) som Azure Migrate-tjänsten använder för att kommunicera med Hyper-V-värden. Det här användarkontot läggs till i dessa grupper på värden:
    - Fjärrhanteringsanvändare
    - Hyper-V-administratörer
    - Användare av prestandaövervakaren
- Kontrollerar att värden kör en version av Hyper-V som stöds och hyper-V-rollen.
- Aktiverar WinRM-tjänsten och öppnar portarna 5985 (HTTP) och 5986 (HTTPS) på värden (behövs för metadatasamling).
- Aktiverar PowerShell-ommotning på värden.
- Kontrollerar att Hyper-V-integrationstjänsterna är aktiverat på alla virtuella datorer som hanteras av värden.
- Aktiverar CredSSP på värden om det behövs.

Kör skriptet på följande sätt:

1. Kontrollera att du har PowerShell version 4.0 eller senare installerat på Hyper-V-värden.
2. Hämta skriptet från [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skriptet är kryptografiskt signerat av Microsoft.
3. Verifiera skriptintegriteten med md5- eller SHA256-hash-filer. Hashtag-värdena finns nedan. Kör det här kommandot om du vill generera hash för skriptet:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exempel på användning:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    När du har validerat skriptintegriteten kör du skriptet på varje Hyper-V-värd med det här PowerShell-kommandot:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hashtag-värden

Hash-värden är:

| **Hash** | **Värde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256 (SHA256)** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Förbered manuellt

Följ procedurerna i det här avsnittet för att förbereda Hyper-V manuellt i stället för att använda skriptet.

### <a name="verify-powershell-version"></a>Verifiera PowerShell-version

Kontrollera att du har PowerShell version 4.0 eller senare installerat på Hyper-V-värden.



### <a name="set-up-an-account-for-vm-discovery"></a>Konfigurera ett konto för vm-identifiering

Azure Migrate behöver behörigheter för att identifiera lokala virtuella datorer.

- Konfigurera en domän eller ett lokalt användarkonto med administratörsbehörighet för Hyper-V-värdar/-klustret.

    - Du behöver ett enda konto för alla värdar och kluster som du vill inkludera i identifieringen.
    - Kontot kan vara ett lokalt konto eller domänkonto. Vi rekommenderar att den har administratörsbehörighet för Hyper-V-värdar eller kluster.
    - Om du inte vill tilldela administratörsbehörigheter behövs följande behörigheter:
        - Fjärrhanteringsanvändare
        - Hyper-V-administratörer
        - Användare av prestandaövervakaren

### <a name="verify-hyper-v-host-settings"></a>Verifiera värdinställningar för Hyper-V

1. Verifiera [Hyper-V-värdkrav](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) för serverutvärdering.
2. Kontrollera att de [portar som krävs](migrate-support-matrix-hyper-v.md#port-access) är öppna på Hyper-V-värdar.

### <a name="enable-powershell-remoting-on-hosts"></a>Aktivera PowerShell-ommotning på värdar

Konfigurera PowerShell-ommotning på varje värd, enligt följande:

1. Öppna en PowerShell-konsol som administratör på varje värd.
2. Kör följande kommando:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Aktivera integrationstjänster på virtuella datorer

Integrationstjänster bör aktiveras på varje virtuell dator så att Azure Migrate kan samla in operativsysteminformation på den virtuella datorn.

Aktivera [Hyper-V-integrationstjänster](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) på varje virtuell dator på virtuella datorer som du vill identifiera och utvärdera.


### <a name="enable-credssp-on-hosts"></a>Aktivera CredSSP på värdar

Om värden har virtuella datorer med diskar finns på SMB-resurser slutför du det här steget på värden.

- Du kan fjärrköra det här kommandot på alla Hyper-V-värdar.
- Om du lägger till nya värdnoder i ett kluster läggs de automatiskt till för identifiering, men du måste aktivera CredSSP manuellt på de nya noderna om det behövs.

Aktivera enligt följande:

1. Identifiera Hyper-V-värdar som kör virtuella hyper-virtuella datorer med diskar på SMB-resurser.
2. Kör följande kommando på varje identifierad Hyper-V-värd:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

När du ställer in apparaten är du klar med installationen av CredSSP genom [att aktivera den på apparaten](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Detta beskrivs i nästa handledning i den här serien.


## <a name="prepare-for-appliance-deployment"></a>Förbered för distribution av apparater

Innan du konfigurerar Azure Migrate-enheten och börjar utvärderingen i nästa självstudiekurs förbereder du för distribution av enheter.

1. [Kontrollera](migrate-appliance.md#appliance---hyper-v) apparatens krav.
2. [Granska](migrate-appliance.md#url-access) de Azure-url:er som enheten behöver komma åt.
3. Granska de data som apparaten samlar in under identifiering och bedömning.
4. [Observera](migrate-appliance.md#collected-data---hyper-v) tillträdeskrav för porten för apparaten.


## <a name="prepare-for-hyper-v-migration"></a>Förbered hyper-V-migrering

1. [Granska](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Hyper-V-värdkrav för migrering och Azure-url:er som Hyper-V-värdar och kluster behöver åtkomst till för vm-migrering.
2. [Granska](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) kraven för virtuella hyper-virtuella datorer som du vill migrera till Azure.


## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera Azure-kontobehörigheter.
> * Förberedda Hyper-V-värdar och virtuella datorer för bedömning och migrering.
> * Förberedd för distribution av Azure Migrate-enheten.

Fortsätt till nästa självstudiekurs för att skapa ett Azure Migrate-projekt, distribuera installationen och identifiera och bedöma virtuella hyper-virtuella datorer för migrering till Azure.

> [!div class="nextstepaction"]
> [Bedöma virtuella virtuella hyper-virtuella datorer](./tutorial-assess-hyper-v.md)
