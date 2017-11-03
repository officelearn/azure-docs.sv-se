---
title: "Hur du skapar och distribuerar en tjänst i molnet | Microsoft Docs"
description: "Lär dig hur du skapar och distribuerar en tjänst i molnet använder metoden Snabbregistrering i Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 2a2172a78bfd3ac923edbc9de366b035629dd27b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hur du skapar och distribuerar en tjänst i molnet
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-create-deploy-portal.md)
> * [Klassisk Azure-portal](cloud-services-how-to-create-deploy.md)
> 
> 

Den klassiska Azure portalen tillhandahåller två sätt att skapa och distribuera en tjänst i molnet: **Snabbregistrering** och **skapa anpassade**.

Det här avsnittet beskriver hur du använder metoden Snabbregistrering för att skapa en ny molntjänst och sedan använda **överför** så här överför och distribuera ett paket med cloud service i Azure. När du använder den här metoden gör tillgängliga praktiska länkar för att slutföra alla krav när du går den klassiska Azure-portalen. Om du är redo att distribuera din molntjänst när du skapar den, kan du göra båda på samma gång med **skapa anpassade**.

> [!NOTE]
> Om du planerar att publicera din molntjänst från Visual Studio Team Services VSTS () använda **Snabbregistrering**, och sedan konfigurera VSTS publicering från **Snabbstart** eller instrumentpanelen.
> 
> 

## <a name="concepts"></a>Koncept
Tre komponenter krävs för att distribuera ett program som en tjänst i molnet i Azure:

* **Tjänstdefinitionen**  
  Molnet tjänstdefinitionsfilen (.csdef) definierar tjänstmodellen, inklusive antalet roller.
* **Tjänstkonfiguration**  
  Molnet tjänstekonfigurationsfilen (.cscfg) innehåller konfigurationsinställningarna för molnet tjänsten och enskilda roller, inklusive antalet rollinstanser.
* **Tjänstpaket**  
  Service-paketet (.cspkg) innehåller programkoden och konfigurationer och tjänstdefinitionsfilen.

