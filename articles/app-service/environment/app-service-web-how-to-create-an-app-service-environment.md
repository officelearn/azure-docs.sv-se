---
title: Så här skapar du en App Service Environment version 1 – Azure
description: Skapa flödesbeskrivningen för en app service environment v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9bc796c4d0d449f72dc3234bc2825554eafaf77f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339900"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Så här skapar du en App Service Environment version 1 

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1. Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).
> 

### <a name="overview"></a>Översikt
App Service Environment (ASE) är ett Premium-tjänsten i Azure App Service som ger en förbättrad configuration-funktion som inte är tillgänglig i stämplarna för flera innehavare. Funktionen ASE distribuerar Azure App Service i stort sett till kundens virtuella nätverk. Att få bättre förståelse av de funktioner som erbjuds av App Service-miljöer läsa den [vad är en App Service Environment] [ WhatisASE] dokumentation.

### <a name="before-you-create-your-ase"></a>Innan du skapar din ASE
Det är viktigt att känna till de saker som du inte kan ändra. Dessa aspekter som du inte kan ändra om din ASE när den har skapats är:

* Plats
* Prenumeration
* Resursgrupp
* Virtuella nätverket som används
* Undernät som används 
* Undernätets storlek

När du väljer ett virtuellt nätverk och ange ett undernät, se till att är det tillräckligt stor för att hantera eventuell tillväxt. 

### <a name="creating-an-app-service-environment-v1"></a>Skapa en App Service Environment version 1
För att skapa en App Service Environment v1, kan du söka på Azure Marketplace för ***App Service Environment v1***, eller gå igenom **skapa en resurs** -> **webb + mobilt**  ->  **Apptjänstmiljö**. Skapa en ASEv1:

1. Ange namnet på din ASE. Namnet du anger för ASE ska användas för appar som skapats i ASE. Om namnet på ASE är appsvcenvdemo, underdomän namnet blir: *appsvcenvdemo.p.azurewebsites.net*. Om du har skapat en app med namnet därför *mytestapp*, skulle det vara adresserbara på *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Du kan inte använda tomma utrymmen för namnet på din ASE. Om du använder versaler i namnet, kommer domännamnet att totala gemenversionen med det namnet. Om du använder en ILB ASE-namn används inte i din underdomän men i stället anges under ASE-generering.
   
    ![][1]
2. Välj din prenumeration. Den prenumeration som du använder för din ASE gäller även för alla appar som du skapar i denna ASE. Du kan inte placera din ASE i ett virtuellt nätverk som tillhör en annan prenumeration.
3. Välj eller ange en ny resursgrupp. Resursgruppen som används för din ASE måste vara samma som används för ditt virtuella nätverk. Om du väljer ett befintliga virtuellt nätverk måste kommer de val av resursgrupp för din ASE att uppdateras för att avspegla för ditt VNet.
   
    ![][2]
4. Gör dina val för virtuellt nätverk och plats. Du kan välja att skapa ett nytt virtuellt nätverk eller välj ett befintliga virtuellt nätverk. Om du väljer ett nytt virtuellt nätverk kan du ange ett namn och plats. Det nya VNet har adressintervallet 192.168.250.0/23 och ett undernät med namnet **standard** som har definierats som 192.168.250.0/24. Du kan också bara välja en befintlig klassisk eller Resource Manager virtuellt nätverk. VIP-typ urvalet bestämmer om din ASE kan nås direkt från internet (extern) eller om den använder en intern belastningsutjämnaren (ILB). Mer information om dem Läs [med hjälp av en intern belastningsutjämnare med en App Service Environment][ILBASE]. Om du väljer en VIP-typ av extern kan du välja hur många externa IP-adresser som systemet skapas med för IPSSL ändamål. Om du väljer internt måste du ange underdomänen som ska använda för din ASE. Ase-miljöer kan distribueras till virtuella nätverk som använder *antingen* offentliga adressintervall, *eller* RFC1918 adressutrymmen (d.v.s. privata adresser). För att kunna använda ett virtuellt nätverk med ett offentligt adressintervall, måste du skapa det virtuella nätverket i tid. När du väljer ett befintliga virtuellt nätverk behöver du skapa ett nytt undernät under ASE-generering. **Du kan inte använda ett undernät som skapats i förväg i portalen. Du kan skapa en ASE med ett befintligt undernät om du skapar din ASE med en resource manager-mall.** Skapa en ASE från en mall använder informationen här, [skapar en App Service Environment från mall] [ ILBAseTemplate] och här kan [skapar en ILB App Service Environment från mall] [ASEfromTemplate].

### <a name="details"></a>Information
En ASE skapas med 2 Klientdelar och 2 Worker-arbeten. Klientdelar fungerar som HTTP/HTTPS-slutpunkter och skicka trafik till anställda som är de roller som värd för dina appar. Du kan justera antalet när ASE har skapats och kan även konfigurera regler för automatisk skalning på de här resurspoolerna. Mer information om manuell skalning finns för hantering och övervakning av en App Service Environment här: [Så här konfigurerar du en App Service Environment][ASEConfig] 

Endast en ASE kan finnas i undernätet som används av ASE. Undernätet kan inte användas för något annat än ASE

### <a name="after-app-service-environment-v1-creation"></a>När App Service Environment v1 har skapats
När ASE har skapats kan du justera:

* Antalet Klientdelar (minsta: 2)
* Antal arbetare (minsta: 2)
* Antal IP-adresser som är tillgängliga för IP SSL
* Compute resource-storlekar som används av Klientdelar eller arbetare (klientdel minsta storleken är P2)

Det finns mer information om manuell skalning, hantering och övervakning av App Service-miljöer här: [Så här konfigurerar du en App Service Environment][ASEConfig] 

Det finns en guide här för information om automatisk skalning: [Så här konfigurerar du automatisk skalning för en App Service Environment][ASEAutoscale]

Det finns ytterligare beroenden som inte är tillgängliga för anpassning, till exempel databas och lagring. Dessa hanteras av Azure och levereras med systemet. Lagringsutrymme för filsystem stöder upp till 500 GB för hela App Service Environment och databasen justeras med Azure vid behov genom att skalan för systemet.

## <a name="getting-started"></a>Komma igång
Kom igång med App Service Environment v1, se [introduktion till App Service Environment v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
