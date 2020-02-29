---
title: Övervaka säkerheten för dina behållare i Azure Security Center
description: Lär dig hur du kontrollerar säkerhets position för dina behållare från Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919540"
---
# <a name="monitoring-the-security-of-your-containers"></a>Övervaka säkerheten för dina behållare

På den här sidan förklaras hur du använder behållar säkerhetsfunktioner som beskrivs i [artikeln behållar säkerhet](container-security.md) i avsnittet begrepp.

Azure Security Center omfattar följande tre aspekter av behållar säkerhet:

- **Sårbarhets hantering** – om du är på Security Center standard pris nivån (se [prissättning](/azure/security-center/security-center-pricing)) kan du söka igenom ARM-baserade Azure Container Registry varje gång en ny avbildning skickas. Skannern (drivs av Qualys) visar avgöranden som Security Center rekommendationer.
    Detaljerade anvisningar finns i [genomsöka dina behållar register för sårbarheter](#scanning-your-arm-based-container-registries-for-vulnerabilities) nedan.

- **Härdning av behållarna "Docker hosts"** -Security Center hittar ohanterade behållare som finns på virtuella datorer med IaaS Linux eller andra Linux-datorer som kör Docker och kontinuerligt jämför behållar konfigurationerna med Center for Internet Security (CIS) Docker-benchmark. Security Center varnar dig om dina behållare inte uppfyller någon av kontrollerna. Kontinuerlig övervakning av säkerhets risker på grund av felaktig konfiguration är en viktig komponent i alla säkerhets program. 
    Detaljerade anvisningar finns i [skärp dina behållares Docker-värdar](#hardening-your-containers-docker-hosts) nedan.

- **Härdning av Azure Kubernetes service-kluster** – Security Center ger rekommendationer när de hittar sårbarheter i konfigurationen av Azure Kubernetes service-kluster. Mer information om de rekommendationer som kan visas finns i rekommendationer för [Kubernetes-tjänsten](recommendations-reference.md#recs-containers).

- **Körnings skydd** – om du är på Security Center standard pris nivån får du real tids skydd för dina behållares miljöer. Security Center genererar aviseringar för misstänkta aktiviteter på kluster nivån värd och AKS. Mer information om relevanta säkerhets aviseringar som kan visas finns i avsnittet [aviseringar för Azure Kubernetes service-kluster](alerts-reference.md#alerts-akscluster) och [aviseringar för behållare-värdnivå](alerts-reference.md#alerts-containerhost) avsnitt i referens tabellen för aviseringar.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Genomsökning av dina ARM-baserade behållar register för sårbarheter 

1. Aktivera sårbarhets ökningar av Azure Container Registry avbildningar:

    1. Se till att du är på Azure Security Center standard pris nivån.

    1. På sidan **Inställningar för prissättnings &** aktiverar du de valfria paketen för behållar register för din prenumeration: ![aktivera paket för behållar register](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center är nu redo att skanna bilder som skickas till registret. 

        >[!NOTE]
        >Den här funktionen debiteras per avbildning.


1. Om du vill utlösa skanningen av en avbildning, push-överför den till registret. 

    När genomsökningen är klar (vanligt vis efter cirka 10 minuter), finns det ett avgörande i Security Center rekommendationer.
    

1. Om du vill visa resultaten går du till sidan **rekommendationer** . Om problem påträffas visas följande rekommendation:

    ![Rekommendation för att åtgärda problem ](media/monitor-container-security/acr-finding.png)


1. Välj rekommendationen. 
    Sidan med rekommendations information öppnas med ytterligare information. Den här informationen omfattar listan över register med sårbara avbildningar ("resurser som påverkas") och åtgärds stegen. 

1. Välj ett enskilt register för att se de databaser i den som har sårbara databaser.

    ![Välj ett register](media/monitor-container-security/acr-finding-select-registry.png)

    Sidan register information öppnas med listan över berörda databaser.

1. Välj en annan lagrings plats för att se de databaser som har sårbara avbildningar i den.

    ![Välj en lagrings plats](media/monitor-container-security/acr-finding-select-repository.png)

    Sidan databas information öppnas. Den visar de sårbara bilderna tillsammans med en utvärdering av avgörandenas allvarlighets grad.

1. Välj en avbildning för att se sårbarheterna.

    ![Välj bilder](media/monitor-container-security/acr-finding-select-image.png)

    Listan över resultat för den valda bilden öppnas.

    ![Lista över resultat](media/monitor-container-security/acr-findings.png)

1. Om du vill veta mer om att hitta kan du välja hitta. 

    Fönstret med information om resultat visas.

    [informations fönstret ![s resultat](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    I det här fönstret finns en detaljerad beskrivning av problemet och länkar till externa resurser som hjälper dig att minimera hoten.

1. Följ stegen i reparations avsnittet i det här fönstret.

1. När du har vidtagit de steg som krävs för att åtgärda säkerhets problemet ersätter du avbildningen i registret:

    1. Skicka den uppdaterade avbildningen. Detta utlöser en sökning. 
    
    1. Se sidan rekommendationer för rekommendationen "säkerhets risker i Azure Container Registry avbildningar bör åtgärdas". 
    
        Om rekommendationen fortfarande visas och den avbildning som du har hanterat fortfarande visas i listan över sårbara avbildningar, kontrol lera reparations stegen igen.

    1. När du är säker på att den uppdaterade avbildningen har flyttats, genomsökts och inte längre visas i rekommendationen, tar du bort den "gamla" sårbara avbildningen från registret.


## <a name="hardening-your-containers-docker-hosts"></a>Härdning av dina behållares Docker-värdar

Security Center övervakar ständigt konfigurationen av Docker-värdarna och genererar säkerhets rekommendationer som återspeglar bransch standarder.

Så här visar du Azure Security Center säkerhets rekommendationer för dina behållares Docker-värdar:

1. Öppna **compute & Apps** i navigerings fältet Security Center och välj fliken **behållare** .

1. Du kan också filtrera listan över dina behållar resurser till container hosts-värdar.

    ![Filter för container resurser](media/monitor-container-security/container-resources-filter.png)

1. I listan över dina behållares värddatorer väljer du en för att undersöka ytterligare.

    ![Rekommendationer för container värd](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    **Sidan information om behållarens värd** öppnas med information om värden och en lista över rekommendationer.

1. I listan rekommendationer väljer du en rekommendation för att undersöka vidare.

    ![Rekommendations lista för container värd](media/monitor-container-security/container-host-rec.png)

1. Du kan också läsa stegen beskrivning, information, hot och reparation. 

1. Välj **vidta åtgärd** längst ned på sidan.

    [knappen ![ta med åtgärd](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics öppnar med en anpassad åtgärd som är klar att köras. Den anpassade standard frågan innehåller en lista över alla misslyckade regler som utvärderades, tillsammans med rikt linjer som hjälper dig att lösa problemen.

    [åtgärd för ![Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Justera frågeparametrarna och välj **Kör** när du är säker på att den är redo för värden. 



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Security Center behållar säkerhetsfunktioner. 

För annat relaterat material, se följande sidor: 

- [Security Center rekommendationer för behållare](recommendations-reference.md#recs-containers)
- [Aviseringar för AKS-kluster nivå](alerts-reference.md#alerts-akscluster)
- [Aviseringar för värd nivå för behållare](alerts-reference.md#alerts-containerhost)
