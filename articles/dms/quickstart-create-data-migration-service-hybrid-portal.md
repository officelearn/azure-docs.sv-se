---
title: 'Snabbstart: Skapa en hybridlägesinstans med Azure-portal'
titleSuffix: Azure Database Migration Service
description: Använd Azure-portalen för att skapa en instans av Azure Database Migration Service i hybridläge.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: dd3e77610749eb5d146b0c0b7cf9d307fba0dd83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370244"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Snabbstart: Skapa en hybridlägesinstans med Azure-portalen & Azure Database Migration Service

Azure Database Migration Service hybridläge hanterar databasmigreringar med hjälp av en migreringsarbetare som är värd lokalt tillsammans med en instans av Azure Database Migration Service som körs i molnet. Hybridläge är särskilt användbart för scenarier där det saknas anslutning mellan det lokala nätverket och Azure eller om det finns begränsad bandbredd för anslutning från plats till plats.

>[!NOTE]
>För närvarande stöder Azure Database Migration Service som körs i hybridläge SQL Server-migreringar till:
>
>- Azure SQL Database hanterad instans med nära noll driftstopp (online).
>- En enda Azure SQL-databas med viss driftstopp (offline).
>- MongoDb till Azure CosmosDB med nära noll driftstopp (online).
>- MongoDb till Azure CosmosDB med vissa driftstopp (offline).

I den här snabbstarten använder du Azure-portalen för att skapa en instans av Azure Database Migration Service i hybridläge. Efteråt hämtar, installerar och konfigurerar du hybridarbetaren i det lokala nätverket. Under förhandsversionen kan du använda hybridläget för Azure Database Migration Service för att migrera data från en lokal instans av SQL Server till Azure SQL Database.

> [!NOTE]
> Hybridinstallationsprogrammet för Azure Database Migration Service körs på Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 och Windows 10.

