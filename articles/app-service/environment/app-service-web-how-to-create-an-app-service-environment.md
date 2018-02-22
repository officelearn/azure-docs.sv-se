---
title: "Så här skapar du en Apptjänst miljö v1"
description: "Beskrivningen av paketflödet skapas för en app service-miljö v1"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 2741ea2931ddd7989fc05e1cddbeedb80bf30410
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Så här skapar du en Apptjänst miljö v1 

> [!NOTE]
> Den här artikeln handlar om Apptjänstmiljö v1. Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
> 

### <a name="overview"></a>Översikt
App Service miljö (ASE) är ett Premium-tjänstealternativ i Azure App Service som levererar en utökad konfiguration-funktion som inte är tillgänglig i flera stämplar. Funktionen ASE i stort sett distribuerar Azure App Service till kundens virtuella nätverk. Att få en bättre förståelse av funktionerna som erbjuds av Apptjänstmiljöer att läsa den [vad är en Apptjänstmiljö] [ WhatisASE] dokumentation.

### <a name="before-you-create-your-ase"></a>Innan du skapar din ASE
Det är viktigt att vara medveten om saker som du inte kan ändra. Dessa aspekter som du inte kan ändra om din ASE när den har skapats är:

* Plats
* Prenumeration
* Resursgrupp
* Virtuella nätverk som används
* Undernät som används 
* Undernätets storlek

När du väljer ett virtuellt nätverk och ange ett undernät, se till att är det tillräckligt stor för att hantera eventuell tillväxt. 

### <a name="creating-an-app-service-environment-v1"></a>Skapa en App Service miljö v1
Om du vill skapa en Apptjänst-miljö v1, kan du söka på Azure Marketplace för ***Apptjänstmiljö v1***, eller gå igenom **skapar du en resurs** -> **webb + mobilt**  ->  **Apptjänstmiljö**. Så här skapar du en ASEv1:

1. Ange namnet på din ASE. Namnet du anger för ASE ska användas för appar som har skapats i ASE. Namnet på ASE appsvcenvdemo underdomännamnet kan är: *appsvcenvdemo.p.azurewebsites.net*. Om du har därför skapat en app med namnet *mytestapp*, det skulle vara adresserbara på *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Du kan inte använda blanksteg i din ASE. Om du använder versaler tecken i namnet, blir domännamnet den totala gemena versionen med det namnet. Om du använder en ILB ASE-namnet används inte i din underdomän men i stället anges uttryckligen under skapande av ASE.
   
    ![][1]
2. Välj din prenumeration. Den prenumeration som du använder för din ASE gäller även för alla appar som du skapar i den ASE. Du kan inte placera din ASE i ett virtuellt nätverk som tillhör en annan prenumeration.
3. Välj eller ange en ny resursgrupp. Resursgruppens namn används för din ASE måste vara samma som används för din VNet. Om du väljer en befintlig VNet uppdateras resursgruppsurvalet för din ASE för att återspegla som ditt VNet.
   
    ![][2]
4. Gör dina val för virtuellt nätverk och plats. Du kan välja att skapa ett nytt virtuellt nätverk eller välj ett befintligt virtuellt nätverk. Om du väljer ett nytt virtuellt nätverk sedan kan du ange ett namn och plats. Det nya VNet har adressintervallet 192.168.250.0/23 och ett undernät med namnet **standard** som har definierats som 192.168.250.0/24. Du också välja en befintlig klassiska eller Resource Manager VNet. VIP-datatyp avgör om din ASE kan nås direkt från internet (externa) eller om den använder en intern belastning belastningsutjämnare (ILB). Mer information om dem läsa [med en Apptjänst-miljö med en intern belastningsutjämnare][ILBASE]. Om du väljer en VIP-typ av externa kan du välja hur många externa IP-adresser i systemet skapas med för IPSSL ändamål. Om du väljer internt måste du ange underdomänen som din ASE ska använda. ASEs kan distribueras till virtuella nätverk som använder *antingen* offentligt adressintervall *eller* RFC1918 adressutrymmen (d.v.s. privata adresser). För att använda ett virtuellt nätverk med ett offentligt adressintervall, behöver du skapa VNet i förväg. När du väljer en befintlig VNet behöver du skapa ett nytt undernät under skapande av ASE. **Du kan inte använda ett befintligt undernät i portalen. Du kan skapa en ASE med en befintlig undernät om du skapar din ASE med hjälp av en resource manager-mall.** Att skapa en ASE från en mall används informationen här, [att skapa en Apptjänst-miljö från mallen] [ ILBAseTemplate] och här, [skapar en ILB Apptjänst-miljö från mall][ASEfromTemplate].

### <a name="details"></a>Information
En ASE skapas med 2 främre slutar och 2 anställda. Främre slutar fungera som HTTP/HTTPS-slutpunkter och skicka trafik till personer som är de roller som värd för dina appar. Du kan justera antalet efter ASE har skapats och kan även konfigurera automatiska regler för de här resurspoolerna. Mer information kring manuell skalning, hantering och övervakning av en Apptjänst-miljö finns här: [så här konfigurerar du en Apptjänst-miljö][ASEConfig] 

Endast en ASE kan finnas i undernätet som används av ASE. Undernätet kan inte användas för något annat än ASE

### <a name="after-app-service-environment-v1-creation"></a>Efter att Apptjänstmiljö v1 har skapats
Efter att ASE har skapats kan du justera:

* Antalet främre slutar (lägsta: 2)
* Antal av de anställda (lägsta: 2)
* Antal IP-adresser som är tillgängliga för IP-SSL
* Compute-resurs-storlekar som används av främre slutar eller arbetare (klientdel minsta storlek är P2)

Det finns mer information kring manuell skalning, hantering och övervakning av Apptjänstmiljöer här: [så här konfigurerar du en Apptjänst-miljö][ASEConfig] 

Mer information om autoskalning är en handbok här: [hur du konfigurerar Autoskala för en Apptjänstmiljö][ASEAutoscale]

Det finns ytterligare beroenden som inte är tillgängliga för anpassning till exempel databasen och lagring. Dessa är hanteras av Azure och levereras med systemet. System storage har stöd för upp till 500 GB för hela Apptjänst-miljön och databasen justeras med Azure vid behov av skalan i systemet.

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänst-miljö v1, se [introduktion till Apptjänstmiljö v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
