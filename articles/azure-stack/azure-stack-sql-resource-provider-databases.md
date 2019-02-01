---
title: Med hjälp av databaser som tillhandahålls av SQL nätverkskort resursprovidern på Azure Stack | Microsoft Docs
description: Skapa och hantera SQL-databaser som etablerats med hjälp av SQL nätverkskort resursprovidern
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: bbb4f72e029a442362b792f3cdb4731e9563687b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244835"
---
# <a name="create-sql-databases"></a>Skapa SQL-databaser

Du kan skapa och hantera självbetjäning databaser i användarportalen. En Azure Stack-användare behöver en prenumeration med ett erbjudande som innehåller SQL-databastjänst.

1. Logga in på den [Azure Stack](azure-stack-poc.md) användarportalen.

2. Välj **+ ny** &gt; **Data + lagring** &gt; **SQL Server-databas** &gt; **lägga till**.

3. Under **Create Database**, ange informationen som krävs som **databasnamn** och **Max Size i MB**.

   >[!NOTE]
   >Databasens storlek måste vara minst 64 MB, vilket du kan öka när du har distribuerat databasen.

   Konfigurera andra inställningar som krävs för din miljö.

4. Under **Create Database**väljer **SKU**. Under **väljer en SKU**, Välj SKU för din databas.

   ![Skapa databas](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >När värdservrar läggs till Azure Stack, är de tilldelade en SKU. Databaser skapas i poolen med som är värd för servrar i en SKU.

5. Välj **inloggning**.
6. Under **väljer en inloggning**, välja en befintlig inloggning eller välja **+ skapa en ny inloggning**.
7. Under **ny inloggning**, ange ett namn för **databasinloggning** och en **lösenord**.

   >[!NOTE]
   >De här inställningarna är SQL-autentisering-autentiseringsuppgifter som har skapats för din åtkomst till endast den här databasen. Användarnamnet för inloggningen måste vara globalt unikt. Du kan återanvända inloggningsinställningar för andra databaser som använder samma SKU.

   ![Skapa en ny databasinloggning](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Välj **OK** att slutföra distributionen av databasen.

Under **Essentials**, som visas när databasen har distribuerats, anteckna den **anslutningssträngen**. Du kan använda den här strängen i alla program som behöver åtkomst till SQL Server-databasen.

![Hämta anslutningssträngen](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On-databaser

Avsiktligt hanteras alltid på databaser annorlunda än i en miljö med fristående servrar. Mer information finns i [introduktion till SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Verifiera SQL Always On-databaser

Följande skärmdump visar hur du kan använda SQL Server Management Studio för att titta på databasens status i SQL Always On.

![Status för AlwaysOn-databasen](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On-databaser ska visa som Synchronized och tillgänglig på alla SQL-instanser och visas i Tillgänglighetsgrupper. I den föregående skärmbilden i databas-exempel är newdb1 och dess status är **newdb1 (synkroniserad)**.

### <a name="delete-an-alwayson-database"></a>Ta bort en AlwaysOn-databas

När du tar bort en SQL AlwaysOn-databas från resursprovidern raderas SQL database från den primära repliken och från tillgänglighetsgruppen.

SQL sedan placerar databasen i återställningsläge på andra repliker och inte släppa databasen, såvida inte utlöses. Om databasen inte släpps, går de sekundära replikerna i ett tillstånd som inte synkroniseras.

## <a name="next-steps"></a>Nästa steg

[Underhåll av SQL Server-resursleverantör](azure-stack-sql-resource-provider-maintain.md)
