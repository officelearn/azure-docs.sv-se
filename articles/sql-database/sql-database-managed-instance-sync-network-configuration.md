---
title: Azure App Service – synkronisera nätverkskonfiguration | Microsoft Docs
description: Den här artikeln beskrivs hur du synkroniserar din nätverkskonfiguration för Azure App Service-värdplan.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 57c4dd523a5dffc48a2d2d403d2a440a8d6cde95
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257910"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synkronisera nätverkskonfiguration för Azure App Service-värdplan

Det kan hända att även om du [integrerat din app med Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), du kan inte upprätta anslutning till hanterad instans. En sak som du ska uppdatera nätverkskonfigurationen för din serviceplan. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synkronisera nätverkskonfiguration för App Service-värdplanen

Det gör du genom att följa dessa steg:  

1. Gå till web apps App Service-plan.
 
   ![apptjänstplan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klicka på **nätverk** och klicka sedan på **Klicka här för att hantera**.
 
   ![hantera service-plan](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Välj din **VNet** och klicka på **synkronisera nätverk**. 
 
   ![Synkronisera nätverk](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Vänta tills synkroniseringen är klar.
  
   ![synkronisering är klar](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Du är nu redo att testa att återupprätta anslutningen till din hanterade instans.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt virtuella nätverk för hanterad instans finns i [konfiguration av hanterad instans virtuellt nätverk](sql-database-managed-instance-vnet-configuration.md).
