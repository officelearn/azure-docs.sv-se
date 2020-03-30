---
title: Konfigurera Azure Blockchain Workbench-databasbrandväggen
description: Lär dig hur du konfigurerar databasbrandväggen för Azure Blockchain Workbench Preview så att externa klienter och program kan ansluta.
ms.date: 09/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e6739b7ead9299f020465d3c50ed01826334ca76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326008"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurera brandväggen för Azure Blockchain Workbench-databasen

Den här artikeln beskriver hur du konfigurerar en brandväggsregel via Azure Portal. Brandväggsregler gör att externa klienter eller program kan ansluta till din Azure Blockchain Workbench-databas.

## <a name="connect-to-the-blockchain-workbench-database"></a>Anslut till Blockchain Workbench-databasen

Så här ansluter du till databasen där du vill konfigurera en regel:

1. Logga in på Azure-portalen med ett konto som har **ägarbehörigheter** för Azure Blockchain Workbench-resurserna.
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.
3. Välj namnet på resursgruppen för din Blockchain Workbench-distribution.
4. Sortera listan med resurser genom att välja **Typ** och välj sedan din **SQL-server**.
5. Exempellistan med resurser på följande skärm innehåller två databaser: *master* och *lsgn-sdk*. Du konfigurerar brandväggsregeln för *lsgn-sdk*.

![Visa en lista över Blockchain Workbench-resurser](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Skapa en brandväggsregel för databasen

Så här skapar du en brandväggsregel:

1. Välj länken till ”lsgn-sdk”-databasen.
2. Välj **Konfigurera serverns brandvägg** på menyraden.

   ![Konfigurera serverns brandvägg](./media/database-firewall/configure-server-firewall.png)

3. Så här skapar du en regel för din organisation:

   * Ange ett **REGELNAMN**
   * Ange en IP-adress för adressintervallets **START-IP**
   * Ange en IP-adress för adressintervallets **SLUT-IP**

   ![Skapa brandväggsregel](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Om du bara vill lägga till IP-adressen för din dator väljer du **+ Lägg till klient-IP**.
        
1. Spara brandväggskonfigurationen genom att välja **Spara**.
2. Testa IP-adressintervallet som du har konfigurerat för databasen genom att ansluta från ett program eller ett verktyg. Till exempel SQL Server Management Studio.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Databasvyer i Azure Blockchain Workbench](database-views.md)