---
title: Hur du skapar och distribuerar en tjänst i molnet | Microsoft Docs
description: Lär dig hur du skapar och distribuerar en tjänst i molnet med Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: a6cf2276da463f71f008c4bfb6eee4c232b18308
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433783"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Skapa och distribuera en molntjänst
Azure-portalen ger dig två sätt att skapa och distribuera en tjänst i molnet: *Snabbregistrering* och *skapa anpassade*.

Den här artikeln förklarar hur du använder Snabbregistrering för att skapa en ny molntjänst och sedan använda **överför** att ladda upp och distribuera ett molntjänstpaket i Azure. När du använder den här metoden gör tillgängliga lättillgängliga länkar för att slutföra alla krav när du går i Azure-portalen. Om du är redo att distribuera din molntjänst när du skapar den, kan du göra både på samma gång med att skapa anpassade.

> [!NOTE]
> Om du planerar att publicera din molntjänst från Azure DevOps, Använd Snabbregistrering och sedan ställa in Azure DevOps-publicering från Azure-Snabbstart eller instrumentpanelen. Mer information finns i [kontinuerlig leverans till Azure med hjälp av Azure DevOps][TFSTutorialForCloudService], eller se hjälp för den **Snabbstart** sidan.
>
>

## <a name="concepts"></a>Begrepp
Tre komponenter krävs för att distribuera ett program som en molntjänst i Azure:

* **Tjänstdefinition**  
  Molnet tjänstdefinitionsfilen (.csdef) definierar tjänstmodellen, inklusive antalet roller.
* **Tjänstkonfiguration**  
  Molntjänstkonfigurationsfilen (.cscfg) innehåller konfigurationsinställningarna för molnet tjänsten och enskilda roller, inklusive antalet rollinstanser.
* **Tjänstpaket**  
  Service-paketet (.cspkg) innehåller programkoden och konfigurationer och tjänstdefinitionsfilen.

Du kan lära dig mer om de här och hur du skapar ett paket [här](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Förbereda din app
Innan du kan distribuera en tjänst i molnet, måste du skapa molntjänstpaketet (.cspkg) från din programkod och en molntjänstkonfigurationsfilen (.cscfg). Azure SDK innehåller verktyg för att förbereda filerna nödvändig distribution. Du kan installera SDK: N från den [Azure hämtar](https://azure.microsoft.com/downloads/) sidan på det språk som du föredrar att utveckla din programkod.

Tre cloud service-funktioner kräver särskilda konfigurationer innan du exporterar ett tjänstpaket:

* Om du vill distribuera en molntjänst som använder Secure Sockets Layer (SSL) för kryptering av data, [konfigurerar programmet](cloud-services-configure-ssl-certificate-portal.md#modify) för SSL.
* Om du vill konfigurera fjärrskrivbordsanslutningar till rollinstanser, [konfigurera rollerna](cloud-services-role-enable-remote-desktop-new-portal.md) för fjärrskrivbord.
* Om du vill konfigurera utförlig övervakning för cloud Services, aktiverar du Azure Diagnostics för Molntjänsten. *Minimal övervakning* (standard övervakning nivå) använder prestandaräknare som samlats in från värdoperativsystem för rollinstanser (virtuella datorer). *Utförlig övervakning* samlar in ytterligare mått baserat på prestandadata i rollinstanser för att aktivera närmare analys av problem som uppstår under bearbetningen av programmet. Om du vill veta hur du aktiverar Azure Diagnostics [aktivera diagnostik i Azure](cloud-services-dotnet-diagnostics.md).

Om du vill skapa en molntjänst med distributioner av web-roller eller worker-roller måste du [skapa tjänstpaketet](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har installerat Azure SDK, klickar du på **installera Azure SDK** att öppna den [Azure hämtar sidan](https://azure.microsoft.com/downloads/), och sedan ladda ned SDK för det språk som du föredrar att utveckla din kod. (Har du möjlighet att göra det senare.)
* Om alla rollinstanser kräver ett certifikat, skapa certifikat. Molntjänster kräver en .pfx-fil med en privat nyckel. Du kan ladda upp certifikat till Azure när du skapar och distribuerar Molntjänsten.

## <a name="create-and-deploy"></a>Skapa och distribuera
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **skapa en resurs > Compute**, och bläddra till och klickar på **molntjänst**.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. I den nya **molntjänst** rutan Ange ett värde för den **DNS-namnet**.
4. Skapa en ny **resursgrupp** eller välj en befintlig.
5. Välj en **Plats**.
6. Klicka på **paketet**. Då öppnas det **överföra ett paket** fönstret. Fyll i fälten som krävs. Om någon av dina roller innehåller en enda instans, se till att **distribuera även om en eller flera roller innehåller en enda instans** har valts.
7. Se till att **starta distribution** har valts.
8. Klicka på **OK** som stängs den **överföra ett paket** fönstret.
9. Om du inte har några certifikat för att lägga till, klickar du på **skapa**.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Ladda upp ett certifikat
Om distributionspaket var [konfigurerad för att använda certifikat](cloud-services-configure-ssl-certificate-portal.md#modify), du kan ladda upp certifikatet nu.

1. Välj **certifikat**, och på den **lägga till certifikat** fönstret Välj filen SSL certificate .pfx och ange sedan den **lösenord** för certifikatet
2. Klicka på **bifoga certifikat**, och klicka sedan på **OK** på den **lägga till certifikat** fönstret.
3. Klicka på **skapa** på den **molntjänst** fönstret. När distributionen har nått den **redo** status, kan du fortsätta till nästa steg.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Kontrollera distributionen har slutförts
1. Klicka på molntjänstinstans.

    Statusen ska visa att tjänsten är **kör**.
2. Under **Essentials**, klickar du på den **webbplatsens URL** att öppna din molntjänst i en webbläsare.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration för din molntjänst](cloud-services-how-to-configure-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate-portal.md).
