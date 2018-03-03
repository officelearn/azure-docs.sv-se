---
title: "Med hjälp av databaser som tillhandahålls av SQL-kort RP Azure stacken | Microsoft Docs"
description: "Hur du skapar och hanterar SQL-databaser som etablerats med hjälp av Resource Provider för SQL-kort"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 39f6cc30191f07a7c891446a9132222a6d264dc4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="create-sql-databases"></a>Skapa SQL-databaser

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Självbetjäning databaser tillhandahålls via Företagsportalen användarupplevelsen. En användare behöver en prenumeration som har ett erbjudande som innehåller database-tjänsten.

1. Logga in på den [Azure Stack](azure-stack-poc.md) användarportalen (service-administratörer kan också använda administrationsportal).

2. Klicka på **+ ny** &gt; **Data + lagring ”** &gt; **SQL Server-databas (förhandsgranskning)** &gt; **Lägg till**.

3. Fyll i formuläret med databasinformation, inklusive en **databasnamnet**, **maxstorleken**, och ändra de andra parametrarna efter behov. Du uppmanas att välja en SKU för din databas. När värdservrar läggs tilldelats de en SKU. Databaser skapas i den poolen, vara värd för servrar som utgör SKU: N.

  ![Ny databas](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Databasens storlek måste vara minst 64 MB. Det kan ökas med inställningar.

4. Fyll i de inloggningsinställningar: **databasinloggningen**, och **lösenord**. Dessa inställningar är SQL-autentisering-autentiseringsuppgifter som har skapats för din åtkomst till endast den här databasen. Användarens inloggningsnamn måste vara globalt unika. Skapa en ny inställning för inloggning eller välj en befintlig. Du kan återanvända inloggningsinställningar för andra databaser som använder samma SKU: N.

    ![Skapa en ny databasinloggning](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Skicka formuläret och vänta på att distributionen ska slutföras.

    Observera fältet ”anslutningssträngen” i det resulterande bladet. Du kan använda denna sträng i alla program som kräver SQL Server-åtkomst (till exempel en webbapp) i Azure-stacken.

    ![Hämta anslutningssträngen](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>Ta bort SQL-databaser
Från portalen

>[!NOTE]
>
>När SQL AlwaysOn-databasen tas bort från RP tagits bort har från den primära servern och AlwaysOn-tillgänglighetsgruppen det men placerar databasen i återställningsläge i varje replik av Design SQL AG och släppa inte databasen om utlöses. Om en databas inte tas bort går de sekundära replikerna att inte synkronisera tillstånd. Om du lägger till en ny databas för AG med samma via RP fortfarande fungerar. Se ![tar bort en sekundär databas](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Hantera Databasautentiseringsuppgifter
Du kan uppdatera Databasautentiseringsuppgifter (inloggningsinställningar).

## <a name="verify-sql-alwayson-databases"></a>Verifiera SQL AlwaysOn-databaser
AlwaysOn-databaser ska visas som synkroniserats och är tillgängliga på alla instanser och i tillgänglighetsgruppen. Databasen bör sömlöst ansluta efter redundans. Du kan använda SQL Server Management Studio för att verifiera att en databas synkroniseras:

![Verify AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
