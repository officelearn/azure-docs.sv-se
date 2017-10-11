---
title: "Så här konfigurerar du en tjänst i molnet (klassiska portal) | Microsoft Docs"
description: "Lär dig hur du konfigurerar molntjänster i Azure. Lär dig att uppdatera tjänstkonfigurationen för molnet och konfigurera fjärråtkomst till rollinstanser."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4902f79d-ea91-41ca-89a4-7c818180ee5f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 39bb294c96ce0c12d91cf8b3488ac3e1a7b2f7b2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-cloud-services"></a>Så här konfigurerar du molntjänster
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-configure-portal.md)
> * [Klassisk Azure-portal](cloud-services-how-to-configure.md)
> 
> 

Du kan konfigurera de vanligaste inställningarna för en molntjänst i den klassiska Azure-portalen. Du kan också uppdatera konfigurationsfilerna direkt, hämta en tjänstkonfigurationsfil för uppdatering och sedan överföra den uppdaterade filen och uppdatera molntjänsten med konfigurationsändringarna. Vilket sätt du än väljer kommer konfigurationsuppdateringarna att skickas ut till alla rollinstanser.

Den klassiska Azure-portalen kan du också [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services](cloud-services-role-enable-remote-desktop.md)

Azure kan bara garantera 99,95% tjänsten under configuration uppdateringar om du har minst två rollinstanser för varje roll. Som gör det möjligt för en virtuell dator att bearbeta klientbegäranden medan den andra uppdateras. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ändra en tjänst i molnet
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **molntjänster**, klicka på namnet på Molntjänsten och klicka sedan på **konfigurera**.
   
    ![Konfigurationssidan](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
   
    På den **konfigurera** kan du konfigurera övervakning, uppdatering rollinställningar och välj gästoperativsystemet och familj för rollinstanser. 
2. I **övervakning**, ställa in övervakning på utförlig eller minimalt och konfigurera diagnostik-anslutningssträngar som krävs för övervakning av utförlig.
3. Du kan uppdatera följande inställningar för tjänsten roller (grupperade efter roll):
   
    * **Inställningar för** ändra värden för diverse konfigurationsinställningar som anges i den *ConfigurationSettings* element i tjänstekonfigurationsfilen (.cscfg).

    * **Certifikat**  
        Ändra tumavtrycket för certifikatet som används i SSL-kryptering för en roll. Om du vill ändra ett certifikat, måste du först ladda upp det nya certifikatet (på den **certifikat** sidan). Uppdatera tumavtrycket i certifikatet strängen som visas i rollinställningarna för.
4. I **operativsystemet**, kan du ändra operativsystemsfamilj eller version för rollinstanser, eller välj **automatisk** att aktivera Automatiska uppdateringar av den aktuella versionen av operativsystemet. Inställningar för operativsystemet gäller webb- och arbetsroller, men påverkar inte virtuella datorer.
   
    Den senaste versionen av operativsystemet är installerat på alla rollinstanser under distributionen och operativsystem som uppdateras automatiskt som standard. 
   
    Om du behöver för din molntjänst som körs på en annan operativsystemversion på grund av kompatibilitetskraven i koden, väljer du en operativsystemsfamilj och version. När du väljer en specifik operativsystemsversion har automatisk operativsystemuppdateringar för Molntjänsten avbrutits. Du måste se till att operativsystem som tar emot uppdateringar.
   
    Om du har löst alla problem med programkompatibilitet som dina appar har med den senaste versionen av operativsystemet kan du aktivera automatisk operativsystemuppdateringar genom att ange operativsystemets version **automatisk**. 
   
    ![OS-inställningar](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)
5. Om du vill spara inställningarna och push-installera dem rollinstanserna, klickar du på **spara**. (Klicka på **Ignorera** att avbryta ändringarna.) **Spara** och **Ignorera** läggs till i kommandofältet när du ändrar en inställning.

## <a name="update-a-cloud-service-configuration-file"></a>Uppdatera en cloud service-konfigurationsfil
1. Hämta en cloud service konfigurationsfilen (.cscfg) med den aktuella konfigurationen. På den **konfigurera** för Molntjänsten, klickar du på **hämta**. Klicka på **spara**, eller klicka på **Spara som** att spara filen.
2. När du har uppdaterat tjänstkonfigurationsfilen överföra och tillämpa konfigurationsuppdateringarna:
   
   1. På den **konfigurera** klickar du på **överför**.
      
       ![Överför konfiguration](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
   2. I **konfigurationsfilen**, använda **Bläddra** att välja uppdaterade .cscfg-filen.
   3. Om din molntjänst innehåller de roller som har en enda instans, Välj den **tillämpa konfigurationen även om en eller flera roller innehåller en enda instans** kryssrutan för att aktivera configuration uppdateringar för rollerna som ska fortsätta.
      
       Om du inte definierar minst två instanser av varje roll, Azure kan inte garantera till minst 99,95% tillgänglighet för din molntjänst under service configuration uppdateringar. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).
   4. Klicka på **OK** (markering). 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [distribuera en tjänst i molnet](cloud-services-how-to-create-deploy.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name.md).
* [Hantera din molntjänst](cloud-services-how-to-manage.md).
* [Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster](cloud-services-role-enable-remote-desktop.md)
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate.md).

