---
title: Lägg till Migreringsverktyg i Azure Migrate
description: Beskriver hur du lägger till Migreringsverktyg i Azure Migrate-hubben.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811874"
---
# <a name="add-migration-tools"></a>Lägg till Migreringsverktyg

Den här artikeln beskrivs hur du lägger till Migreringsverktyg i [Azure Migrate](migrate-overview.md).

Azure Migrate tillhandahåller en hubb för verktyg för bedömning och migrering till Azure. Det innehåller inbyggda verktyg verktyg som tillhandahålls av andra Azure-tjänster och programvara från tredje part för oberoende programvaruleverantörer (ISV)-erbjudanden.

Om du vill lägga till ett migrationsverktyg och ännu inte har konfigurerat ett Azure Migrate-projekt, följ den här [artikeln](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Att välja ett ISV-verktyg

Om du väljer en [ISV-verktyget](migrate-services-overview.md#isv-integration) för migrering, som du kan börja genom att hämta en licens eller registrera dig för en kostnadsfri utvärderingsversion, i enlighet med ISV-princip. Det finns ett alternativ för att ansluta till Azure Migrate i varje verktyg. Distribuera verktyget och följ anvisningarna för verktyg och dokumentation om du vill ansluta arbetsytan verktyget med Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Välj ett Migreringsscenario

1. I Azure Migrate-projektet klickar du på **översikt**.
2. Välj Migreringsscenario som du vill använda:

    - Om du vill migrera datorer och arbetsbelastningar till Azure, Välj **utvärdera och migrera servrar för**.
    - Om du vill migrera lokala datorer med SQL, Välj **utvärdera och migrera databaser**.
    - Om du vill migrera lokala webbappar, Välj **utvärdera och migrera webbappar**.
    - Om du vill migrera stora mängder data på plats till Azure i offline-läge, Välj **beställa en Data Box**.

    ![Utvärdering av scenario](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Välj en server-Migreringsverktyget

1. Klicka på **utvärdera och migrera servrar för**.
2. I **Azure Migrate - servrar**, om du inte har lagt till Migreringsverktyg ännu, under **Migreringsverktyg**väljer **Klicka här för att lägga till ett migrationsverktyg**. Om du redan har lagt till verktyg för molnmigrering, i **lägga till mer Migreringsverktyg**väljer **ändra**.

    > [!NOTE]
    > Om du behöver navigera till ett annat projekt i **Azure Migrate - servrar**, bredvid **finns i ett annat migrate-projekt**, klickar du på **Klicka här**.

3. I **Azure Migrate**, Välj Migreringsverktyget som du vill använda.
    - Om du använder Azure Migrate-servermigrering kan du konfigurera och kör migreringar direkt i Azure Migrate-projektet.
    - Om du använder ett verktyg från tredje part utvärdering, gå till länken för ISV: er och kör migreringen enligt instruktionerna som de tillhandahåller.

## <a name="select-a-database-migration-tool"></a>Välj en databas-Migreringsverktyget

1. Klicka på **utvärdera och migrera databaser**
2. I **databaser**, klickar du på **lägga till verktyg**.
3. Lägg till ett verktyg i > **väljer Migreringsverktyget**, Välj de verktyg som du vill använda för att migrera din databas.

## <a name="select-a-web-app-migration-tool"></a>Välj en web app-Migreringsverktyget

1. Klicka på **utvärdera och migrera webbappar**.
2. Länken Migreringsverktyget för Azure App Service. Använd Migreringsverktyget för att:

    - **Utvärdera appar online**: Du kan utvärdera och migrera appar med en offentlig URL online, med hjälp av Azure App Service Migration Assistant.
    - **.NET/PHP**: Du kan ladda ned och köra Migration Assistant för interna .NET och PHP-appar.

## <a name="order-an-azure-data-box"></a>Beställa en Azure Data Box

För att migrera stora mängder data till Azure, kan du beställa en Azure DAta Box för överföring av data offline.

1. Klicka på **beställa en Data Box**.
2. I **Välj din Azure Data Box**, ange din prenumeration. 
3. Överföringen kommer att vara en import till Azure. Ange datakällan och Azure-region mål för data.

## <a name="next-steps"></a>Nästa steg

Prova en migrering med hjälp av Azure Migrate servermigrering för [Hyper-V](tutorial-migrate-hyper-v.md) eller [VMware](tutorial-migrate-vmware.md) virtuella datorer.
