---
title: Skydda dina maskiner och applikationer
description: Det här dokumentet innehåller rekommendationer i Security Center som hjälper dig att skydda dina virtuella datorer och datorer och dina webbappar och App Service-miljöer.
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
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435636"
---
# <a name="protect-your-machines-and-applications"></a>Skydda dina maskiner och applikationer
När Azure Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som hjälper dig att konfigurera de nödvändiga kontrollerna för att härda och skydda dina resurser.

I den här artikeln beskrivs sidan **Beräkning och appar** i Security Centers resurssäkerhetsavsnitt.

En fullständig lista över de rekommendationer som kan visas på den här sidan finns i [rekommendationer för beräkning och appar](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Visa säkerheten för dina beräknings- och appresurser

[![Instrumentpanelen i Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Om du vill visa status för dina beräknings- och appresurser väljer du **Beräkna & appar**från den vänstra rutan i Security Center . Följande flikar är tillgängliga:

* **Översikt**: visar rekommendationerna för alla beräknings- och appresurser samt deras aktuella säkerhetsstatus 

* [**Virtuella datorer och servrar:**](#vms-and-computers)visar rekommendationerna för dina virtuella datorer, datorer och aktuella säkerhetstillstånd för varje

* [**VM-skalningsuppsättningar:**](#vmscale-sets)visar rekommendationerna för dina skalningsuppsättningar, 

* [**Molntjänster**](#cloud-services): visar rekommendationer för webb- och arbetsroller som övervakas av Security Center

* [**Apptjänster**](#app-services): visar rekommendationerna för apptjänstmiljöerna och det aktuella säkerhetsläget för varje

* [**Behållare**](#containers): listar rekommendationerna för dina behållare och säkerhetsbedömning av deras konfigurationer

* **Beräkningsresurser**: visar rekommendationer för dina beräkningsresurser, till exempel kluster för serviceinfrastruktur och händelsehubbar

### <a name="whats-in-each-tab"></a>Vad finns i varje flik?

Varje flik har flera avsnitt, och i varje avsnitt kan du öka detaljnivån för att se ytterligare information om objektet som visas.

På varje flik ser du också rekommendationer för relevanta resurser i din övervakade miljö. I den första kolumnen visas rekommendationen, den andra visar det totala antalet resurser som påverkas och den tredje visar problemets allvarlighetsgrad.

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt den. Om du till exempel väljer **Saknade systemuppdateringar**visas antalet virtuella datorer och datorer som saknar korrigeringar och hur allvarlig den saknade uppdateringen är.

> [!NOTE]
> Säkerhetsrekommendationerna är desamma som på sidan **Rekommendationer,** men här filtreras de till den specifika resurstyp som du har valt. Mer information om hur du löser rekommendationer finns [i Implementera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Virtuella datorer och servrar
Avsnittet Virtuella datorer och datorer ger dig en översikt över alla säkerhetsrekommendationer för dina virtuella datorer och datorer. Fyra typer av maskiner ingår:

![Datorer som inte är Azure-datorer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Icke-Azure-dator.

![Virtuella Azure Resource Manager-datorer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Virtuell Azure Resource Manager.

![Azure Classic VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure Classic VM.

![Virtuella datorer som identifierats från arbetsytan](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta inkluderar virtuella datorer från andra prenumerationer som rapporterar till arbetsytan i den här prenumerationen och virtuella datorer som installerades med Operations Manager direct agent och har inget resurs-ID.

Ikonen som visas under varje rekommendation hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och vilken typ av rekommendation som ska ändras. Du kan också använda filtren för att söka i listan efter **resurstyp** och **efter allvarlighetsgrad**.

Om du vill öka detaljnivån i säkerhetsrekommendationerna för varje virtuell dator klickar du på den virtuella datorn.
Här ser du säkerhetsinformationen för den virtuella datorn eller datorn. Längst ned kan du se den rekommenderade åtgärden och allvarlighetsgraden för varje problem.

[![Molntjänster](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Skalningsuppsättningar för virtuella datorer
Security Center identifierar automatiskt om du har skalningsuppsättningar och rekommenderar att du installerar Log Analytics-agenten på dem.

Så här installerar du Log Analytics-agenten: 

1. Välj rekommendation **Installera övervakningsagenten på skalningsuppsättningen för virtuella datorer**. Du får en lista över oövervakade skaluppsättningar.

1. Välj en felaktig skalningsuppsättning. Följ instruktionerna för att installera övervakningsagenten med hjälp av en befintlig ifylld arbetsyta eller skapa en ny. Se till att ange prisnivån för [arbetsytan](security-center-pricing.md) om den inte är inställd.

   ![Installera MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Så här anger du nya skalningsuppsättningar för att automatiskt installera Log Analytics-agenten:
1. Gå till Azure-principen och klicka på **Definitioner**.

1. Sök efter principen **Deploy Log Analytics agent för Windows virtuella dator skalningsuppsättningar** och klicka på den.

1. Klicka på **Tilldela**.

1. Ange **arbetsytan Scope** och **Log Analytics** och klicka på **Tilldela**.

Om du vill ange alla befintliga skalningsuppsättningar för att installera Log Analytics-agenten går du till **Reparation** i Azure-principen och tillämpar den befintliga principen på befintliga skalningsuppsättningar.





### <a name="cloud-services"></a><a name="cloud-services"></a>Molntjänster
För molntjänster skapas en rekommendation när operativsystemversionen är inaktuella.

![Molntjänster](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation följer du stegen i rekommendationen för att uppdatera operativsystemet. När en uppdatering är tillgänglig har du en avisering (röd eller orange - beroende på problemets allvarlighetsgrad). Om du vill ha en fullständig förklaring av den här rekommendationen klickar du på **Uppdatera OS-versionen** under kolumnen **BESKRIVNING.**






### <a name="app-services"></a><a name="app-services"></a>Apptjänster
Om du vill visa apptjänstinformationen måste du ha standardprisnivå för Security Center och aktivera App Service i prenumerationen. Instruktioner om hur du aktiverar den här funktionen finns i [Skydda apptjänsten med Azure Security Center](security-center-app-services.md).

Under **Apptjänster**hittar du en lista över apptjänstmiljöer och hälsosammanfattningen baserat på den utvärdering säkerhetscenter som utförts.

![Apptjänster](./media/security-center-virtual-machine-recommendations/app-services.png)

Det finns tre typer av programtjänster som visas:

![Miljö för apptjänster](./media/security-center-virtual-machine-recommendations/ase.png) Miljö för apptjänster

![Webbprogram](./media/security-center-virtual-machine-recommendations/web-app.png) Webbprogram

![Funktionsprogram](./media/security-center-virtual-machine-recommendations/function-app.png) Funktionsprogram

Om du väljer ett webbprogram öppnas en sammanfattningsvy med tre flikar:

   - **Rekommendationer**: baserat på bedömningar som utförts av Security Center som misslyckades.
   - **Godkända utvärderingar**: lista över utvärderingar som utförts av Security Center som passerade.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller rekommendationen är inte relevant för den specifika App-tjänsten

   Under **Rekommendationer** finns en lista över rekommendationerna för det valda webbprogrammet och allvarlighetsgraden för varje rekommendation.

   ![Rekommendationer för App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Välj en rekommendation om du vill se en beskrivning av rekommendationen och en lista över felaktiga resurser, felfria resurser och icke-skannade resurser.

   - Kolumnen **Godkända utvärderingar** visar en lista över godkända utvärderingar. Allvarlighetsgraden för dessa bedömningar är alltid grön.

   - Välj en godkänd bedömning i listan för en beskrivning av utvärderingen, en lista över felaktiga och felfria resurser och en lista över icke-skannade resurser. Det finns en flik för felaktiga resurser, men den listan är alltid tom sedan utvärderingen passerade.





### <a name="containers"></a><a name="containers"></a>Behållare

När du öppnar fliken **Behållare,** beroende på din miljö, kan du se någon av tre typer av resurser:

![Behållare värd](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Container värdar - virtuella datorer som kör docker 

![Tjänsten Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Aks-kluster (Azure Kubernetes Service)

![Containerregister](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Acr-register (Azure Container Registry) – Visas bara när du är på standardprisnivån och när du har azure container-registret aktiverat.

Instruktioner om hur du använder säkerhetsfunktionerna i behållaren finns i [Övervaka säkerheten för dina behållare](monitor-container-security.md).

Fördelarna med Azure Container Registry-paketet förklaras [här](azure-container-registry-integration.md)

Fördelarna med Kubernetes Services-paketet förklaras [här](azure-kubernetes-service-integration.md)

[![Fliken Behållare](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Klicka på resursen om du vill se rekommendationerna för en viss resurs i listan.

#### <a name="visibility-into-container-registries"></a>Insyn i behållarregister

Om du till exempel klickar på ACR-registret för asc-demo från listan som visas i bilden ovan visas den här informationssidan:

[![Rekommendationer för ett specifikt ACR-register](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Insyn i behållare som finns på IaaS Linux-maskiner

När du klickar på en av de virtuella datorer som kör docker visas informationssidan med information om behållarna på datorn, till exempel Docker-versionen och antalet bilder som körs på värden.

![Rekommendationer för en dator som kör docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Säkerhetsrekommendationer baserade på CIS-riktmärke för Docker

Security Center söker igenom dina Docker-konfigurationer och ger dig insyn i felkonfigurationer genom att tillhandahålla en lista över alla misslyckade regler som har utvärderats. Security Center innehåller riktlinjer som hjälper dig att lösa dessa problem snabbt och spara tid. Security Center utvärderar Docker-konfigurationer och ger dig det senaste tillståndet kontinuerligt.

![fliken behållare](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra Azure-resurstyper finns i följande artiklar:

* [Fullständig referenslista över Säkerhetsrekommendationer för Azure Security Center](recommendations-reference.md)
* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)