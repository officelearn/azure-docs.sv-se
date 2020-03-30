---
title: Lägga till utvärderingsverktyg i Azure Migrate
description: Läs om hur du lägger till utvärderingsverktyg i Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185918"
---
# <a name="add-assessment-tools"></a>Lägga till utvärderingsverktyg

I den här artikeln beskrivs hur du lägger till utvärderingsverktyg i [Azure Migrate](migrate-overview.md).

Azure Migrate är ett nav med verktyg för utvärdering och migrering till Azure. Den innehåller Azure Migrate-verktyg, liksom andra verktyg och oberoende programvaruleverantörer (ISV) erbjudanden.

Om du vill lägga till ett utvärderingsverktyg och ännu inte har ett Azure Migrate-projekt följer du den här [artikeln](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Välj ett verktyg

Om du väljer ett verktyg som inte är Azure Migrate för bedömning, börja med att skaffa en licens eller registrera dig för en kostnadsfri utvärderingsversion, i enlighet med verktygsprincipen. Verktyg har en möjlighet att ansluta till Azure Migrate. Följ instruktionerna och dokumentationen för att ansluta verktyget till Azure Migrate. [Läs mer](migrate-services-overview.md) om verktyg.


## <a name="select-an-assessment-scenario"></a>Välj ett bedömningsscenario

1. Klicka på **Översikt**i Azure Migrate-projektet .
2. Välj det bedömningsscenario som du vill använda:

    - Om du vill identifiera och bedöma datorer och arbetsbelastningar för migrering till Azure väljer du **Utvärdera och migrera servrar**.
    - Om du vill bedöma lokala SQL-datorer väljer du **Utvärdera och migrera databaser**.
    - Om du vill bedöma lokala webbappar väljer du **Utvärdera och migrera webbappar**.

    ![Bedömningsscenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Välj ett serverutvärderingsverktyg 

1. Klicka på **Utvärdera och migrera servrar**.
2. Om du inte har lagt till ett bedömningsverktyg under **Bedömningsverktyg**i **Azure Migrate - Servrar**väljer du Klicka här om du vill lägga till ett **bedömningsverktyg**. Om du redan har lagt till bedömningsverktyg väljer du **Ändra**i **Lägg till fler bedömningsverktyg**.

    > [!NOTE]
    > Om du behöver navigera till ett annat projekt klickar du på Klicka här i **Azure Migrate - Servrar**, **bredvid** **Visa information för ett annat migrerande projekt**.

3. Välj det bedömningsverktyg som du vill använda i **Azure Migrate.**

    - Om du använder Azure Migrate Server Assessment kan du ställa in, köra och visa utvärderingar direkt i Azure Migrate-projektet.
    - Om du använder ett annat bedömningsverktyg navigerar du till länken som tillhandahålls för deras webbplats och kör utvärderingen i enlighet med de instruktioner de tillhandahåller.


## <a name="select-a-database-assessment-tool"></a>Välj ett databasutvärderingsverktyg

1. Klicka på **Utvärdera och migrera databaser**
2. Klicka på **Lägg till verktyg**i **Databaser**.
3. I Lägg till ett verktyg > **Välj bedömningsverktyg**väljer du det verktyg som du vill använda för att bedöma databasen.

## <a name="select-a-web-app-assessment-tool"></a>Välj ett bedömningsverktyg för webbappar

1. Klicka på **Utvärdera och migrera webbappar**.
2. Följ länken till migreringsverktyget för Azure App Service. Använd migreringsverktyget för att:

    - **Utvärdera appar online**: Du kan bedöma appar med en offentlig URL online med hjälp av Migreringsassistenten för Azure App Service.
    - **.NET/PHP**: För interna .NET- och PHP-appar kan du hämta och köra Migreringsassistenten.



## <a name="next-steps"></a>Nästa steg

Prova en utvärdering med Azure Migrate Server Assessment for [VMware-virtuella](tutorial-prepare-vmware.md) datorer, [Hyper-V](tutorial-prepare-hyper-v.md)eller [fysiska servrar](tutorial-prepare-physical.md)
