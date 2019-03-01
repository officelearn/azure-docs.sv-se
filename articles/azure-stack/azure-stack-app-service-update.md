---
title: Uppdatera Azure App Service i Azure Stack | Microsoft Docs
description: Detaljerad vägledning för att uppdatera Azure App Service i Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: c993322bdf4266939eab854e217cae6e90326d29
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991571"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Uppdatera Azure App Service i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!IMPORTANT]  
> Uppdateringen är 1901 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.5.

Genom att följa anvisningarna i den här artikeln kan du uppgradera den [App Service-resursprovider](azure-stack-app-service-overview.md) distribueras i en Azure Stack-miljö som är ansluten till Internet.

> [!IMPORTANT]  
> Innan du kör uppgraderingen bör du kontrollera att du redan har slutfört den [distribution av Azure App Service i Azure Stack Resource Provider](azure-stack-app-service-deploy.md) och att du har läst den [viktig](azure-stack-app-service-release-notes-update-five.md) som medföljer 1,5 versionen Läs mer om nya funktioner och korrigeringar kända problem som kan påverka din distribution.

## <a name="run-the-app-service-resource-provider-installer"></a>Kör installationsprogrammet för providern App Service-resurs

Under den här processen kommer uppgraderingen:

* Identifiera tidigare distribution av App Service
* Förbered alla paket och nya versioner av alla OSS bibliotek som ska distribueras
* Ladda upp till Storage
* Uppgradera alla App Service-roller (domänkontrollanter, hantering, klientdel, utgivare och Worker roller)
* Uppdatera definitioner för App Service-skalningsuppsättning
* Uppdatera App Service-Resursprovidermanifest

> [!IMPORTANT]
> App Service-installationsprogrammet måste köras på en dator som kan nå Azure Stack administratör Azure Resource Manager-slutpunkten.
>
>

Följ dessa steg om du vill uppgradera din distribution av App Service på Azure Stack:

1. Ladda ned den [App Service-installationsprogrammet](https://aka.ms/appsvcupdate4installer)

2. Köra appservice.exe som en administratör

    ![App Service-installationsprogrammet][1]

3. Klicka på **distribuera App Service eller uppgradera till den senaste versionen.**

4. Granska och acceptera licensvillkoren för programvara från Microsoft och klickar sedan på **nästa**.

5. Granska och acceptera licensvillkoren från tredje part och klicka sedan på **nästa**.

6. Se till att Azure Stack Azure Resource Manager-slutpunkten och Active Directory-Klientorganisation informationen är korrekt. Om du använde standardinställningarna under distributionen av Azure Stack Development Kit kan godkänna du standardvärdena här. Om du har anpassat alternativ när du har distribuerat Azure Stack, måste du redigera värdena i det här fönstret. Exempel: Om du använder domänsuffixet *mycloud.com*, Azure Stack Azure Resource Manager-slutpunkten måste ändra till *management.region.mycloud.com*. När du har bekräftat din information, klickar du på **nästa**.

    ![Informationen för Azure Stack-molnet][2]

7. På nästa sida:

   1. Klicka på den **Connect** knappen bredvid den **Azure Stack-prenumerationer** box.
        * Om du använder Azure Active Directory (AD Azure), anger du det Azure AD-administratörskonto och lösenord som du angav när du har distribuerat Azure Stack. Klicka på **logga In**.
        * Om du använder Active Directory Federation Services (AD FS), ange ditt administratörskonto. Till exempel *cloudadmin@azurestack.local*. Ange ditt lösenord och klicka på **logga In**.
   2. I den **Azure Stack-prenumerationer** väljer den **standard Providerprenumeration**.
   3. I den **platser för Azure Stack** väljer du den plats som motsvarar den region som du distribuerar till. Välj exempelvis **lokala** om din distribution till Azure Stack Development Kit.
   4. Om en befintlig App Service-distribution har identifierats vara sedan resurskontot grupp och lagring fylls och nedtonat.
   5. Klicka på **nästa** att granska sammanfattningen av uppgraderingen.

    ![App Service-Installation identifierades][3]

8. På sidan Sammanfattning:
   1. Kontrollera de val du gjort. Använd för att göra ändringar i **föregående** knappar för att gå till föregående sidor.
   2. Markera kryssrutan om konfigurationerna är korrekta.
   3. Starta uppgraderingen genom att klicka på **nästa**.

       ![Sammanfattningen av uppgraderingen för App Service][4]

9. Uppgradera sidan för Installationsförlopp:
    1. Spåra uppgraderingsförloppet. Varaktigheten för uppgraderingen av App Service i Azure Stack varierar beroende på antalet rollinstanser som distribuerats.
    2. När uppgraderingen har slutförts klickar du på **avsluta**.

        ![Uppgradera App Service-förlopp][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) tjänster](azure-stack-tools-paas-services.md).

* [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md)
