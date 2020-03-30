---
title: Lägga till migreringsverktyg i Azure Migrate
description: Läs om hur du lägger till migreringsverktyg i Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185968"
---
# <a name="add-migration-tools"></a>Lägga till migreringsverktyg

I den här artikeln beskrivs hur du lägger till migreringsverktyg i [Azure Migrate](migrate-overview.md).

Azure Migrate är ett nav med verktyg för utvärdering och migrering till Azure. Den innehåller inbyggda verktyg, verktyg som tillhandahålls av andra Azure-tjänster och ISV-erbjudanden (Third-party Independent Software Vendor).

Om du vill lägga till ett migreringsverktyg och ännu inte har konfigurerat ett Azure Migrate-projekt följer du den här [artikeln](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Välja ett ISV-verktyg

Om du väljer ett [ISV-verktyg](migrate-services-overview.md#isv-integration) för migrering kan du börja med att skaffa en licens eller registrera dig för en kostnadsfri utvärderingsversion i enlighet med ISV-principen. I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Distribuera verktyget och följ verktygsinstruktionerna och dokumentationen för att ansluta verktygsarbetsytan med Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Välj ett migreringsscenario

1. Klicka på **Översikt**i Azure Migrate-projektet .
2. Välj det migreringsscenario som du vill använda:

    - Om du vill migrera datorer och arbetsbelastningar till Azure väljer du **Utvärdera och migrera servrar**.
    - Om du vill migrera lokala SQL-datorer väljer du **Utvärdera och migrera databaser**.
    - Om du vill migrera lokala webbappar väljer du **Utvärdera och migrera webbappar**.
    - Om du vill migrera stora mängder lokala data till Azure i offlineläge väljer du **Beställ en dataruta**.

    ![Bedömningsscenario](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Välj ett verktyg för migrering av servrar

1. Klicka på **Utvärdera och migrera servrar**.
2. I **Azure Migrate - Servrar**väljer du Klicka **här**under **Migreringsverktyg under Migreringsverktyg**. Om du redan har lagt till migreringsverktyg väljer du **Ändra**i **Lägg till fler migreringsverktyg**.

    > [!NOTE]
    > Om du behöver navigera till ett annat projekt klickar du på Klicka här i **Azure Migrate - Servrar**, **bredvid** **Visa information för ett annat migrerande projekt**.

3. Välj det migreringsverktyg som du vill använda i **Azure Migrate.**
    - Om du använder Azure Migrate Server Migration kan du konfigurera och köra migreringar direkt i Azure Migrate-projektet.
    - Om du använder ett bedömningsverktyg från tredje part navigerar du till länken som tillhandahålls för ISV och kör migreringen i enlighet med de instruktioner de tillhandahåller.

## <a name="select-a-database-migration-tool"></a>Välj ett verktyg för databasmigrering

1. Klicka på **Utvärdera och migrera databaser**
2. Klicka på **Lägg till verktyg**i **Databaser**.
3. I Lägg till ett verktyg > **Välj migreringsverktyg**väljer du det verktyg som du vill använda för att migrera databasen.

## <a name="select-a-web-app-migration-tool"></a>Välj ett migreringsverktyg för webbappar

1. Klicka på **Utvärdera och migrera webbappar**.
2. Följ länken till migreringsverktyget för Azure App Service. Använd migreringsverktyget för att:

    - **Utvärdera appar online**: Du kan bedöma och migrera appar med en offentlig URL online med hjälp av Migreringsassistenten för Azure App Service.
    - **.NET/PHP**: För interna .NET- och PHP-appar kan du hämta och köra Migreringsassistenten.

## <a name="order-an-azure-data-box"></a>Beställ en Azure-dataruta

Om du vill migrera stora mängder data till Azure kan du beställa en Azure DAta Box för offlinedataöverföring.

1. Klicka på **Beställ en dataruta**.
2. Ange din prenumeration i **Välj din Azure Data Box.** 
3. Överföringen kommer att vara en import till Azure. Ange datakällan och Azure-regionens mål för data.

## <a name="next-steps"></a>Nästa steg

Prova en migrering med Azure Migrate Server Migration för virtuella [hyper-V-](tutorial-migrate-hyper-v.md) eller [VMware-datorer.](tutorial-migrate-vmware.md)
