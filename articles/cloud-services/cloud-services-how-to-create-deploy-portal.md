---
title: "Hur du skapar och distribuerar en tjänst i molnet | Microsoft Docs"
description: "Lär dig hur du skapar och distribuerar en tjänst i molnet med Azure-portalen."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 020e2996f40ed4a48affd3776e44e382c40cb3c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hur du skapar och distribuerar en tjänst i molnet
Azure portal tillhandahåller två sätt att skapa och distribuera en tjänst i molnet: *Snabbregistrering* och *skapa anpassade*.

Den här artikeln förklarar hur du använder metoden Snabbregistrering för att skapa en ny molntjänst och sedan använda **överför** så här överför och distribuera ett paket med cloud service i Azure. När du använder den här metoden gör tillgängliga praktiska länkar för att slutföra alla krav när du går i Azure-portalen. Om du är redo att distribuera din molntjänst när du skapar den, kan du göra båda på samma gång med att skapa anpassade.

> [!NOTE]
> Om du planerar att publicera din molntjänst från Visual Studio Team Services VSTS () använda Snabbregistrering och sedan konfigurera VSTS publicering från Azure Quickstart eller instrumentpanelen. Mer information finns i [kontinuerlig leverans till Azure med hjälp av Visual Studio Team Services][TFSTutorialForCloudService], eller se hjälpinformation om den **Snabbstart** sidan.
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

* Om du vill distribuera en tjänst i molnet som använder Secure Sockets Layer (SSL) för kryptering av data, [konfigurera ditt program](cloud-services-configure-ssl-certificate-portal.md#modify) för SSL.
* Om du vill konfigurera anslutningar till fjärrskrivbord till rollinstanser, [konfigurera rollerna](cloud-services-role-enable-remote-desktop-new-portal.md) för fjärrskrivbord.
* Om du vill konfigurera detaljerad övervakning av Molntjänsten, aktiverar du Azure-diagnostik för Molntjänsten. *Minimal övervakning* (standardinställningar för övervakning nivå) använder prestandaräknare som samlats in från värdoperativsystem för rollinstanser (virtuella datorer). *Utförlig övervakning* samlar in ytterligare mått baserat på prestandadata i rollinstanser för att aktivera närmare analys av problem som uppstår under bearbetningen av programmet. Om du vill veta hur du aktiverar Azure Diagnostics [aktiverar diagnostik i Azure](cloud-services-dotnet-diagnostics.md).

Om du vill skapa en molntjänst med distributioner av webbroller eller arbetsroller, måste du [skapa tjänstepaketet](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har installerat Azure SDK, klickar du på **installera Azure SDK** att öppna den [Azure hämtar sidan](https://azure.microsoft.com/downloads/), och sedan hämta SDK för det språk som du föredrar att utveckla din kod. (Har du möjlighet att göra det senare.)
* Om alla rollinstanser kräver ett certifikat, skapa certifikat. Molntjänster kräver en .pfx-fil med en privat nyckel. Du kan ladda upp certifikat till Azure när du skapar och distribuerar Molntjänsten.

## <a name="create-and-deploy"></a>Skapa och distribuera
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **New > Compute**, och bläddra till och klickar på **Molntjänsten**.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. I den nya **Molntjänsten** bladet, ange ett värde för den **DNS-namnet**.
4. Skapa en ny **resursgruppen** eller välj en befintlig.
5. Välj en **Plats**.
6. Klicka på **paketet**. Då öppnas den **överföra ett paket** bladet. Fyll i de obligatoriska fälten. Om någon av dina roller innehåller en enda instans, kontrollerar du **distribuera även om en eller flera roller innehåller en enda instans** är markerad.
7. Se till att **starta distribution** är markerad.
8. Klicka på **OK** som stänger den **överföra ett paket** bladet.
9. Om du inte har några certifikat att lägga till klickar du på **skapa**.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Överföra ett certifikat
Om din distributionspaketet [konfigurerats för att använda certifikat](cloud-services-configure-ssl-certificate-portal.md#modify), du kan ladda upp certifikatet nu.

1. Välj **certifikat**, och på den **lägga till certifikat** bladet välj SSL-certifikat PFX-fil och ange sedan den **lösenord** för certifikatet
2. Klicka på **bifoga certifikat**, och klicka sedan på **OK** på den **lägga till certifikat** bladet.
3. Klicka på **skapa** på den **Molntjänsten** bladet. När distributionen har uppnått den **klar** status, kan du fortsätta till nästa steg.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Kontrollera distributionen har slutförts
1. Klicka på cloud service-instans.

    Status ska visa att tjänsten är **kör**.
2. Under **Essentials**, klicka på den **Webbadress** att öppna din molntjänst i en webbläsare.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av Molntjänsten](cloud-services-how-to-configure-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate-portal.md).
