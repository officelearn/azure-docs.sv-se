---
title: 'Snabb start: skapa en instans för Azure Database Migration Service hybrid läge med hjälp av Azure Portal | Microsoft Docs'
description: Använd Azure Portal för att skapa en instans av Azure Database Migration Service i hybrid läge
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/06/2019
ms.openlocfilehash: 47c0ecb5674f821d3034fb5f165df08f176c2e93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646946"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>Snabb start: skapa en instans av Azure Database Migration Service i hybrid läge med hjälp av Azure Portal (för hands version)

Azure Database Migration Service hybrid läge hanterar migrering av databasen med hjälp av en migrering som finns lokalt med en instans av Azure Database Migration Service som körs i molnet. Hybrid läge är särskilt användbart för scenarier där det inte finns någon plats-till-plats-anslutning mellan det lokala nätverket och Azure, eller om det finns begränsad plats-till-plats-anslutningens bandbredd.

I den här snabb starten använder du Azure Portal för att skapa en instans av Azure Database Migration Service i hybrid läge. Därefter laddar du ned, installerar och konfigurerar hybrid Worker i ditt lokala nätverk. Under för hands versionen kan du använda Azure Database Migration Service hybrid läge för att migrera data från en lokal instans av SQL Server till Azure SQL Database.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren, gå till [Microsoft Azure-portalen](https://portal.azure.com/) och logga in genom att ange dina autentiseringsuppgifter.

Standardvyn är instrumentpanelen.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Registrera Microsoft. data migration-resurs leverantören innan du skapar din första instans av Azure Database Migration Service.

1. I Azure Portal väljer du **prenumerationer**, väljer den prenumeration där du vill skapa instansen av Azure Database migration service och väljer sedan **resurs leverantörer**.

    ![Sök efter resurs leverantör](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Skapar en instans av tjänsten

1. Välj +**skapa en resurs** för att skapa en instans av Azure Database migration service.

2. Sök efter ”migration” på Marketplace och välj **Azure Database Migration Service**. På skärmen **Azure Database Migration Service** väljer du sedan **Skapa**.

3. På skärmen **Skapa migreringstjänst**:

    - Välj ett **tjänst namn** som är minnes värt och unikt för att identifiera din instans av Azure Database migration service.
    - Välj den Azure-**prenumeration** där du vill skapa instansen.
    - Välj en befintlig **resursgrupp** eller skapa en ny.
    - Välj den **plats** som ligger närmast din käll- eller målserver.

    > [!IMPORTANT]
    > I för hands versionen stöds hybrid läge endast i regionen USA, östra. Eftersom hybrid Worker är installerat i ditt lokala nätverk, finns det ingen påverkan på prestanda även om du migrerar till ett mål i en annan region.

    - För **tjänst läge**väljer du **hybrid (för hands version)** .

      ![Skapa migration service – grundläggande information](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Välj **Granska + skapa**.

5. På fliken **Granska + skapa** granskar du villkoren, verifierar den andra informationen och väljer sedan **skapa**.

    ![Skapa migration service – granska + skapa](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Efter en liten stund skapas din instans av Azure Database Migration Service i hybrid läge och är redo att konfigureras. Azure Database Migration Service-instansen visas som på följande bild:

    ![Azure Database Migration Service hybrid läges instans](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. När tjänsten har skapats väljer du **Egenskaper**och kopierar sedan värdet som visas i rutan **resurs-ID** , som du kommer att använda för att installera Azure Database migration service hybrid Worker.

    ![Egenskaper för Azure Database Migration Service hybrid läge](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Skapa Azure App registrerings-ID

Du måste skapa ett Azure App registrerings-ID som den lokala hybrid Worker kan använda för att kommunicera med Azure Database Migration Service i molnet.

1. I Azure Portal väljer du **Azure Active Directory**, väljer **Appregistreringar**och väljer sedan **ny registrering**.
2. Ange ett namn för programmet och välj den typ av konton som du vill ge stöd för att ange vem som kan använda programmet under **typer av konto typer som stöds**.

    ![Register program för Azure Database Migration Service hybrid läge](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Använd standardvärdena för fälten **omdirigerings-URI (valfritt)** och välj sedan **Registrera**.

4. När app-ID-registreringen är klar noterar du **program-ID: t**, som du kommer att använda när du installerar hybrid Worker.

5. Gå till Azure Database Migration Service i Azure Portal, Välj **åtkomst kontroll (IAM)** och välj sedan **Lägg till roll tilldelning** för att tilldela deltagar åtkomst till app-ID: t.

    ![Azure Database Migration Service hybrid läge tilldela deltagar rollen](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Välj **deltagare** som roll, tilldela åtkomst till **Azure AD-användare eller tjänstens huvud**namn och välj sedan appens id-namn.

    ![Azure Database Migration Service hybrid läge tilldela deltagar roll information](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Välj **Spara** för att spara roll tilldelningen för app-ID: t på Azure Database migration service resursen.

## <a name="download-and-install-the-hybrid-worker"></a>Ladda ned och installera hybrid Worker

1. I Azure Portal navigerar du till din instans av Azure Database Migration Service.

2. Under **Inställningar**väljer du **hybrid**och väljer sedan **installations programmet Ladda** ned för att ladda ned hybrid Worker.

    ![Azure Database Migration Service hybrid Worker-hämtning](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extrahera ZIP-filen på den server som ska vara värd för Azure Database Migration Service hybrid Worker.

4. I mappen installera, leta upp och öppna filen **dmsSettings. JSON** , ange **ApplicationId** och **resourceId**och spara sedan filen.

    ![Azure Database Migration Service hybrid Worker-inställningar](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)
 
5. Generera ett certifikat som Azure Database Migration Service kan använda för att autentisera kommunikationen från hybrid arbetaren med hjälp av följande kommando.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Ett certifikat skapas i installationsmappen.

    ![Azure Database Migration Service hybrid Worker-certifikat](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. I Azure Portal går du till app-ID: t under **Hantera**, väljer **certifikat & hemligheter**och väljer sedan **Ladda upp certifikat** för att välja det offentliga certifikat som du nyss skapade.

    ![Azure Database Migration Service hybrid Worker Certificate upload](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Installera Azure Database Migration Service hybrid Worker på din lokala server genom att köra följande kommando:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

8. Om installations programmet körs utan fel kommer tjänsten att visa en onlinestatus i Azure Database Migration Service och du är redo att migrera dina databaser.

    ![Azure Database Migration Service online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Avinstallera Azure Database Migration Service hybrid läge

För närvarande stöds inte installation av Azure Database Migration Service hybrid läge via Azure Database Migration Service hybrid Worker-installation på den lokala servern med hjälp av följande kommando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera SQL Server till en Azure SQL Database Hanterad instans online](tutorial-sql-server-managed-instance-online.md)
> [migrera SQL Server till en databas eller databas i pooler i Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
