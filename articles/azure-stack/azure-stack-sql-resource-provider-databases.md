---
title: Med hjälp av databaser som tillhandahålls av SQL-kort RP Azure stacken | Microsoft Docs
description: Hur du skapar och hanterar SQL-databaser som etablerats med hjälp av Resource Provider för SQL-kort
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b9f92b4d85e17bc848d82be413df1d0dad7c8548
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294946"
---
# <a name="create-sql-databases"></a>Skapa SQL-databaser
Självbetjäning databaser har du tillgång till användarportalen. En Azure-stacken användare behöver en prenumeration som har ett erbjudande som innehåller tjänsten SQL-databasen.

1. Logga in på den [Azure Stack](azure-stack-poc.md) användarportalen (service-administratörer kan också använda administrationsportal).

2. Klicka på **+ ny** &gt; **Data + lagring ”** &gt; **SQL Server-databas** &gt; **lägga till**.

3. Fyll i formuläret med databasinformation, inklusive en **databasnamnet**, **maxstorleken**, och ändra de andra parametrarna efter behov. Du uppmanas att välja en SKU för din databas. När värdservrar läggs tilldelats de en SKU. Databaser skapas i den poolen, vara värd för servrar som utgör SKU: N.

  ![Ny databas](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Databasens storlek måste vara minst 64 MB. Det kan ökas med inställningar.

4. Fyll i de inloggningsinställningar: **databasinloggningen**, och **lösenord**. Dessa inställningar är SQL-autentisering-autentiseringsuppgifter som har skapats för din åtkomst till endast den här databasen. Användarens inloggningsnamn måste vara globalt unika. Skapa en ny inställning för inloggning eller välj en befintlig. Du kan återanvända inloggningsinställningar för andra databaser som använder samma SKU: N.

    ![Skapa en ny databasinloggning](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Skicka formuläret och vänta på att distributionen ska slutföras.

    Observera fältet ”anslutningssträngen” i det resulterande bladet. Du kan använda denna sträng i alla program som kräver SQL Server-åtkomst (till exempel en webbapp) i Azure-stacken.

    ![Hämta anslutningssträngen](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>Ta bort SQL AlwaysOn-databaser
När SQL AlwaysOn-databasen tas bort från resursprovidern tagits bort har från den primära servern och AlwaysOn-tillgänglighetsgruppen grupp, det men placerar databasen i återställningsläge i varje replik av Design, SQL AG och släppa inte databasen om utlöses. Om en databas inte tas bort går de sekundära replikerna att inte synkronisera tillstånd. Om du lägger till en ny databas för AG med samma via RP fortfarande fungerar.

## <a name="verify-sql-alwayson-databases"></a>Verifiera SQL AlwaysOn-databaser
AlwaysOn-databaser ska visas som synkroniserats och är tillgängliga på alla instanser och i tillgänglighetsgruppen. Databasen bör sömlöst ansluta efter redundans. Du kan använda SQL Server Management Studio för att verifiera att en databas synkroniseras:

![Kontrollera AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>Nästa steg

[Underhåll av SQL Server-resursprovidern](azure-stack-sql-resource-provider-maintain.md)
