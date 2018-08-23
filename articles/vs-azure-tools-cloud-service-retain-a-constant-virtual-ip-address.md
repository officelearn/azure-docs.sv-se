---
title: Hur behåller jag en konstant virtuella IP-adress för en Azure-molntjänst | Microsoft Docs
description: Lär dig att se till att virtuella IP-adressen (VIP) för Azure-Molntjänsten inte ändras.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 2f82663f6b53c6d4e32b8d655dcbd67a321d91ed
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42062070"
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Behålla en konstant virtuella IP-adress för en Azure cloud Services
När du uppdaterar en molnbaserad tjänst som ligger i Azure, kan du behöva se till att den virtuella IP-adressen (VIP) för tjänsten inte ändras. Många tjänster för domänen använda Domain Name System (DNS) för att registrera domännamn. DNS fungerar bara om VIP-Adressen förblir densamma. Du kan använda den **Publiceringsguiden** i Azure-verktyg för att se till att inte VIP för din molntjänst ändras när du uppdaterar den. Läs mer om hur du använder hantering av DNS-domän för cloud services, [konfigurera ett anpassat domännamn för en Azure cloud Services](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publicera en tjänst i molnet utan att ändra sin VIP
VIP för en molntjänst tilldelas när du först distribuera den till Azure i en viss miljö, till exempel produktionsmiljön. VIP-Adressen ändras om du tar bort distributionen explicit eller distributionen raderas implicit av distributionsprocessen för uppdateringen. Om du vill behålla VIP kan du ta bort inte distributionen och du måste se till att Visual Studio inte ta bort distributionen automatiskt. 

Du kan ange inställningar i den **Publiceringsguiden**, som har stöd för flera distributionsalternativ. Du kan ange en ny distribution eller en uppdateringsdistribution som kan vara inkrementella eller samtidig. Båda typerna av uppdateringsdistributionen behåller VIP. Definitioner av dessa olika typer av distributionen finns [Publiceringsguiden för Azure Application](vs-azure-tools-publish-azure-application-wizard.md). Du kan dessutom styra om föregående distributionen av en molntjänst tas bort om ett fel inträffar. Om det alternativet inte korrekt, kan VIP ändras oväntat.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Uppdatera en tjänst i molnet utan att ändra sin VIP
1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio. 

2. I **Solution Explorer**, högerklicka på projektet. På snabbmenyn väljer **publicera**.

    ![Menyn Publish (Publicera)](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. I den **publicera Azure-program** dialogrutan väljer du den Azure-prenumeration som du vill distribuera. Logga in om nödvändigt och väljer **nästa**.

    ![Publicera Azure Application inloggningssida](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. På den **gemensamma inställningar för** kontrollerar som namnet på molnet service för att som du distribuerar, **miljö**, **versionskonfiguration**, och **Tjänstkonfiguration** är korrekta.

    ![Publicera gemensamma inställningar för Azure-program](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. På den **avancerade inställningar** fliken, kontrollera att den **distributionsetiketten** och **lagringskonto** är korrekta. Kontrollera att den **ta bort distributionen vid fel** kryssrutan är avmarkerad och kontrollera att den **uppdatering av programdistribution** kryssrutan är markerad. Genom att avmarkera den **ta bort distributionen vid misslyckande** kan du se till att dina VIP inte förlorade om ett fel uppstår under distributionen. Genom att välja den **uppdatering av programdistribution** kan du se till att distributionen tas inte bort och dina VIP inte är förlorade när du publicerar ditt program. 

    ![Publicera Azure Application avancerade inställningar-fliken](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Välj för att ange hur du vill att rollerna som ska uppdateras ytterligare **inställningar** bredvid **uppdatering av programdistribution**. Välj antingen **inkrementell uppdatering** eller **samtidig uppdatering**, och välj **OK**. Välj **inkrementell uppdatering** uppdatera varje instans av ditt program efter varandra, så att programmet alltid är tillgänglig. Välj **samtidig uppdatering** att uppdatera alla instanser av ditt program på samma gång. Samtidig uppdatering är snabbare, men tjänsten kanske inte är tillgängliga under uppdateringen. När du är klar väljer du **nästa**.

    ![Publicera Azure programinställningar för distributionen sida](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. I den **publicera Azure-program** dialogrutan **nästa** tills den **sammanfattning** visas. Verifiera dina inställningar och välj sedan **publicera**.
   
    ![Publicera Azure Application sammanfattningssida](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Nästa steg
- [Med Visual Studio Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md)

