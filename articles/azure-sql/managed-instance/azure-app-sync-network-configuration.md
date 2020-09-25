---
title: Synkronisera nätverks konfiguration för Azure App Service
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln beskriver hur du synkroniserar nätverks konfigurationen för Azure App Service hosting plan med din Azure SQL-hanterade instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/13/2018
ms.openlocfilehash: fc77b73f7e284dcaacdc64fdc7e3c72579d2e135
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283735"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Synkronisera nätverks konfiguration för Azure App Service värd plan med Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Det kan hända att även om du [integrerar din app med en Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md)kan du inte upprätta en anslutning till SQL-hanterad instans. Uppdaterar eller synkroniserar nätverks konfigurationen för din tjänst plan kan lösa det här problemet. 

## <a name="sync-network-configuration"></a>Synkronisera nätverkskonfiguration 

Det gör du genom att följa dessa steg:  

1. Gå till webbappar App Service plan.

   ![Skärm bild av App Service plan](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Välj **nätverk** och välj sedan **Klicka här för att hantera**.

   ![Skärm bild av hantera tjänste plan](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Välj ditt **VNet** och klicka på **Synkronisera nätverk**.

   ![Skärm bild av Sync-nätverk](./media/azure-app-sync-network-configuration/sync.png)

4. Vänta tills synkroniseringen är färdig.
  
   ![Skärm bild av synkronisering färdig](./media/azure-app-sync-network-configuration/sync-done.png)

Nu kan du försöka återupprätta anslutningen till din SQL-hanterade instans.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt VNet för SQL-hanterad instans finns i [SQL Managed instance VNet-arkitektur](connectivity-architecture-overview.md) och [hur du konfigurerar befintligt VNet](vnet-existing-add-subnet.md).
