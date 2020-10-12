---
title: Lägg till Migreringsverktyg i Azure Migrate
description: Lär dig hur du lägger till Migreringsverktyg i Azure Migrate.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: b6ac8ed64d3f12783cc16f0428874a19d027adf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86109815"
---
# <a name="add-migration-tools"></a>Lägga till migreringsverktyg

I den här artikeln beskrivs hur du lägger till Migreringsverktyg i [Azure Migrate](./migrate-services-overview.md).

- Om du vill lägga till ett Migreringsverktyg och ännu inte har skapat ett Azure Migrate projekt, följer du den här [artikeln](how-to-add-tool-first-time.md).
- Om du har lagt till ett ISV-verktyg för migrering [följer du stegen](prepare-isv-movere.md)för att förbereda arbetet med verktyget.

## <a name="select-a-migration-scenario"></a>Välj ett scenario för migrering

1. Klicka på **Översikt**i Azure Migrate projektet.
2. Välj det scenario för migrering som du vill använda:

    - Om du vill migrera datorer och arbets belastningar till Azure väljer du **utvärdera och migrera servrar**.
    - Om du vill migrera lokala SQL Server databaser väljer du **utvärdera och migrera databaser**.
    - Om du vill migrera lokala webbappar väljer du **utvärdera och migrera webbappar**.
    - Om du vill migrera stora mängder lokala data till Azure i offline-läge väljer **du Beställ en data Box-enhet**.

    ![Bedömnings scenario](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Välj ett Migreringsverktyg för Server

1. Klicka på **utvärdera och migrera servrar**.
2. Om du ännu inte har lagt till Migreringsverktyg i **Azure Migrate-servrar**, väljer du **Klicka här för att lägga till ett Migreringsverktyg**under **Migreringsverktyg**. Om du redan har lagt till Migreringsverktyg väljer du **ändra**i **Lägg till fler Migreringsverktyg**.

    > [!NOTE]
    > Om du behöver navigera till ett annat projekt, i **Azure Migrate-servrar**, bredvid **se information om ett annat migrerat projekt**klickar du **på Klicka här**.

3. I **Azure Migrate**väljer du det Migreringsverktyg som du vill använda.
    - Om du använder Azure Migrate Server-migrering kan du konfigurera och köra migreringar direkt i Azure Migrate projektet.
    - Om du använder ett utvärderings verktyg från tredje part, navigerar du till den länk som tillhandahölls för ISV och kör migreringen enligt de instruktioner som de tillhandahåller.

## <a name="select-a-database-migration-tool"></a>Välj ett Migreringsverktyg för databas

1. Klicka på **utvärdera och migrera databaser**
2. I **databaser**klickar du på **Lägg till verktyg**.
3. I Lägg till ett verktyg > **Välj Migreringsverktyg**väljer du det verktyg som du vill använda för att migrera databasen.

## <a name="select-a-web-app-migration-tool"></a>Välj ett Migreringsverktyg för webbapp

1. Klicka på **utvärdera och migrera webbappar**.
2. Följ länken till migrations verktyget för Azure App Service. Använd Migreringsverktyget för att:

    - **Utvärdera appar online**: du kan utvärdera och migrera appar med en offentlig URL online med hjälp av Azure App Service Migration Assistant.
    - **.Net/php**: för interna .net-och php-appar kan du hämta och köra Migration Assistant.

## <a name="order-an-azure-data-box"></a>Beställ en Azure Data Box

Om du vill migrera stora mängder data till Azure kan du beställa en Azure DAta box för data överföring offline.

1. Klicka på **Beställ en data Box-enhet**.
2. I **Välj din Azure Data Box**anger du din prenumeration. 
3. Överföringen är en import till Azure. Ange data källa och mål för Azure-regionen för data.

## <a name="next-steps"></a>Nästa steg

Prova en migrering med hjälp av migrering av Azure Migrate Server för virtuella [Hyper-V-](tutorial-migrate-hyper-v.md) eller [VMware](tutorial-migrate-vmware.md) -datorer.
