---
title: Skydda dina datorer och program
description: Det här dokumentet beskriver rekommendationer i Security Center som hjälper dig att skydda dina virtuella datorer och datorer och dina webbappar och App Service miljöer.
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
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 4a6d733b490edd892136f6febcc90c29a5a865e1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766811"
---
# <a name="protect-your-machines-and-applications"></a>Skydda dina datorer och program
När Security Center identifierar potentiella säkerhets problem skapas rekommendationer som vägleder dig genom processen med att konfigurera nödvändiga kontroller. 

I den här artikeln beskrivs sidan **beräkning och appar** i avsnittet om resurs säkerhet i Azure Security Center. Här beskrivs också några av de rekommendationer som du ser där.

En fullständig lista över rekommendationer för beräknings-och app Services finns i [rekommendationer för beräkning och appar](recommendations-compute-and-apps.md).

## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Visa säkerheten för dina Compute-och apps-resurser

![Instrumentpanelen i Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

Om du vill visa status för dina resurser för beräkning och appar väljer du **compute & Apps** under **resurser** i Security Center sid panelen. Följande flikar är tillgängliga:

* **Översikt**: visar rekommendationer för alla beräknings-och apps-resurser samt deras aktuella säkerhets status 

* [**Virtuella datorer och datorer**](#vms-and-computers): visar rekommendationer för dina virtuella datorer, datorer och aktuell säkerhets status för varje

* [**VM Scale Sets**](#vmscale-sets): visar rekommendationer för dina skalnings uppsättningar. 

* [**Cloud Services**](#cloud-services): visar en lista över rekommendationer för dina webb-och arbets roller som övervakas av Security Center

* [**App Services**](#app-services): visar rekommendationer för dina App Service-miljöer och det aktuella säkerhets läget för varje

* **Behållare**: visar rekommendationer för dina behållare och säkerhetsbedömningar av deras konfigurationer

* **Beräknings resurser**: visar rekommendationer för dina beräknings resurser, t. ex. Service Fabric kluster och händelse nav

### <a name="whats-in-each-tab"></a>Vad finns på varje flik?

Varje flik har flera avsnitt, och i varje avsnitt kan du öka detalj nivån för att visa mer information om det objekt som visas.

På varje flik visas även rekommendationer för relevanta resurser i den övervakade miljön. Den första kolumnen visar rekommendationen, den andra visar det totala antalet resurser som påverkas och den tredje visar problemets allvarlighets grad.

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du har valt den. Om du till exempel väljer **saknade system uppdateringar**, så visas antalet virtuella datorer och datorer som saknar uppdateringar och allvarlighets graden för den saknade uppdateringen.

> [!NOTE]
> Säkerhets rekommendationerna är desamma som på sidan **rekommendationer** , men här är de filtrerade till den resurs typ som du har valt. Mer information om hur du löser rekommendationer finns [i implementera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>Virtuella datorer och datorer
Avsnittet virtuella datorer och datorer ger en översikt över alla säkerhets rekommendationer för dina virtuella datorer och datorer. Det finns fyra typer av datorer:

![Datorer som inte är Azure-datorer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Icke-Azure-dator.

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Klassisk virtuell Azure-dator](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klassisk virtuell Azure-dator.

![Virtuella datorer som identifierats från arbets ytan](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta inkluderar virtuella datorer från andra prenumerationer som rapporterar till arbets ytan i den här prenumerationen och virtuella datorer som har installerats med Operations Manager Direct agent och inte har något resurs-ID.

Ikonen som visas under varje rekommendation hjälper dig att snabbt identifiera den virtuella datorn och datorn som behöver åtgärdas och typ av rekommendation. Du kan också använda filtren för att söka i listan efter **resurs typ** och efter **allvarlighets grad**.

Om du vill öka detalj nivån för säkerhets rekommendationerna för varje virtuell dator klickar du på den virtuella datorn.
Här ser du säkerhets informationen för den virtuella datorn eller datorn. Längst ned kan du se den rekommenderade åtgärden och allvarlighets graden för varje problem.
![Molntjänster](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Moln tjänster
För Cloud Services skapas en rekommendation när operativ systemets version är inaktuell.

![Cloud services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation följer du stegen i rekommendationen för att uppdatera operativ systemet. När en uppdatering är tillgänglig får du en avisering (röd eller orange – beroende på problemets allvarlighets grad). En fullständig förklaring av den här rekommendationen får du genom att klicka på **Uppdatera OS-version** under kolumnen **Beskrivning** .

### <a name="app-services"></a>App Services
Om du vill visa App Service information måste du vara på Security Center standard pris nivå och aktivera App Service i din prenumeration. Anvisningar om hur du aktiverar den här funktionen finns i [skydda app service med Azure Security Center](security-center-app-services.md).


Under **app Services**hittar du en lista över dina App Service-miljöer och hälso översikten baserat på utvärderings Security Center utförd.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Det finns tre typer av program tjänster som visas:

![App Services-miljö](./media/security-center-virtual-machine-recommendations/ase.png) App Services-miljö

![Webbprogram](./media/security-center-virtual-machine-recommendations/web-app.png) Webbprogram

![Funktions program](./media/security-center-virtual-machine-recommendations/function-app.png) Funktions program

Om du väljer ett webb program öppnas en sammanfattningsvy med tre flikar:

   - **Rekommendationer**: baserat på utvärderingar som utförts av Security Center som misslyckades.
   - **Slutförda utvärderingar**: lista över utvärderingar som utförts av Security Center som skickades.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller att rekommendationen inte är relevant för den speciella App Service

   Under **rekommendationer** är en lista över rekommendationerna för det valda webb programmet och allvarlighets graden för varje rekommendation.

   ![App Services rekommendationer](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Välj en rekommendation om du vill se en beskrivning av rekommendationen och en lista över felaktiga resurser, felfria resurser och ej genomsökta resurser.

   - Kolumnen **skickade utvärderingar** visar en lista över slutförda utvärderingar. Allvarlighets graden för de här utvärderingarna är alltid grön.

   - Välj en slutförd utvärdering i listan för en beskrivning av utvärderingen, en lista över felaktiga och felfria resurser samt en lista över ej inskannade resurser. Det finns en flik för resurser som inte är felfria, men listan är alltid tom sedan utvärderingen skickades.

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


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande artiklar:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)