> [!IMPORTANT]
> Hybridinstallationsprogrammet för Azure Database Migration Service kräver .NET 4.7.2 eller senare. Information om hur du hittar de senaste versionerna av .NET finns på sidan [Hämta .NET Framework.](https://dotnet.microsoft.com/download/dotnet-framework)

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren, gå till [Microsoft Azure-portalen](https://portal.azure.com/) och logga in genom att ange dina autentiseringsuppgifter.

Standardvyn är instrumentpanelen.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Registrera microsoft.datamigration-resursprovidern innan du skapar din första instans av Azure Database Migration Service.

1. I Azure-portalen väljer du **Prenumerationer**, väljer den prenumeration där du vill skapa instansen av Azure Database Migration Service och väljer sedan **Resursleverantörer**.

    ![Sök resursleverantör](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Skapar en instans av tjänsten

1. Välj +**Skapa en resurs** för att skapa en instans av Azure Database Migration Service.

2. Sök på Marketplace efter "migrering", välj **Azure Database Migration Service**och välj sedan **Skapa**på skärmen Azure Database **Migration Service** .

3. På skärmen **Skapa migreringstjänst**:

    - Välj ett **tjänstnamn** som är minnesvärt och unikt för att identifiera din instans av Azure Database Migration Service.
    - Välj den Azure-**prenumeration** där du vill skapa instansen.
    - Välj en befintlig **resursgrupp** eller skapa en ny.
    - Välj den **plats** som ligger närmast din käll- eller målserver.
    - För **tjänstläge**väljer du **Hybrid (Förhandsgranskning)**.

         ![Skapa migreringstjänst - grunderna](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Välj **Granska + skapa**.

5. På fliken **Granska + skapa** granskar du villkoren, verifierar annan information som tillhandahålls och väljer sedan **Skapa**.

    ![Skapa migreringstjänst - Granska + skapa](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Efter en stund skapas din instans av Azure Database Migration Service i hybridläge och är klar att konfigureras. Azure Database Migration Service-instansen visas som visas i följande bild:

    ![Hybridlägesinstans för Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. När tjänsten har skapats väljer du **Egenskaper**och kopierar sedan värdet som visas i rutan **Resurs-ID,** som du använder för att installera hybridarbetaren för Azure Database Migration Service.

    ![Hybridlägesegenskaper för Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Skapa registrerings-ID för Azure-program

Du måste skapa ett Registrerings-ID för Azure-appar som den lokala hybridarbetaren kan använda för att kommunicera med Azure Database Migration Service i molnet.

1. I Azure-portalen väljer du **Azure Active Directory**, väljer **Appregistreringar**och väljer sedan **Ny registrering**.
2. Ange ett namn för programmet och välj sedan den typ av konton som ska stödjas under Kontotyper som stöds för att ange vem som kan använda programmet under **Kontotyper som stöds.**

    ![Hybridlägesregisterprogram för Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Använd standardvärdena för fälten **Omdirigera URI (valfritt)** och välj sedan **Registrera**.

4. När registreringen av app-ID har slutförts bör du anteckna **programmets (klient)-ID**, som du ska använda när du installerar hybridarbetaren.

5. I Azure-portalen navigerar du till Azure Database Migration Service, väljer **Åtkomstkontroll (IAM)** och väljer sedan **Lägg till rolltilldelning** för att tilldela deltagare åtkomst till app-ID.

    ![Hybridläge för Azure Database Migration Service-tjänst tilldela deltagarroll](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Välj **Deltagare** som roll, tilldela åtkomst till **Azure AD-användare eller tjänstens huvudnamn**och välj sedan namnet på app-ID.

    ![Hybridläge för Azure Database Migration Service tilldela deltagarrollinformation](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Välj **Spara** om du vill spara rolltilldelningen för app-ID:et på Azure Database Migration Service-resursen.

## <a name="download-and-install-the-hybrid-worker"></a>Hämta och installera hybridarbetaren

1. Navigera till din instans av Azure Database Migration Service i Azure-portalen.

2. Under **Inställningar**väljer du **Hybrid**och väljer sedan **Hämta Installer** för att hämta hybridarbetaren.

    ![Hybridarbetstjänsthämtning av Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extrahera ZIP-filen på servern som ska vara värd för hybridarbetaren för Azure Database Migration Service.

    > [!IMPORTANT]
    > Hybridinstallationsprogrammet för Azure Database Migration Service kräver .NET 4.7.2 eller senare. Information om hur du hittar de senaste versionerna av .NET finns på sidan [Hämta .NET Framework.](https://dotnet.microsoft.com/download/dotnet-framework)

4. Leta upp och öppna filen **dmsSettings.json** i installationsmappen, ange **ApplicationId** och **resourceId**och spara filen.

    ![Hybridarbetstagarinställningar för Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Generera ett certifikat som Azure Database Migration Service kan använda för att autentisera kommunikationen från hybridarbetaren med hjälp av följande kommando.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Ett certifikat genereras i mappen Installera.

    ![Hybridarbetscertifikat för Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. I Azure-portalen navigerar du till app-ID:t under **Hantera**, väljer **Certifikat & hemligheter**och väljer sedan Ladda upp **certifikat** för att välja det offentliga certifikat som du genererade.

    ![Hybridarbetscertifikat för Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Installera hybridarbetaren för Azure Database Migration Service på den lokala servern genom att köra följande kommando:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > När du kör installationskommandot kan du också använda följande parametrar:
    >
    > - **-TelemetryOptOut** - Stoppar arbetaren från att skicka telemetri men fortsätter att logga lokalt minimalt.  Installationsprogrammet skickar fortfarande telemetri.
    > - **-p {InstallLocation}**. Aktiverar ändring av installationssökvägen, som som standard är "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Om installationsprogrammet körs utan fel visas en onlinestatus i Azure Database Migration Service och du är redo att migrera dina databaser.

    ![Migreringstjänsten för Azure Database online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Avinstallera hybridläget för Azure Database Migration Service

För närvarande stöds avinstallation av hybridläget för Azure Database Migration Service endast via hybridarbetsinstallationsprogrammet för Azure Database Migration Service på din lokala server med hjälp av följande kommando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> När du kör avinstallationskommandot kan du också använda parametern "-ReuseCert", som behåller AdApp-cert som genereras av generateCert-arbetsflödet.  Detta gör det möjligt att använda samma certifikat som tidigare genererades och laddades upp.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Konfigurera hybridarbetaren för Azure Database Migration Service med PowerShell

Förutom att installera hybridarbetaren för Azure Database Migration Service via Azure-portalen tillhandahåller vi ett [PowerShell-skript](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) som du kan använda för att automatisera arbetsinstallationsstegen när du har skapat en ny instans av Azure Database Migration Service i hybridläge. Skriptet:

1. Skapar en ny AdApp.
2. Hämtar installationsprogrammet.
3. Kör generateCert-arbetsflödet.
4. Laddar upp certifikatet.
5. Lägger till AdApp som deltagare i din Azure Database Migration Service-instans.
6. Kör arbetsflödet för installation.

Det här skriptet är avsett för snabb prototyper när användaren redan har alla nödvändiga behörigheter i miljön. Observera att i din produktionsmiljö kan AdApp och Cert ha olika krav, så skriptet kan misslyckas.

> [!IMPORTANT]
> Det här skriptet förutsätter att det finns en befintlig instans av Azure Database Migration Service i hybridläge och att det Azure-konto som används har behörighet att skapa AdApps i klienten och ändra RBAC på prenumerationen.

Fyll i parametrarna högst upp i skriptet och kör sedan skriptet från en Administratörs-PowerShell-instans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera SQL Server till en Hanterad Azure SQL Database-hanterad instans online](tutorial-sql-server-managed-instance-online.md)
> [Migrera SQL Server till en enda databas eller poolad databas i Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
