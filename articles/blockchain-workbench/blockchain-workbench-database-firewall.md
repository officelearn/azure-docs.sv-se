---
title: Konfigurera Azure Blockchain arbetsstationen SQL DB-brandväggen
description: Lär dig hur du konfigurerar Azure Blockchain arbetsstationen SQL DB-brandväggen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dc22f212c014ab1d6622eff3491d669b21ca6f47
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurera Azure Blockchain arbetsstationen database-brandvägg

Den här artikeln visar hur du konfigurerar en brandväggsregel som använder Azure portal. Brandväggsregler att externa klienter eller program som ansluter till databasen Azure Blockchain arbetsstationen.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ansluta till databasen Blockchain arbetsstationen

Att ansluta till databasen där du vill konfigurera en regel:

1. Logga in på Azure-portalen med ett konto som har **ägare** behörigheter för Blockchain-arbetsstationen för Azure-resurser.
2. I det vänstra navigeringsfönstret väljer **resursgrupper**.
3. Välj namnet på resursgruppen för distributionen Blockchain arbetsstationen.
4. Välj **typen** att sortera listan över resurser och välj sedan din **SQLServer**.
5. Resursen listan exemplet i följande skärmbild visar två databaser: *master* och *lsgn sdk*. Du kan konfigurera brandväggsregeln på *lsgn sdk*.

![Lista Blockchain arbetsstationen resurser](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Skapa en brandväggsregel för databasen

Skapa en brandväggsregel:

1. Välj en länk till ”lsgn sdk”-databasen.
2. På menyraden, Välj **ange serverbrandvägg**.

   ![Konfigurera serverns brandvägg](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Skapa en regel för din organisation:

   * Ange en **REGELNAMN**
   * Ange en IP-adress för den **första IP-** intervall
   * Ange en IP-adress för den **sista IP** intervall

   ![Skapa brandväggsregel](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Om du bara vill lägga till IP-adressen till datorn väljer du **+ Lägg till klientens IP-Adressen**.
        
1. Välj för att spara konfigurationen av brandväggen **spara**.
2. Testa IP-adressintervall som du har konfigurerat för databasen genom att ansluta från ett program eller ett verktyg. Till exempel SQL Server Management Studio.

## <a name="next-steps"></a>Nästa steg

* [Databasvyer i Azure Blockchain arbetsstationen](blockchain-workbench-database-views.md)