---
title: Översikt över Azure Defender och tillgängliga planer
description: Lär dig mer om Azure Defender-planer,-skydd och-aviseringar. Fortsätt sedan med att aktivera Azure Defender på dina prenumerationer.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5a5b96d5a9ea6aa05da30238690b8f5fa745b3f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448425"
---
# <a name="introduction-to-azure-defender"></a>Introduktion till Azure Defender

Azure Security Centers funktioner beskriver de två breda pelare för moln säkerhet:

- **Cloud Security position Management (CSPM)**
- **Moln skydd för arbets belastning (CWP)**

CSPM-funktionerna i Security Center, till exempel säkra poäng, identifiering av felkonfigurationer av säkerhet i dina Windows-och Linux Azure-datorer, är en del av den kostnads fria Security Center-upplevelsen som är tillgänglig för alla Azure-användare. Använd dessa CSPM-funktioner för att förstärka din position och kontrol lera efterlevnaden.

**Azure Defender** är CWPP (Cloud Protection Platform) som är integrerad i Security Center för avancerad, intelligent, skydd av dina Azure-och hybrid arbets belastningar.

Det här är Azure Defender-instrumentpanelen i Azure Security Center:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Ett exempel på Azure Defender-instrumentpanelen" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Vilka resurs typer kan Azure Defender skydda?

Azure Defender innehåller säkerhets aviseringar och Avancerat skydd för virtuella datorer, SQL-databaser, behållare, webb program, nätverk och mycket annat.

När du aktiverar Azure Defender från avsnittet **priser och inställningar** i Azure Security Center, aktive ras följande Defender-planer samtidigt och ger omfattande försvars områden i din miljö:

- [Azure Defender för servrar](defender-for-servers-introduction.md)
- [Azure Defender för App Service](defender-for-app-service-introduction.md)
- [Azure Defender för Storage](defender-for-storage-introduction.md)
- [Azure Defender för SQL](defender-for-sql-introduction.md)
- [Azure Defender för IoT](defender-for-iot-introduction.md)
- [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender för containerregister](defender-for-container-registries-introduction.md)
- [Azure Defender för Key Vault](defender-for-key-vault-introduction.md)

Var och en av dessa planer beskrivs separat i Security Center-dokumentationen.


## <a name="hybrid-cloud-protection"></a>Hybrid moln skydd

Du kan lägga till Azure Defender-funktioner i din hybrid moln miljö, samt skydda din Azure-miljö:

- Skydda dina icke-Azure-servrar
- Skydda dina virtuella datorer i andra moln (till exempel AWS och GCP)
- Skydda dina IoT-enheter

Du får en anpassad Hot information och prioriterade aviseringar enligt din miljö så att du kan fokusera på det viktigaste

Distribuera [Azure-bågen](https://azure.microsoft.com/services/azure-arc/) och aktivera Azure Defender för att utöka skyddet till lokala och virtuella datorer med flera moln och SQL-databaser. Azure-bågen för servrar är en kostnads fri tjänst, men tjänster som används på Arc-aktiverade servrar, till exempel Azure Defender, debiteras enligt priserna för tjänsten.

[Läs mer om Azure-bågen](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Azure Defender-aviseringar 

När Azure Defender identifierar ett hot i alla områden i miljön genererar den en avisering. De här aviseringarna beskriver information om de resurser som påverkas, föreslagna åtgärds steg och i vissa fall ett alternativ för att utlösa en logisk app som svar.

Om en avisering genereras av Security Center eller tas emot av Security Center från en integrerad säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).

> [!NOTE]
> Aviseringar från olika källor kan ta olika tids mängder att visas. Till exempel kan aviseringar som kräver analys av nätverks trafiken ta längre tid än aviseringar som är relaterade till misstänkta processer som körs på virtuella datorer.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender avancerade skydds funktioner

Azure Defender använder avancerade analyser för skräddarsydda rekommendationer relaterade till dina resurser. 

Skyddet omfattar att skydda de virtuella datorernas hanterings portar med just-in-Time-åtkomst och anpassningsbara program kontroller för att skapa listor för vilka appar som ska och inte ska köras på dina datorer. 

Använd panelerna Avancerat skydd på Azure Defender-instrumentpanelen för att övervaka och konfigurera vart och ett av dessa skydd. 

## <a name="vulnerability-assessment-and-management"></a>Sårbarhets bedömning och hantering

Azure Defender innehåller sårbarhets sökning för dina virtuella datorer och behållar register utan extra kostnad. Skannern drivs av Qualys, men du behöver inte en Qualys-licens eller till och med ett Qualys-konto – allt hanteras sömlöst i Security Center. 

Granska resultaten från dessa sårbarhets skannrar och svara på dem i Security Center. Detta ger Security Center närmare det enda fönstret i glas för alla dina moln säkerhets åtgärder.

Läs mer på följande sidor:

- [Security Center är en integrerad lösning för sårbarhets bedömning för Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Identifiera sårbarheter i avbildningar i Azure Container register](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om fördelarna med Azure Defender. 

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](security-center-pricing.md)