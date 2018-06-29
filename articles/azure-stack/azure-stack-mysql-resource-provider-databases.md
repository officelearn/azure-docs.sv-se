---
title: Med hjälp av databaser som tillhandahålls av MySQL kortet RP på AzureStack | Microsoft Docs
description: Hur du skapar och hanterar MySQL-databaser som etablerats med hjälp av Resursprovidern för MySQL-kort
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: fdadc29aa1d25e90afe088053ae4fe2139fa7f16
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031845"
---
# <a name="create-mysql-databases"></a>Skapa MySQL-databaser

Du kan skapa och hantera självbetjäning databaser i användarportalen. En Azure-stacken användare behöver en prenumeration med ett erbjudande som inkluderar MySQL-databas.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Testa distributionen genom att skapa en MySQL-databas

1. Logga in på användarportalen Azure stacken.
2. Välj **+ ny** > **Data + lagring** > **MySQL-databas** > **lägga till**.
3. Under **skapa MySQL-databas**, ange namnet på databasen och konfigurera andra inställningar som krävs för din miljö.

    ![Skapa en test MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Under **Create Database**väljer **SKU**. Under **väljer en MySQL-SKU**, Välj SKU för din databas.

    ![Välj en MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >De är tilldelade en SKU när värdservrar läggs till Azure-stacken. Du skapa databaser i poolen med värd-servrar i en SKU.

5. Under **inloggning**väljer ***konfigurera nödvändiga inställningar***.
6. Under **väljer en inloggning**, kan du välja en befintlig inloggning eller välja **+ skapa en ny inloggning** att ställa in en ny inloggning.  Ange en **databasinloggningen** namn och **lösenord**, och välj sedan **OK**.

    ![Skapa en ny databasinloggning](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Längden på inloggningen Databasnamnet får inte överstiga 32 tecken i MySQL 5.7. Det får inte överstiga 16 tecken i tidigare versioner.

7. Välj **skapa** för att konfigurera databasen.

När databasen har distribuerats, notera den **anslutningssträngen** under **Essentials**. Du kan använda den här strängen i alla program som behöver åtkomst till MySQL-databas.

![Hämta anslutningssträngen för MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Uppdatera lösenordet för administratörer

Du kan ändra lösenordet genom att ändra på MySQL server-instansen.

1. Välj **administrativa resurser** > **MySQL som värd för servrar**. Välj servern som värd.
2. Under **inställningar**väljer **lösenord**.
3. Under **lösenord**, ange det nya lösenordet och välj sedan **spara**.

![Uppdatera administratörslösenordet](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Nästa steg

[Uppdatera MySQL-resursprovidern](azure-stack-mysql-resource-provider-update.md)
