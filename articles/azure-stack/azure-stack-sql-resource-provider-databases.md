---
title: Med hjälp av databaser som tillhandahålls av SQL kortet resursprovidern Azure stacken | Microsoft Docs
description: Hur du skapar och hanterar SQL-databaser som etablerats med hjälp av SQL kortet resursprovidern
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 56d21b76268f94f4254985a6924c4ca2d778a9cd
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300830"
---
# <a name="create-sql-databases"></a>Skapa SQL-databaser

Du kan skapa och hantera självbetjäning databaser i användarportalen. En Azure-stacken användare behöver en prenumeration med ett erbjudande som innehåller tjänsten SQL-databasen.

1. Logga in på den [Azure Stack](azure-stack-poc.md) användarportalen.

2. Välj **+ ny** &gt; **Data + lagring** &gt; **SQL Server-databas** &gt; **lägga till**.

3. Under **Create Database**, ange informationen som krävs, t.ex **databasnamnet** och **Max Size i MB**.

   >[!NOTE]
   >Databasens storlek måste vara minst 64 MB, vilket du kan öka när du har distribuerat databasen.

   Konfigurera andra inställningar som krävs för din miljö.

4. Under **Create Database**väljer **SKU**. Under **väljer en SKU**, Välj SKU för din databas.

   ![Skapa databas](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >De är tilldelade en SKU när värdservrar läggs till Azure-stacken. Du skapa databaser i poolen med värd-servrar i en SKU.

5. Välj **inloggning**.
6. Under **väljer en inloggning**, välja en befintlig inloggning eller välja **+ skapa en ny inloggning**.
7. Under **ny inloggning**, ange ett namn för **databasinloggningen** och en **lösenord**.

   >[!NOTE]
   >Dessa inställningar är SQL-autentisering-autentiseringsuppgifter som har skapats för din åtkomst till endast den här databasen. Användarens inloggningsnamn måste vara globalt unika. Du kan återanvända inloggningsinställningar för andra databaser som använder samma SKU: N.

   ![Skapa en ny databasinloggning](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Välj **OK** att slutföra distributionen av databasen.

Under **Essentials**, som visas när databasen har distribuerats, notera den **anslutningssträngen**. Du kan använda den här strängen i alla program som behöver åtkomst till SQL Server-databasen.

![Hämta anslutningssträngen](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On-databaser

Som standard hanteras alltid på databaser annorlunda än i en miljö med fristående servrar. Mer information finns i [introduktion till SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Kontrollera SQL Always On-databaser

Följande skärmdump visar hur du kan använda SQL Server Management Studio för att titta på databasens status i SQL Always On.

![Tillståndet för AlwaysOn-databasen](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On databaser ska visa som Synchronized och är tillgängliga på alla SQL-instanser och visas i Tillgänglighetsgrupper. I den föregående skärmbilden databasen exempel är newdb1 och dess status är **newdb1 (synkroniserad)**.

### <a name="delete-an-alwayson-database"></a>Ta bort en AlwaysOn-databas

När du tar bort en SQL AlwaysOn-databas från resursprovidern SQL databasen från den primära repliken och bort från tillgänglighetsgruppen.

SQL-sedan placerar databasen i återställningsläge på andra replikerna och släppa inte databasen om utlöses. Om databasen inte släpps, går de sekundära replikerna i ett tillstånd som inte synkroniseras.

## <a name="next-steps"></a>Nästa steg

[Underhåll av SQL Server-resursprovidern](azure-stack-sql-resource-provider-maintain.md)
