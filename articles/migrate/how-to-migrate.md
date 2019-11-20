---
title: Lägg till Migreringsverktyg i Azure Migrate
description: Lär dig hur du lägger till Migreringsverktyg i Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185968"
---
# <a name="add-migration-tools"></a>Lägga till migreringsverktyg

I den här artikeln beskrivs hur du lägger till Migreringsverktyg i [Azure Migrate](migrate-overview.md).

Azure Migrate tillhandahåller en hubb med verktyg för utvärdering och migrering till Azure. Den innehåller inbyggda verktyg, verktyg som tillhandahålls av andra Azure-tjänster och oberoende program varu leverantörer (ISV) som tillhandahålls av tredje part.

Om du vill lägga till ett Migreringsverktyg och ännu inte har skapat ett Azure Migrate projekt, följer du den här [artikeln](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Välja ett ISV-verktyg

Om du väljer ett [ISV-verktyg](migrate-services-overview.md#isv-integration) för migrering kan du starta genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version, i enlighet med ISV-principen. I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Distribuera verktyget och följ anvisningarna och dokumentationen för verktyget för att ansluta arbets ytan med Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Välj ett scenario för migrering

1. Klicka på **Översikt**i Azure Migrate projektet.
2. Välj det scenario för migrering som du vill använda:

    - Om du vill migrera datorer och arbets belastningar till Azure väljer du **utvärdera och migrera servrar**.
    - Om du vill migrera lokala SQL-datorer väljer du **utvärdera och migrera databaser**.
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
