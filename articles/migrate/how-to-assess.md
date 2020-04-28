---
title: Lägg till utvärderings verktyg i Azure Migrate
description: Lär dig hur du lägger till bedömnings verktyg i Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185918"
---
# <a name="add-assessment-tools"></a>Lägga till utvärderingsverktyg

Den här artikeln beskriver hur du lägger till bedömnings verktyg i [Azure Migrate](migrate-overview.md).

Azure Migrate tillhandahåller en hubb med verktyg för utvärdering och migrering till Azure. Den innehåller Azure Migrate verktyg, samt andra verktyg och oberoende program varu leverantörer (ISV)-erbjudanden.

Om du vill lägga till ett utvärderings verktyg och du inte har ett Azure Migrate projekt, följer du den här [artikeln](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Välj ett verktyg

Om du väljer ett verktyg som inte Azure Migrate för utvärdering börjar du med att skaffa en licens eller registrerar dig för en kostnads fri utvärderings version, i enlighet med verktygs principen. Det finns ett alternativ i verktyg för att ansluta till Azure Migrate. Följ anvisningarna och dokumentationen för att ansluta verktyget till Azure Migrate. [Läs mer](migrate-services-overview.md) om verktyg.


## <a name="select-an-assessment-scenario"></a>Välj ett utvärderings scenario

1. Klicka på **Översikt**i Azure Migrate projektet.
2. Välj det utvärderings scenario du vill använda:

    - Välj **utvärdera och migrera servrar**för att identifiera och utvärdera datorer och arbets belastningar för migrering till Azure.
    - Om du vill utvärdera lokala SQL-datorer väljer du **utvärdera och migrera databaser**.
    - Om du vill utvärdera lokala webbappar väljer du **utvärdera och migrera webbappar**.

    ![Bedömnings scenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Välj ett Server bedömnings verktyg 

1. Klicka på **utvärdera och migrera servrar**.
2. Om du inte har lagt till ett utvärderings verktyg under **utvärderings verktyg**i **Azure Migrate-servrar**, väljer du **Klicka här för att lägga till ett utvärderings verktyg**. Om du redan har lagt till bedömnings verktyg, i **Lägg till fler utvärderings verktyg**, väljer du **ändra**.

    > [!NOTE]
    > Om du behöver navigera till ett annat projekt, i **Azure Migrate-servrar**, bredvid **se information om ett annat migrerat projekt**klickar du **på Klicka här**.

3. I **Azure Migrate**väljer du det utvärderings verktyg som du vill använda.

    - Om du använder Azure Migrate Server utvärdering kan du ställa in, köra och Visa utvärderingar direkt i Azure Migrate projektet.
    - Om du använder ett annat utvärderings verktyg navigerar du till den länk som tillhandahölls för platsen och kör utvärderingen enligt de instruktioner som de tillhandahåller.


## <a name="select-a-database-assessment-tool"></a>Välj ett verktyg för databas bedömning

1. Klicka på **utvärdera och migrera databaser**
2. I **databaser**klickar du på **Lägg till verktyg**.
3. I Lägg till ett verktyg > **Välj bedömnings verktyg**väljer du det verktyg som du vill använda för att utvärdera din databas.

## <a name="select-a-web-app-assessment-tool"></a>Välj ett verktyg för bedömning av webb program

1. Klicka på **utvärdera och migrera webbappar**.
2. Följ länken till migrations verktyget för Azure App Service. Använd Migreringsverktyget för att:

    - **Utvärdera appar online**: du kan utvärdera appar med en offentlig URL online med hjälp av Azure App Service Migration Assistant.
    - **.Net/php**: för interna .net-och php-appar kan du hämta och köra Migration Assistant.



## <a name="next-steps"></a>Nästa steg

Prova en utvärdering med hjälp av Azure Migrate Server utvärdering för virtuella [VMware](tutorial-prepare-vmware.md) -datorer, [Hyper-V](tutorial-prepare-hyper-v.md)eller [fysiska servrar](tutorial-prepare-physical.md)
