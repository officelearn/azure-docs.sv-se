---
title: Azure App Service – synkronisera nätverkskonfiguration | Microsoft Docs
description: Den här artikeln beskrivs hur du synkroniserar din nätverkskonfiguration för Azure App Service-värdplan.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 03/07/2018
ms.openlocfilehash: d5de908166e8de1d45a36f97aee8934653e59623
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163187"
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
