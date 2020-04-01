---
title: Skapa en ASE v1
description: Skapande flödesbeskrivning för en apptjänstmiljö v1. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89dc96370f65ff20d7f8be38ff78d6c1664305d3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477792"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Skapa en apptjänstmiljö v1 

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1. Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).
> 

### <a name="overview"></a>Översikt
App Service Environment (ASE) är ett Premium-tjänstalternativ för Azure App Service som ger en förbättrad konfigurationsfunktion som inte är tillgänglig i stämplarna för flera innehavare. ASE-funktionen distribuerar i huvudsak Azure App Service till en kunds virtuella nätverk. För att få en större förståelse för de funktioner som erbjuds av App Service-miljöer, läs dokumentationen [Vad är en App Service Environment-dokumentation.][WhatisASE]

### <a name="before-you-create-your-ase"></a>Innan du skapar din ASE
Det är viktigt att vara medveten om de saker du inte kan ändra. De aspekter du inte kan ändra om din ASE efter att den har skapats är:

* Location
* Prenumeration
* Resursgrupp
* Använt V-nätverk
* Undernät som används 
* Storleken på undernätet

När du väljer ett virtuella nätverk och anger ett undernät kontrollerar du att det är tillräckligt stort för att rymma framtida tillväxt. 

### <a name="creating-an-app-service-environment-v1"></a>Skapa en apptjänstmiljö v1
Om du vill skapa en apptjänstmiljö v1 kan du söka i Azure Marketplace for ***App Service Environment v1***eller gå igenom **Skapa en resurswebb** -> **+ Servicemiljö** -> **för mobilapp**. Så här skapar du en ASEv1:

1. Ange namnet på din ASE. Namnet du anger för ASE används för de appar som skapas i ASE. Om namnet på ASE är appsvcenvdemo, skulle underdomännamnet vara: *appsvcenvdemo.p.azurewebsites.net*. Om du därmed skapade en app som heter *mytestapp,* skulle det vara adresserbart på *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Du kan inte använda tomt utrymme i ASE:s namn. Om du använder versaler i namnet blir domännamnet den totala gemener versionen av det namnet. Om du använder en ILB används inte ditt ASE-namn i underdomänen utan anges i stället uttryckligen när ASE skapas.
   
    ![][1]
2. Välj din prenumeration. Prenumerationen som du använder för din ASE gäller även för alla appar som du skapar i ase-systemet. Du kan inte placera din ASE i ett virtuella nätverk som finns i en annan prenumeration.
3. Markera eller ange en ny resursgrupp. Resursgruppen som används för ASE måste vara samma som används för ditt virtuella nätverk. Om du väljer ett befintligt virtuella nätverk uppdateras resursgruppsvalet för DITT ASE för att återspegla det i ditt virtuella nätverk.
   
    ![][2]
4. Gör dina virtuella nätverks- och platsval. Du kan välja att skapa ett nytt virtuella nätverk eller välja ett befintligt virtuella nätverk. Om du väljer ett nytt virtuella nätverk kan du ange ett namn och en plats. Det nya virtuella nätverket har adressintervallet 192.168.250.0/23 och ett undernät med namnet **standard** som definieras som 192.168.250.0/24. Du kan också helt enkelt välja ett befintligt virtuella nätverk för Klassiska eller Resource Manager. VALET AV VIP-typ avgör om din ASE kan nås direkt från internet (externt) eller om det använder en intern belastningsutjämnare (ILB). Mer information om dem finns [i Använda en intern belastningsutjämnare med en apptjänstmiljö][ILBASE]. Om du väljer en VIP-typ av extern kan du välja hur många externa IP-adresser som systemet skapas med för IP SSL-ändamål. Om du väljer Internt måste du ange den underdomän som ase ska använda. ASE kan distribueras till virtuella nätverk som använder *antingen* offentliga adressintervall *eller* RFC1918-adressutrymmen (dvs. privata adresser). För att kunna använda ett virtuellt nätverk med ett offentligt adressintervall måste du skapa det virtuella nätverket i förväg. När du väljer ett befintligt virtuella nätverk måste du skapa ett nytt undernät när ASE skapas. **Du kan inte använda ett förskapadt undernät i portalen. Du kan skapa en ASE med ett befintligt undernät om du skapar din ASE med hjälp av en resurshanterares mall.** Om du vill skapa en ASE från en mall använder du informationen här, [Skapa en App Service-miljö från mall][ILBAseTemplate] och [här, Skapa en ILB App Service Environment från mall][ASEfromTemplate].

### <a name="details"></a>Information
En ASE skapas med 2 frontändar och 2 arbetare. Front ends fungerar som HTTP/HTTPS-slutpunkter och skickar trafik till arbetarna som är de roller som är värdar för dina appar. Du kan justera kvantiteten när ASE har skapats och även ställa in regler för automatisk skalning på dessa resurspooler. Mer information om manuell skalning, hantering och övervakning av en App Service-miljö finns här: [Så här konfigurerar du en apptjänstmiljö][ASEConfig] 

Endast en ASE kan finnas i undernätet som används av ASE. Undernätet kan inte användas för något annat än ASE

### <a name="after-app-service-environment-v1-creation"></a>Efter att App Service Environment v1 har skapats
När ASE har skapats kan du justera:

* Antal frontändar (minst 2)
* Antal arbetstagare (minst: 2)
* Antal IP-adresser tillgängliga för IP SSL
* Beräkna resursstorlekar som används av frontändingarna eller arbetarna (Den minsta storleken på klientdelen är P2)

Det finns mer information om manuell skalning, hantering och övervakning av App Service-miljöer här: [Så här konfigurerar du en apptjänstmiljö][ASEConfig] 

Information om automatisk skalning finns här: Så här konfigurerar du [automatisk skalning för en apptjänstmiljö][ASEAutoscale]

Det finns ytterligare beroenden som inte är tillgängliga för anpassning, till exempel databasen och lagringen. Dessa hanteras av Azure och levereras med systemet. Systemlagringen stöder upp till 500 GB för hela App Service-miljön och databasen justeras av Azure efter behov av systemets skala.

## <a name="getting-started"></a>Komma igång
Kom igång med App Service Environment v1, se [Introduktion till App Service Environment v1][WhatisASE]

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
