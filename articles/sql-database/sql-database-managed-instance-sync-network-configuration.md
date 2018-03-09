---
title: "Azure Apptjänst - sync nätverkskonfigurationen | Microsoft Docs"
description: "Den här artikeln beskrivs hur du synkroniserar din nätverkskonfiguration för Azure App Service som värd för planen."
ms.service: sql-database
author: srdjan-bozovic
manager: cguyer
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1968f736dbfc24495e4e932f9c8c5955dc607133
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synkronisera nätverkskonfigurationen för Azure App Service som värd för planen

Det kan hända att även om du [integrerad appen med ett Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), du kan inte upprätta anslutningen till instansen hanteras. Vad du kan försöka är att uppdatera nätverkskonfigurationen för service-plan. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synkronisera nätverkskonfiguration för App Service som värd för planen

Det gör du genom att följa dessa steg:  

1. Gå till dina webbprogram App Service-plan.
 
   ![apptjänstplan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klicka på **nätverk** och klicka sedan på **Klicka här för att hantera**.
 
   ![hantera service-plan](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Välj din **VNet** och på **Sync nätverket**. 
 
   ![Synkronisera nätverk](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Vänta tills synkroniseringen är klar.
  
   ![synkronisering klar](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Du är nu redo att försök att återupprätta anslutningen till din hanteras instans.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt VNet för hanterade instansen finns [konfigurationen hanteras instans virtuellt nätverk](sql-database-managed-instance-vnet-configuration.md).
