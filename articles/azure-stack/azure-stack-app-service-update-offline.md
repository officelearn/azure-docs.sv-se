---
title: Uppdatera Azure App Service Offline | Microsoft Docs
description: "Detaljerad vägledning för att uppdatera Azure App Service på Azure-stacken offline"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 61a3169229cc121c078a934f6b979bdaffafd565
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Offline uppdatering av Azure App Service på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

> [!IMPORTANT]
> Uppdateringen är 1802 Azure Stack integrerade systemet eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service.
>
>

Genom att följa anvisningarna i den här artikeln kan du uppgradera den [Apptjänst resursprovidern](azure-stack-app-service-overview.md) distribuerats i en miljö med Azure-stacken som är:

* inte ansluten till Internet
* skyddas av Active Directory Federation Services (AD FS).

> [!IMPORTANT]
> Innan du kör uppgraderingen bör du kontrollera att du redan har slutfört den [distribution av Azure App Service på Azure-stacken Resource Provider](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Kör installationsprogrammet för App Service resource provider

Om du vill uppgradera App-tjänstresursprovider i en Azure-Stack-miljö måste du utföra dessa uppgifter:

1. Hämta den [Apptjänst Installer](https://aka.ms/appsvcupdate1installer)
2. Skapa ett uppgraderingspaket för offline.
3. Kör installationsprogrammet för App Service (appservice.exe) och slutföra uppgraderingen.

Under den här processen kommer uppgraderingen:

* Identifiera tidigare distribution av App Service
* Överföra till lagring
* Uppgradera alla roller för App Service (domänkontrollanter, hantering, frontend, utgivare och Worker roller)
* Uppdatera definitioner för Apptjänst scale set
* Uppdatera Providermanifestet för Apptjänst-resurs

## <a name="create-an-offline-upgrade-package"></a>Skapa ett uppgraderingspaket för offline

Om du vill uppgradera Apptjänst i en frånkopplad miljö måste du först skapa ett uppgraderingspaket för offline på en dator som är ansluten till Internet.

1. Köra appservice.exe som administratör

    ![App Service Installer][1]

2. Klicka på **Avancerat** > **Skapa offline-paket**

    ![Apptjänst Installer avancerade][2]

3. Installationsprogrammet för App Service skapar ett uppgraderingspaket för offline och visar sökvägen till den.  Du kan klicka på **öppna mappen** att öppna mappen i din Utforskaren.

4. Kopiera installationsprogrammet (AppService.exe) och offline uppgraderingspaketet till din Azure-stacken värddatorn.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Slutför uppgraderingen av Apptjänst i Azure-stacken

> [!IMPORTANT]
> Apptjänst installationsprogrammet måste köras på en dator som kan nå Azure Stack administratör Azure Resource Manager-slutpunkt.
>
>

1. Kör appservice.exe som administratör.  

    ![App Service Installer][1]

2. Klicka på **Avancerat** > **slutföra offline installationen eller uppgraderingen av**.

    ![Apptjänst Installer avancerade][2]

3. Bläddra till platsen där offline uppgraderingspaketet som du skapade tidigare och klicka sedan på **nästa**.

4. Granska och acceptera licensvillkoren för programvara från Microsoft och klicka sedan på **nästa**.

5. Granska och acceptera licensvillkoren för tredjeparts- och klicka sedan på **nästa**.

6. Kontrollera att Azure Stack Azure Resource Manager-slutpunkten och Active Directory-klient informationen är korrekt. Du kan acceptera standardvärdena här om du använder standardinställningarna under distributionen av Azure-stacken Development Kit. Om du har anpassat alternativen när du har distribuerat Azure-stacken, måste du redigera värdena i det här fönstret för att återspegla som. Till exempel om du använder domänsuffixet *mycloud.com*, Azure Stack Azure Resource Manager-slutpunkten måste ändra till *management.region.mycloud.com*. När du bekräftar din information klickar du på **nästa**.

    ![Moln för Azure Stackinformation][3]

7. På nästa sida:

   1. Klicka på den **Anslut** knappen bredvid den **Azure Stack-prenumerationer** rutan.
        * Om du använder Azure Active Directory (Azure AD), anger du Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure Stack. Klicka på **logga In**.
        * Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel  *cloudadmin@azurestack.local* . Ange ditt lösenord och klicka på **logga In**.
   2. I den **Azure Stack-prenumerationer** väljer din prenumeration.
   3. I den **Azure Stack platser** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure-stacken Development Kit.
   4. Om en befintlig App Service-distribution har identifierats vara sedan resurs grupp och lagring kontot fylls och nedtonat.
   5. Klicka på **nästa** att granska sammanfattningen av uppgraderingen.

    ![Installationen av App Service upptäcktes][4]

8. På sidan Sammanfattning:
   1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att besöka föregående sidor.
   2. Markera kryssrutan om konfigurationerna är korrekta.
   3. Starta uppgraderingen genom att klicka på **nästa**.

       ![Uppgradera App Service-sammanfattning][5]

9. Uppgradera sidan för Installationsförlopp:
    1. Spåra uppgradering pågår. Varaktighet för uppgradering av Apptjänst i Azure-stacken varierar beroende på antalet rollinstanser som distribuerats.
    2. När uppgraderingen har slutförts klickar du på **avsluta**.

        ![App Service Upgrade Progress][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) services](azure-stack-tools-paas-services.md).

* [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md)
