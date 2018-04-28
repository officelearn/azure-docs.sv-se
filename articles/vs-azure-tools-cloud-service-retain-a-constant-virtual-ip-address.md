---
title: Hur du bevara ett konstant virtuella IP-adressen för en Azure-molntjänst | Microsoft Docs
description: Lär dig mer om att se till att den virtuella IP-adressen (VIP) för din Azure-molntjänst inte ändras.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: c02e80a97fe5943019f446299e370bf029a01ff4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Bevara ett konstant virtuella IP-adressen för en Azure-molntjänst
När du uppdaterar en molnbaserad tjänst som finns i Azure kan du behöva se till att den virtuella IP-adressen (VIP) för tjänsten inte ändras. Många domän hanteringstjänster använda Domain Name System (DNS) för att registrera domännamn. DNS fungerar bara om VIP är densamma. Du kan använda den **Publiceringsguiden** i Azure-verktyg för att se till att VIP för Molntjänsten inte ändras när du uppdaterar den. Mer information om hur du använder DNS-domän management för molntjänster finns [konfigurera ett anpassat domännamn för en Azure-molntjänst](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publicera en tjänst i molnet utan att ändra dess VIP
När du först distribuera den till Azure i en viss miljö, till exempel produktionsmiljön allokeras VIP för en tjänst i molnet. VIP ändras bara om du har tagit bort distributionen explicit eller raderas implicit distributionen av uppdateringen distributionsprocessen. Om du vill behålla VIP måste du inte ta bort distributionen och måste du se till att Visual Studio inte ta bort distributionen automatiskt. 

Du kan ange inställningar för distribution i den **Publiceringsguiden**, som stöder flera distributionsalternativ. Du kan ange en ny distribution eller uppdatera-distributioner som kan vara inkrementell eller samtidig. Båda typerna av distribution behålla VIP. Definitioner för dessa olika typer av distribution finns i [Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md). Du kan dessutom styra om tidigare distributionen av en molntjänst tas bort om ett fel inträffar. Om du inte alternativet korrekt ändras VIP oväntat.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Uppdatera en tjänst i molnet utan att ändra dess VIP
1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio. 

2. I **Solution Explorer**, högerklicka på projektet. Välj på snabbmenyn **publicera**.

    ![Menyn Publish (Publicera)](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. I den **publicera Azure-programmet** dialogrutan Välj Azure-prenumeration som du vill distribuera. Logga in om nödvändigt och välj **nästa**.

    ![Publicera Azure program inloggningssida](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. På den **gemensamma inställningar för** kontrollerar som namnet på molnet tjänsten till som du distribuerar, **miljö**, **versionskonfiguration**, och **tjänstkonfiguration** är korrekta.

    ![Publicera gemensamma inställningar för Azure-program](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. På den **avancerade inställningar** kontrollerar du att den **distributionsetiketten** och **lagringskonto** är korrekta. Kontrollera att den **ta bort distributionen på fel** kryssrutan är avmarkerad och kontrollera att den **uppdatering av programdistribution** är markerad. Genom att avmarkera den **ta bort distributionen på fel** kan du se till att din VIP försvinner inte om ett fel uppstår under distributionen. Genom att välja den **uppdatering av programdistribution** kan du se till att distributionen tas inte bort och din VIP försvinner inte när du publicerar ditt program. 

    ![Publicera fliken Avancerade inställningar för Azure-program](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Välj för att ange hur du vill att rollerna som ska uppdateras ytterligare **inställningar** bredvid **uppdatering av programdistribution**. Välj antingen **stegvis uppdatering** eller **samtidig uppdatering**, och välj **OK**. Välj **stegvis uppdatering** uppdatera varje instans av ditt program efter varandra, så att programmet alltid är tillgängligt. Välj **samtidig uppdatering** att uppdatera alla instanser av programmet på samma gång. Samtidig uppdatering är snabbare, men tjänsten kanske inte är tillgängliga under uppdateringen. När du är klar väljer du **nästa**.

    ![Publicera Azure inställningar för programdistribution sida](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. I den **publicera Azure-programmet** dialogrutan **nästa** tills den **sammanfattning** visas. Verifiera inställningarna och välj sedan **publicera**.
   
    ![Publicera Azure program sammanfattningssida](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Nästa steg
- [Med Visual Studio Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md)

