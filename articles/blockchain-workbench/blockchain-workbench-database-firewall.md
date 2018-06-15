---
title: Konfigurera SQL DB-brandväggen i Azure Blockchain Workbench
description: Lär dig hur du konfigurerar SQL DB-brandväggen i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074957"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurera brandväggen för Azure Blockchain Workbench-databasen

Den här artikeln beskriver hur du konfigurerar en brandväggsregel via Azure Portal. Brandväggsregler gör att externa klienter eller program kan ansluta till din Azure Blockchain Workbench-databas.

## <a name="connect-to-the-blockchain-workbench-database"></a>Anslut till Blockchain Workbench-databasen

Så här ansluter du till databasen där du vill konfigurera en regel:

1. Logga in på Azure Portal med ett konto som har **ägarbehörigheter** för Azure Blockchain Workbench-resurser.
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.
3. Välj namnet på resursgruppen för din Blockchain Workbench-distribution.
4. Sortera listan med resurser genom att välja **Typ** och välj sedan din **SQL-server**.
5. Exempellistan med resurser på följande skärm innehåller två databaser: *master* och *lsgn-sdk*. Du konfigurerar brandväggsregeln för *lsgn-sdk*.

![Visa en lista över Blockchain Workbench-resurser](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Skapa en brandväggsregel för databasen

Så här skapar du en brandväggsregel:

1. Välj länken till ”lsgn-sdk”-databasen.
2. Välj **Konfigurera serverns brandvägg** på menyraden.

   ![Konfigurera serverns brandvägg](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Så här skapar du en regel för din organisation:

   * Ange ett **REGELNAMN**
   * Ange en IP-adress för adressintervallets **START-IP**
   * Ange en IP-adress för adressintervallets **SLUT-IP**

   ![Skapa brandväggsregel](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Om du bara vill lägga till IP-adressen för din dator väljer du **+ Lägg till klient-IP**.
        
1. Spara brandväggskonfigurationen genom att välja **Spara**.
2. Testa IP-adressintervallet som du har konfigurerat för databasen genom att ansluta från ett program eller ett verktyg. Till exempel SQL Server Management Studio.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Databasvyer i Azure Blockchain Workbench](blockchain-workbench-database-views.md)