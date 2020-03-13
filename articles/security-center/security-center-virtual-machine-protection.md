---
title: Skydda dina datorer och program
description: Det här dokumentet belyser rekommendationer i Security Center som hjälper dig att skydda dina virtuella datorer och datorer och webbappar och App Service-miljöer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282645"
---
# <a name="protect-your-machines-and-applications"></a>Skydda dina datorer och program
När Azure Security Center identifierar potentiella säkerhets risker skapar den rekommendationer som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna för att skärp och skydda dina resurser.

I den här artikeln beskrivs sidan **Compute och apps** i Security Center resurs säkerhets avsnittet.

En fullständig lista över rekommendationer som du kan se på den här sidan finns i [rekommendationer för beräkning och appar](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Visa säkerheten för dina Compute-och apps-resurser

[instrument panel för ![Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Om du vill visa statusen för dina Compute-och apps-resurser går du till den vänstra rutan i Security Center och väljer **compute & Apps**. Följande flikar är tillgängliga:

* **Översikt**: visar rekommendationer för alla beräknings-och apps-resurser samt deras aktuella säkerhets status 

* [**Virtuella datorer och servrar**](#vms-and-computers): visar rekommendationer för dina virtuella datorer, datorer och aktuell säkerhets status för varje

* [**VM Scale Sets**](#vmscale-sets): visar rekommendationer för dina skalnings uppsättningar. 

* [**Moln tjänster**](#cloud-services): visar rekommendationer för dina webb-och arbets roller som övervakas av Security Center

* [**App Services**](#app-services): visar rekommendationer för dina App Service-miljöer och det aktuella säkerhets läget för varje

* [**Behållare**](#containers): visar rekommendationer för dina behållare och säkerhetsbedömningar av deras konfigurationer

* **Beräknings resurser**: visar rekommendationer för dina beräknings resurser, t. ex. Service Fabric kluster och händelse nav

### <a name="whats-in-each-tab"></a>Vad finns på varje flik?

Varje flik har flera avsnitt, och i varje avsnitt kan du öka detalj nivån för att visa mer information om det objekt som visas.

På varje flik visas även rekommendationer för relevanta resurser i den övervakade miljön. Den första kolumnen visar rekommendationen, den andra visar det totala antalet resurser som påverkas och den tredje visar problemets allvarlighets grad.

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt. Om du till exempel väljer **saknade system uppdateringar**, så visas antalet virtuella datorer och datorer som saknar uppdateringar och allvarlighets graden för den saknade uppdateringen.

> [!NOTE]
> Säkerhets rekommendationerna är desamma som på sidan **rekommendationer** , men här är de filtrerade till den resurs typ som du har valt. Mer information om hur du löser rekommendationer finns [i implementera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-computers"></a>Virtuella datorer och servrar
Avsnittet virtuella datorer och datorer ger en översikt över alla säkerhets rekommendationer för dina virtuella datorer och datorer. Det finns fyra typer av datorer:

![Icke-Azure-dator](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Icke-Azure-dator.

![Azure Resource Manager-VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager-VM.

![Klassiska virtuella Azure-datorer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure klassisk virtuell dator.

![Virtuella datorer identifieras från arbetsytan](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta inkluderar virtuella datorer från andra prenumerationer som rapporterar till arbets ytan i den här prenumerationen och virtuella datorer som har installerats med Operations Manager Direct agent och inte har något resurs-ID.

Genom ikonerna under de olika rekommendationerna hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och typ av rekommendation. Du kan också använda filtren för att söka i listan efter **resurs typ** och efter **allvarlighets grad**.

Om du vill öka detalj nivån för säkerhets rekommendationerna för varje virtuell dator klickar du på den virtuella datorn.
Här kan du visa säkerhetsinformation för den virtuella datorn eller datorn. Längst ned kan du se den rekommenderade åtgärden och allvarlighets graden för varje problem.

[![Cloud Services](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="vmscale-sets"></a>Skalnings uppsättningar för virtuella datorer
Security Center identifierar automatiskt om du har skalnings uppsättningar och rekommenderar att du installerar Microsoft Monitoring Agent på dem.

Installera Microsoft Monitoring Agent: 

1. Välj rekommendationen **Installera övervaknings agenten på den virtuella datorns skal uppsättning**. Du får en lista över oövervakade skalnings uppsättningar.

1. Välj en skalnings uppsättning som inte är felfri. Följ anvisningarna för att installera övervaknings agenten med hjälp av en befintlig ifylld arbets yta eller skapa en ny. Se till att ange [pris nivån](security-center-pricing.md) för arbets ytan om den inte är inställd.

   ![Installera MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Ange nya skalnings uppsättningar för att automatiskt installera Microsoft Monitoring Agent:
1. Gå till Azure Policy och klicka på **definitioner**.

1. Sök efter principen **distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer** och klicka på den.

1. Klicka på **Tilldela**.

1. Ange **området** och **Log Analytics arbets ytan** och klicka på **tilldela**.

Om du vill ange alla befintliga skalnings uppsättningar för att installera Microsoft Monitoring Agent går du till Azure Policy och går till **reparation** och tillämpar den befintliga principen i befintliga skalnings uppsättningar.





### <a name="cloud-services"></a>Moln tjänster
För Cloud Services skapas en rekommendation när operativ systemets version är inaktuell.

![Molntjänster](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation följer du stegen i rekommendationen för att uppdatera operativ systemet. När en uppdatering är tillgänglig får du en avisering (röd eller orange – beroende på problemets allvarlighets grad). En fullständig förklaring av den här rekommendationen får du genom att klicka på **Uppdatera OS-version** under kolumnen **Beskrivning** .






### <a name="app-services"></a>App Services
Om du vill visa App Service information måste du vara på Security Center standard pris nivå och aktivera App Service i din prenumeration. Anvisningar om hur du aktiverar den här funktionen finns i [skydda app service med Azure Security Center](security-center-app-services.md).

Under **app Services**hittar du en lista över dina App Service-miljöer och hälso översikten baserat på utvärderings Security Center utförd.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Det finns tre typer av program tjänster som visas:

![App services-miljö](./media/security-center-virtual-machine-recommendations/ase.png) App services-miljö

![Webbprogram](./media/security-center-virtual-machine-recommendations/web-app.png) Webbprogram

![Funktionsprogram](./media/security-center-virtual-machine-recommendations/function-app.png) Funktionsprogram

Om du väljer ett webb program öppnas en sammanfattningsvy med tre flikar:

   - **Rekommendationer**: baserat på utvärderingar som utförts av Security Center som misslyckades.
   - **Slutförda utvärderingar**: lista över utvärderingar som utförts av Security Center som skickades.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller att rekommendationen inte är relevant för den speciella App Service

   Under **rekommendationer** är en lista över rekommendationerna för det valda webb programmet och allvarlighets graden för varje rekommendation.

   ![App Services rekommendationer](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Välj en rekommendation om du vill se en beskrivning av rekommendationen och en lista över felaktiga resurser, felfria resurser och ej genomsökta resurser.

   - Kolumnen **skickade utvärderingar** visar en lista över slutförda utvärderingar. Allvarlighetsgraden för de här utvärderingar är alltid grönt.

   - Välj en skickade utvärdering från listan för en beskrivning av utvärderingen, en lista över feltillstånd och felfria resurser och en lista över ej genomsökta resurser. Det finns en flik för skadade resurser men listan är alltid tomt eftersom utvärderingen skickades.





### <a name="containers"></a>Artiklar

När du öppnar fliken **behållare** , beroende på din miljö, kan du se tre typer av resurser:

![Container värd](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Container hosts-virtuella datorer som kör Docker 

![Kubernetes-tjänst](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes service-kluster (AKS)

![Containerregister](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure Container Registry (ACR) register – visas bara när du är på standard pris nivån och när du har aktiverat Azure Container Registry-paketet.

Instruktioner för hur du använder säkerhets funktionerna för behållare finns i [övervaka säkerheten för dina behållare](monitor-container-security.md).

Fördelarna med Azure Container Registry-paketet beskrivs [här](azure-container-registry-integration.md)

Fördelarna med Kubernetes Services-paketet beskrivs [här](azure-kubernetes-service-integration.md)

[fliken ![behållare](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Klicka på resursen för att se rekommendationer för en speciell resurs i listan.

#### <a name="visibility-into-container-registries"></a>Insyn i behållar register

Du kan till exempel klicka på ACR-registret ASC-demo från listan som visas i bilden ovan leads till den här informations sidan:

[![rekommendationer för ett enskilt ACR-register](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Insyn i behållare som finns på IaaS Linux-datorer

När du klickar på en av de virtuella datorerna som kör Docker visas sidan information med information om behållarna på datorn, till exempel Docker-versionen och antalet avbildningar som körs på värden.

![Rekommendationer för en virtuell dator som kör Docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Säkerhets rekommendationer baserat på CIS-benchmark för Docker

Security Center söker igenom dina Docker-konfigurationer och ger dig insyn i felkonfigurationer genom att tillhandahålla en lista över alla misslyckade regler som har utvärderats. Security Center innehåller rikt linjer som hjälper dig att snabbt lösa problemen och spara tid. Security Center utvärderar Docker-konfigurationer och ger dig det senaste tillståndet kontinuerligt.

![fliken container](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande artiklar:

* [Fullständig referens lista över Azure Security Center säkerhets rekommendationer](recommendations-reference.md)
* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)