---
title: Lägg till bedömningsverktyg i Azure Migrate | Microsoft Docs
description: Beskriver hur du lägger till bedömningsverktyg i Azure Migrate-hubben.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811653"
---
# <a name="add-assessment-tools"></a>Lägg till bedömningsverktyg

Den här artikeln beskrivs hur du lägger till bedömningsverktyg i [Azure Migrate](migrate-overview.md).

Azure Migrate tillhandahåller en hubb för verktyg för bedömning och migrering till Azure. Det innehåller inbyggda verktyg verktyg som tillhandahålls av andra Azure-tjänster och programvara från tredje part för oberoende programvaruleverantörer (ISV)-erbjudanden.

Om du vill lägga till ett verktyg för utvärdering och du ännu inte har ett Azure Migrate-projekt, följ den här [artikeln](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Att välja ett ISV-verktyg

Om du väljer en [ISV-verktyget](migrate-services-overview.md#isv-integration) för utvärdering, som du kan börja genom att hämta en licens eller registrera dig för en kostnadsfri utvärderingsversion, i enlighet med ISV-princip. Det finns ett alternativ för att ansluta till Azure Migrate i varje verktyg. Följ anvisningarna för verktyg och dokumentation om du vill ansluta arbetsytan verktyget med Azure Migrate. 


## <a name="select-an-assessment-scenario"></a>Välj ett scenario för utvärdering

1. I Azure Migrate-projektet klickar du på **översikt**.
2. Välj den utvärdering scenario som du vill använda:

    - Om du vill identifiera och utvärdera datorer och arbetsbelastningar för migrering till Azure, Välj **utvärdera och migrera servrar för**.
    - Om du vill utvärdera lokala datorer med SQL, Välj **utvärdera och migrera databaser**.
    - Om du vill utvärdera lokala webbappar, Välj **utvärdera och migrera webbappar**.

    ![Utvärdering av scenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Välj ett verktyg för server-utvärdering 

1. Klicka på **utvärdera och migrera servrar för**.
2. I **Azure Migrate - servrar**, om du inte har lagt till en utvärderingsvertyget under **bedömningsverktyg**väljer **Klicka här för att lägga till en utvärderingsvertyget**. Om du redan har lagt till bedömningsverktyg, i **lägga till mer bedömningsverktyg**väljer **ändra**.

    > [!NOTE]
    > Om du behöver navigera till ett annat projekt i **Azure Migrate - servrar**, bredvid **finns i ett annat migrate-projekt**, klickar du på **Klicka här**.

3. I **Azure Migrate**, Välj utvärderingsvertyget som du vill använda.

    
    ![Bedömningsverktyg](./media/how-to-assess/assess-tool.png)

    - Om du använder Azure Migrate Server-utvärdering kan du konfigurera, köra och visa utvärderingar direkt i Azure Migrate-projektet.
    - Om du använder ett verktyg från tredje part utvärdering, gå till länken till sin webbplats och kör utvärderingen enligt instruktionerna som de tillhandahåller.


## <a name="select-a-database-assessment-tool"></a>Välj ett verktyg för utvärdering av databasen

1. Klicka på **utvärdera och migrera databaser**
2. I **databaser**, klickar du på **lägga till verktyg**.
3. Lägg till ett verktyg i > **väljer utvärderingsvertyget**, Välj de verktyg som du vill använda för att utvärdera din databas.

## <a name="select-a-web-app-assessment-tool"></a>Välj en utvärderingsvertyget för web app

1. Klicka på **utvärdera och migrera webbappar**.
2. Länken Migreringsverktyget för Azure App Service. Använd Migreringsverktyget för att:

    - **Utvärdera appar online**: Du kan utvärdera appar med en offentlig URL online, med hjälp av Azure App Service Migration Assistant.
    - **.NET/PHP**: Du kan ladda ned och köra Migration Assistant för interna .NET och PHP-appar.



## <a name="next-steps"></a>Nästa steg

Prova att använda en utvärdering med hjälp av Azure Migrate Server-utvärdering för [Hyper-V](tutorial-prepare-hyper-v.md) eller [VMware](tutorial-prepare-vmware.md) virtuella datorer.
