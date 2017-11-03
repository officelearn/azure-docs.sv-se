---
title: Skapa och anslut till en MySQL-databas i Azure
description: "Lär dig hur du använder Azure-portalen för att skapa en MySQL-databas och anslut sedan till den från en PHP-webbapp i Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Skapa och anslut till en MySQL-databas i Azure
Den här kursen visar hur du skapar en MySQL-databas i den [Azure-portalen](https://portal.azure.com) (providern är [ClearDB](http://www.cleardb.com/)) och hur du ansluter till den från en PHP-webbapp körs i [Azure App Service](app-service/app-service-web-overview.md).

> [!NOTE]
> Du kan också skapa en MySQL-databas som en del av en <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">Marketplace-appmallen</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Skapa en MySQL-databas på Azure-portalen
Om du vill skapa en MySQL-databas på Azure-portalen, gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I den vänstra menyn klickar du på **ny** > **Data + lagring** > **MySQL-databas**.

    ![Skapa en MySQL-databas i Azure - start](./media/store-php-create-mysql-database/create-db-1-start.png)
3. I den nya MySQL-databasen [bladet](azure-portal-overview.md), konfigurera den nya MySQL-databasen på följande sätt (*bladet*: en Portalsida som öppnar vågrätt):

   * **Databasnamn**: Ange en unik namn
   * **Prenumerationen**: Välj prenumerationen för att använda
   * **Typ av databas**: Välj **delade** för låg kostnad eller kostnadsfria nivåer eller **dedikerad** få dedicerade resurser.
   * **Resursgruppen**: Lägg till MySQL-databas till en befintlig [resursgruppen](azure-resource-manager/resource-group-overview.md) eller placera den i en ny. Resurser i samma grupp kan enkelt hanteras tillsammans.
   * **Plats**: Välj en plats nära dig. När du lägger till en befintlig resursgrupp, kan du komma till resursgruppens plats.
   * **Prisnivån**: klickar du på **prisnivån**, Välj en prisnivå alternativ (**Merkurius** nivå är ledigt), och klicka sedan på **Välj**.
   * **Juridiska villkor**: Klicka på **juridiska villkor**, granska inköpsinformation och på **köpa**.
   * **Fäst på instrumentpanelen**: Välj om du vill komma åt direkt från instrumentpanelen. Detta är särskilt användbart om du inte är bekant med portalen navigering ännu.

     Följande skärmbild är bara ett exempel på hur du kan konfigurera MySQL-databas.  
     ![Skapa en MySQL-databas i Azure - konfigurera](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. När du är klar konfigurerar, klickar du på **skapa**.

    ![Skapa en MySQL-databas i Azure - skapa](./media/store-php-create-mysql-database/create-db-3-create.png)

    Du ser ett popup-meddelande postmeddelande med information om att distributionen har startats.

    ![Skapa en MySQL-databas i Azure - pågår](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Du får en annan popup när distributionen har slutförts. Portalen öppnas automatiskt även ditt MySQL-databas-blad.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Ansluta till din MySQL-databas
Om du vill se anslutningsinformationen för den nya MySQL-databasen, klicka bara på **egenskaper** i din webbapps blad.

![Skapa en MySQL-databas i Azure - bladet för MySQL-databas](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Du kan nu använda att anslutningsinformationen i ett webbprogram. Ett exempel som visar hur du använder informationen från en enkel app för PHP finns [här](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="next-steps"></a>Nästa steg
Mer information finns i [PHP Developer Center](/develop/php/).
