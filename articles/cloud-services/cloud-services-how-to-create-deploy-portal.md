---
title: Så här skapar och distribuerar du en molntjänst | Microsoft-dokument
description: Lär dig hur du skapar och distribuerar en molntjänst med Hjälp av Azure-portalen.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 63cf864a3f3b92728ad613ac45542bdbce2c9858
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811335"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Så här skapar och distribuerar du en molntjänst
Azure-portalen innehåller två sätt för dig att skapa och distribuera en molntjänst: *Snabbskapa* och *anpassad skapa*.

I den här artikeln beskrivs hur du använder snabbsökningsmetoden för att skapa en ny molntjänst och sedan använda **Ladda upp** för att ladda upp och distribuera ett molntjänstpaket i Azure. När du använder den här metoden gör Azure-portalen tillgängliga praktiska länkar för att slutföra alla krav när du går. Om du är redo att distribuera din molntjänst när du skapar den kan du göra båda samtidigt med anpassad create.

> [!NOTE]
> Om du planerar att publicera din molntjänst från Azure DevOps använder du Snabbskapa och konfigurerar sedan Azure DevOps-publicering från Azure Quickstart eller instrumentpanelen. Mer information finns i [Kontinuerlig leverans till Azure med Hjälp av Azure DevOps][TFSTutorialForCloudService]eller mer information för **snabbstartssidan.**
>
>

## <a name="concepts"></a>Begrepp
Tre komponenter krävs för att distribuera ett program som en molntjänst i Azure:

* **Definition av tjänst**  
  Definitionsfilen för molntjänsten (.csdef) definierar tjänstmodellen, inklusive antalet roller.
* **Tjänstkonfiguration**  
  Konfigurationsfilen för molntjänsten (.cscfg) innehåller konfigurationsinställningar för molntjänsten och enskilda roller, inklusive antalet rollinstanser.
* **Servicepaket**  
  Servicepaketet (.cspkg) innehåller programkoden och konfigurationerna och tjänstdefinitionsfilen.

Du kan läsa mer om dessa och hur du skapar ett paket [här](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Förbereda din app
Innan du kan distribuera en molntjänst måste du skapa molntjänstpaketet (.cspkg) från programkoden och en molntjänstkonfigurationsfil (.cscfg). Azure SDK innehåller verktyg för att förbereda dessa nödvändiga distributionsfiler. Du kan installera SDK från sidan [Azure Downloads](https://azure.microsoft.com/downloads/) på det språk som du föredrar att utveckla programkoden på.

Tre molntjänstfunktioner kräver speciella konfigurationer innan du exporterar ett servicepaket:

* Om du vill distribuera en molntjänst som använder Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL), [konfigurerar du programmet](cloud-services-configure-ssl-certificate-portal.md#modify) för TLS för datakryptering.
* Om du vill konfigurera anslutning till fjärrskrivbord till rollinstanser [konfigurerar du rollerna](cloud-services-role-enable-remote-desktop-new-portal.md) för Fjärrskrivbord.
* Om du vill konfigurera utförlig övervakning för din molntjänst aktiverar du Azure Diagnostics för molntjänsten. *Minimal övervakning* (standardövervakningsnivån) använder prestandaräknare som samlats in från värdoperativsystemen för rollinstanser (virtuella datorer). *Utförlig övervakning* samlar in ytterligare mått baserat på prestandadata i rollinstanserna för att möjliggöra en närmare analys av problem som uppstår under programbearbetning. Information om hur du aktiverar Azure Diagnostics finns [i Aktivera diagnostik i Azure](cloud-services-dotnet-diagnostics.md).

Om du vill skapa en molntjänst med distributioner av webbroller eller arbetsroller måste du [skapa tjänstpaketet](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har installerat Azure SDK klickar du på **Installera Azure SDK** för att öppna [sidan Azure Downloads](https://azure.microsoft.com/downloads/)och hämtar sedan SDK för det språk som du föredrar att utveckla koden på. (Du har möjlighet att göra detta senare.)
* Om några rollinstanser kräver ett certifikat skapar du certifikaten. Molntjänster kräver en PFX-fil med en privat nyckel. Du kan ladda upp certifikaten till Azure när du skapar och distribuerar molntjänsten.

## <a name="create-and-deploy"></a>Skapa och distribuera
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Skapa en resurs > Beräkning**och rulla sedan ned till och klicka på **Molntjänst**.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Ange ett värde för **DNS-namnet**i det nya **molntjänstfönstret** .
4. Skapa en ny **resursgrupp** eller välj en befintlig.
5. Välj en **plats**.
6. Klicka på **Paket**. Då öppnas **fönstret Ladda upp ett paket.** Fyll i de obligatoriska fälten. Om någon av dina roller innehåller en enda instans, se **till att distribuera även om en eller flera roller innehåller en enda instans** är markerad.
7. Kontrollera att **Starta distributionen** är markerad.
8. Klicka på **OK** som stänger **fönstret Ladda upp ett paket.**
9. Om du inte har några certifikat att lägga till klickar du på **Skapa**.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Ladda upp ett certifikat
Om distributionspaketet [har konfigurerats för att använda certifikat](cloud-services-configure-ssl-certificate-portal.md#modify)kan du ladda upp certifikatet nu.

1. Välj **Certifikat**och välj pfx-filen TLS/SSL-certifikat i fönstret **Lägg till certifikat** och ange sedan **lösenordet** för certifikatet.
2. Klicka på **Bifoga certifikat**och sedan på **OK** i fönstret Lägg **till certifikat.**
3. Klicka på **Skapa** i fönstret **Molntjänst.** När distributionen har nått **statusen Klar** kan du gå vidare till nästa steg.

    ![Publicera din molntjänst](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifiera att distributionen har slutförts
1. Klicka på molntjänstinstansen.

    Statusen ska visa att tjänsten **körs**.
2. Under **Essentials**klickar du på **webbadressen** för att öppna molntjänsten i en webbläsare.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av molntjänsten](cloud-services-how-to-configure-portal.md).
* Konfigurera ett [eget domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).