Du kan lära dig mer om de här och hur du skapar ett paket [här](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Förbereda din app
Innan du kan distribuera en tjänst i molnet, måste du skapa cloud service-paketet (.cspkg) från din programkod och ett moln tjänstekonfigurationsfilen (.cscfg). Azure SDK innehåller verktyg för att förbereda filerna obligatorisk distribution. Du kan installera SDK från den [Azure hämtar](https://azure.microsoft.com/downloads/) sidan på det språk som du föredrar att utveckla programkoden.

Tre kräver cloud tjänsten särskilda konfigurationer innan du exporterar en tjänstpaket:

* Om du vill distribuera en tjänst i molnet som använder Secure Sockets Layer (SSL) för kryptering av data, [konfigurera ditt program](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) för SSL.
* Om du vill konfigurera anslutningar till fjärrskrivbord till rollinstanser, [konfigurera rollerna](cloud-services-role-enable-remote-desktop.md) för fjärrskrivbord.
* Om du vill konfigurera detaljerad övervakning av Molntjänsten, aktiverar du Azure-diagnostik för Molntjänsten. *Minimal övervakning* (standardinställningar för övervakning nivå) använder prestandaräknare som samlats in från värdoperativsystem för rollinstanser (virtuella datorer). ”Utförlig övervakning * samlar in ytterligare mått baserat på prestandadata i rollinstanser för att aktivera närmare analys av problem som uppstår under bearbetningen av programmet. Om du vill veta hur du aktiverar Azure Diagnostics [aktiverar diagnostik i Azure](cloud-services-dotnet-diagnostics.md).

Om du vill skapa en molntjänst med distributioner av webbroller eller arbetsroller, måste du [skapa tjänstepaketet](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har installerat Azure SDK, klickar du på **installera Azure SDK** att öppna den [Azure hämtar sidan](https://azure.microsoft.com/downloads/), och sedan hämta SDK för det språk som du föredrar att utveckla din kod. (Har du möjlighet att göra det senare.)
* Om alla rollinstanser kräver ett certifikat, skapa certifikat. Molntjänster kräver en .pfx-fil med en privat nyckel. Du kan [överför certifikat till Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) som du skapar och distribuerar Molntjänsten.
* Om du planerar att distribuera Molntjänsten till en tillhörighetsgrupp, skapa tillhörighetsgruppen. Du kan använda en tillhörighetsgrupp för att distribuera din molntjänst och andra Azure-tjänster till samma plats i en region. Du kan skapa tillhörighetsgruppen i den **nätverk** område i den klassiska Azure-portalen Företagsportalen på den **Tillhörighetsgrupper** sidan.

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Så här: skapa en molntjänst med Snabbregistrering
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **ny**>**Compute**>**Molntjänsten**>**Snabbregistrering**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. I **URL**, ange ett underdomänsnamn på ska användas i offentlig URL för åtkomst till Molntjänsten för produktionsdistributioner. URL-formatet för Produktionsdistribution: http://*myURL*. cloudapp.net.
3. I **Region eller Affinitetsgrupp**, Välj den geografiska region eller affinitetsgrupp att distribuera Molntjänsten till. Välj en tillhörighetsgrupp om du vill distribuera din molntjänst på samma plats som andra Azure-tjänster inom en region.
4. Klicka på **Create Cloud Service** (Skapa molntjänst).
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    Du kan övervaka statusen för processen i meddelandeområdet längst ned i fönstret.
   
    Den **molntjänster** område öppnas med ny molntjänst visas. När statusen ändras till skapad, har för att skapa moln slutförts.
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Så här: ladda upp ett certifikat för en tjänst i molnet
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **molntjänster**, klicka på namnet på Molntjänsten och klicka sedan på **certifikat**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. Klicka på antingen **överföra ett certifikat** eller **överför**.
3. I **filen**, använda **Bläddra** att välja certifikatet (.pfx-fil).
4. I **lösenord**, ange den privata nyckeln för certifikatet.
5. Klicka på **OK** (markering).
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    Du kan övervaka status för överföringen i meddelandeområdet visas nedan. När överföringen är klar har certifikatet lagts till i tabellen. I meddelandeområdet klickar du på OK för att stänga meddelandet.
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Så här: distribuera en tjänst i molnet
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **molntjänster**, klicka på namnet på Molntjänsten och klicka sedan på **instrumentpanelen**.
2. Klicka på antingen **ladda upp en ny Produktionsdistribution** eller **överför**.
3. I **distributionsetiketten**, ange ett namn för den nya distributionen – till exempel MyCloudServicev4.
4. I **paketet**, använda **Bläddra** att välja servicepaketfil (.cspkg) ska användas.
5. I **Configuration**, använda **Bläddra** att välja Konfigurera tjänstfilen (.cscfg) ska användas.
6. Om Molntjänsten innehåller några roller med endast en instans, väljer du den **distribuera även om en eller flera roller innehåller en enda instans** kryssrutan för att distributionen ska fortsätta.
   
    Azure kan bara garantera 99,95% åtkomst till Molntjänsten vid underhåll och service uppdateringar om varje roll har minst två instanser. Om det behövs kan du lägga till ytterligare rollinstanser på den **skala** sidan när du har distribuerat Molntjänsten. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).
7. Klicka på **OK** (markering) för att starta tjänsten molndistribution.
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    Du kan övervaka statusen för distributionen i meddelandeområdet. Klicka på OK om du vill dölja meddelandet.
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Kontrollera distributionen har slutförts
1. Klicka på **instrumentpanelen**.
   
    Status ska visa att tjänsten är **kör**.
2. Under **snabböversikten**, klicka på Webbadressen för att öppna din molntjänst i en webbläsare.
   
    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av Molntjänsten](cloud-services-how-to-configure.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name.md).
* [Hantera din molntjänst](cloud-services-how-to-manage.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate.md).

