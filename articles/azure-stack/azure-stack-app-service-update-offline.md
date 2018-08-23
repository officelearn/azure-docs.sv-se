---
title: Uppdatera Azure App Service Offline | Microsoft Docs
description: Detaljerad vägledning för att uppdatera Azure App Service i Azure Stack offline
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: anwestg
ms.openlocfilehash: f48872d1853dfd4c40022f42c8e237973ac70fe6
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42057643"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Offline uppdatering av Azure App Service i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

> [!IMPORTANT]
> Uppdateringen är 1807 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.3.
>
>

Genom att följa anvisningarna i den här artikeln kan du uppgradera den [App Service-resursprovider](azure-stack-app-service-overview.md) distribueras i en Azure Stack-miljö som är:

* inte ansluten till Internet
* skyddas av Active Directory Federation Services (AD FS).

> [!IMPORTANT]
> Innan du kör uppgraderingen bör du kontrollera att du redan har slutfört den [distribution av Azure App Service i Azure Stack Resource Provider](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Kör installationsprogrammet för providern App Service-resurs

Om du vill uppgradera App Service-resursprovider i Azure Stack-miljön, måste du utföra dessa uppgifter:

1. Ladda ned den [App Service-installationsprogrammet](https://aka.ms/appsvcupdate3installer)
2. Skapa ett offline uppgraderingspaket.
3. Kör installationsprogrammet för App Service (appservice.exe) och slutföra uppgraderingen.

Under den här processen kommer uppgraderingen:

* Identifiera tidigare distribution av App Service
* Ladda upp till Storage
* Uppgradera alla App Service-roller (domänkontrollanter, hantering, klientdel, utgivare och Worker roller)
* Uppdatera definitioner för App Service-skalningsuppsättning
* Uppdatera App Service-Resursprovidermanifest

## <a name="create-an-offline-upgrade-package"></a>Skapa ett offline uppgraderingspaket

Om du vill uppgradera App Service i en frånkopplad miljö måste du först skapa ett offline uppgraderingspaket på en dator som är ansluten till Internet.

1. Köra appservice.exe som en administratör

    ![App Service-installationsprogrammet][1]

2. Klicka på **Avancerat** > **skapa offlinepaket**

    ![App Service-installationsprogrammet avancerade][2]

3. App Service-installationsprogrammet skapar ett offline uppgraderingspaket och visar sökvägen till den.  Du kan klicka på **Öppna mapp** öppna mappen i Utforskaren.

4. Kopiera installationsprogrammet (AppService.exe) och offline uppgraderingspaketet till din Azure Stack-värddatorn.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Slutför uppgraderingen av App Service i Azure Stack

> [!IMPORTANT]
> App Service-installationsprogrammet måste köras på en dator som kan nå Azure Stack-administratören Azure Resource Manager-slutpunkten.
>
>

1. Kör appservice.exe som administratör.

    ![App Service-installationsprogrammet][1]

2. Klicka på **Avancerat** > **Slutför offlineinstallationen eller uppgradera**.

    ![App Service-installationsprogrammet avancerade][2]

3. Bläddra till platsen där offline uppgraderingspaketet som du skapade tidigare och klicka sedan på **nästa**.

4. Granska och acceptera licensvillkoren för programvara från Microsoft och klickar sedan på **nästa**.

5. Granska och acceptera licensvillkoren från tredje part och klicka sedan på **nästa**.

6. Se till att Azure Stack Azure Resource Manager-slutpunkten och Active Directory-Klientorganisation informationen är korrekt. Om du använde standardinställningarna under distributionen av Azure Stack Development Kit kan godkänna du standardvärdena här. Om du har anpassat alternativ när du har distribuerat Azure Stack, måste du redigera värdena i det här fönstret för att avspegla. Exempel: Om du använder domänsuffixet *mycloud.com*, Azure Stack Azure Resource Manager-slutpunkten måste ändra till *management.region.mycloud.com*. När du har bekräftat din information, klickar du på **nästa**.

    ![Informationen för Azure Stack-molnet][3]

7. På nästa sida:

   1. Klicka på den **Connect** knappen bredvid den **Azure Stack-prenumerationer** box.
        * Om du använder Azure Active Directory (AD Azure), anger du det Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure Stack. Klicka på **logga In**.
        * Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel *cloudadmin@azurestack.local*. Ange ditt lösenord och klicka på **logga In**.
   2. I den **Azure Stack-prenumerationer** väljer den **standard Providerprenumeration**.
   3. I den **platser för Azure Stack** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure Stack Development Kit.
   4. Om en befintlig App Service-distribution har identifierats vara sedan resurskontot grupp och lagring fylls och nedtonat.
   5. Klicka på **nästa** att granska sammanfattningen av uppgraderingen.

    ![App Service-Installation identifierades][4]

8. På sidan Sammanfattning:
   1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att gå till föregående sidor.
   2. Markera kryssrutan om konfigurationerna är korrekta.
   3. Starta uppgraderingen genom att klicka på **nästa**.

       ![Sammanfattningen av uppgraderingen för App Service][5]

9. Uppgradera sidan för Installationsförlopp:
    1. Spåra uppgraderingsförloppet. Varaktigheten för uppgraderingen av App Service i Azure Stack varierar beroende på antalet rollinstanser som distribuerats.
    2. När uppgraderingen har slutförts klickar du på **avsluta**.

        ![Uppgradera App Service-förlopp][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) tjänster](azure-stack-tools-paas-services.md).

* [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md)
