---
title: Övervaka säkerheten för dina behållare i Azure Security Center
description: Lär dig hur du kontrollerar säkerhetspositionen för dina behållare från Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919540"
---
# <a name="monitoring-the-security-of-your-containers"></a>Övervaka säkerheten för dina behållare

På den här sidan beskrivs hur du använder behållarsäkerhetsfunktionerna som beskrivs i [artikeln Container Security](container-security.md) i avsnittet Innehåller koncept.

Azure Security Center täcker följande tre aspekter av containersäkerhet:

- **Sårbarhetshantering** – Om du använder Security Centers standardprisnivå (se [prissättning)](/azure/security-center/security-center-pricing)kan du skanna ditt ARM-baserade Azure Container-register varje gång en ny avbildning skjuts. Skannern (som drivs av Qualys) presenterar resultaten som Security Center rekommendationer.
    Detaljerade instruktioner finns i [Skanna behållarregister för säkerhetsproblem](#scanning-your-arm-based-container-registries-for-vulnerabilities) nedan.

- **Härdning av dina containers Docker-värdar** - Security Center hittar ohanterade behållare som finns på IaaS Linux-virtuella datorer eller andra Linux-datorer som kör Docker, och jämför kontinuerligt behållarnas konfigurationer med CiS-dockerriktaren (Center for Internet Security). Security Center varnar dig om dina behållare inte uppfyller någon av kontrollerna. Kontinuerlig övervakning av säkerhetsrisker på grund av felkonfigurationer är en viktig komponent i alla säkerhetsprogram. 
    Detaljerade instruktioner finns i [Härda dina behållares Docker-värdar](#hardening-your-containers-docker-hosts) nedan.

- **Härdatning av dina Azure Kubernetes-tjänstkluster** – Security Center ger rekommendationer när det hittar säkerhetsproblem i konfigurationen av dina Azure Kubernetes-tjänstkluster. Mer information om de specifika rekommendationer som kan visas finns i [kubernetes-tjänstens rekommendationer](recommendations-reference.md#recs-containers).

- **Körningsskydd** – Om du använder Security Centers standardprisnivå får du hotskydd i realtid för dina containermiljöer. Security Center genererar aviseringar för misstänkta aktiviteter på värd- och AKS-klusternivå. Mer information om relevanta säkerhetsaviseringar som kan visas finns i [aviseringarna för Azure Kubernetes-tjänstkluster](alerts-reference.md#alerts-akscluster) och [aviseringar för behållare – värdnivåavsnitt](alerts-reference.md#alerts-containerhost) i referenstabellen för aviseringar.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Skanna dina ARM-baserade behållarregister efter sårbarheter 

1. Så här aktiverar du säkerhetsproblem genom avbildningar i Azure Container Registry:

    1. Se till att du är på Azure Security Centers standardprisnivå.

    1. Aktivera det valfria paket för behållarregister för din prenumeration på ![sidan Pris & **inställningar:** Aktivera paketet Container Registertries](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center är nu redo att skanna avbildningar som skjuts till registret. 

        >[!NOTE]
        >Den här funktionen debiteras per bild.


1. Om du vill utlösa genomsökningen av en avbildning skickar du den till registret. 

    När genomsökningen är klar (vanligtvis efter cirka 10 minuter) finns resultaten i Säkerhetscenterrekommendationer.
    

1. Om du vill visa resultaten går du till sidan **Rekommendationer.** Om problem hittades visas följande rekommendation:

    ![Rekommendation för att åtgärda problem ](media/monitor-container-security/acr-finding.png)


1. Välj rekommendationen. 
    Sidan med rekommendationsinformation öppnas med ytterligare information. Den här informationen innehåller en lista över register med sårbara bilder ("Berörda resurser") och reparationsstegen. 

1. Välj ett specifikt register om du vill visa de databaser i det som har sårbara databaser.

    ![Välj ett register](media/monitor-container-security/acr-finding-select-registry.png)

    Sidan registerinformation öppnas med en lista över berörda databaser.

1. Välj en specifik databas om du vill visa de databaser i den som har sårbara bilder.

    ![Markera en databas](media/monitor-container-security/acr-finding-select-repository.png)

    Informationssidan för databasen öppnas. Den listar de sårbara bilderna tillsammans med en bedömning av hur allvarliga resultaten är.

1. Välj en specifik bild om du vill se säkerhetsproblemen.

    ![Välj bilder](media/monitor-container-security/acr-finding-select-image.png)

    Listan över resultat för den valda bilden öppnas.

    ![Lista över resultat](media/monitor-container-security/acr-findings.png)

1. Om du vill veta mer om ett resultat väljer du att hitta. 

    Fönstret resultatinformation öppnas.

    [![Fönstret Information om resultat](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Det här fönstret innehåller en detaljerad beskrivning av problemet och länkar till externa resurser för att minska hoten.

1. Följ stegen i avsnittet reparation i det här fönstret.

1. När du har vidtagit de åtgärder som krävs för att åtgärda säkerhetsproblemet ersätter du avbildningen i registret:

    1. Tryck på den uppdaterade bilden. Detta kommer att utlösa en genomsökning. 
    
    1. Kontrollera rekommendationssidan för rekommendationen "Säkerhetsproblem i Azure Container Registry avbildningar bör åtgärdas". 
    
        Om rekommendationen fortfarande visas och bilden du har hanterat fortfarande visas i listan över sårbara bilder kontrollerar du reparationsstegen igen.

    1. När du är säker på att den uppdaterade bilden har pushats, skannats och inte längre visas i rekommendationen tar du bort den "gamla" sårbara avbildningen från registret.


## <a name="hardening-your-containers-docker-hosts"></a>Härdning av dina containrars Docker-värdar

Security Center övervakar ständigt konfigurationen av Dina Docker-värdar och genererar säkerhetsrekommendationer som återspeglar branschstandarder.

Så här visar du säkerhetsrekommendationer för Azure Security Center för docker-värdar för dina behållare:

1. Öppna **&-appar i** navigeringsfältet i Security Center och välj fliken **Behållare.**

1. Du kan också filtrera listan över behållarresurser till behållare värdar.

    ![Filter för behållareresurser](media/monitor-container-security/container-resources-filter.png)

1. Välj en som ska undersökas ytterligare i listan över behållarvärdmaskiner.

    ![Rekommendationer för behållarevärd](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Sidan **Container värdinformation** öppnas med information om värden och en lista med rekommendationer.

1. Välj en rekommendation som ska undersökas ytterligare i listan över rekommendationer.

    ![Rekommendationslista för behållare värd](media/monitor-container-security/container-host-rec.png)

1. Du kan också läsa stegen beskrivning, information, hot och reparation. 

1. Välj **Vidta åtgärder** längst ned på sidan.

    [![Knappen Vidta åtgärd](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics öppnas med en anpassad åtgärd som är klar att köras. Standardanpassad fråga innehåller en lista över alla misslyckade regler som har bedömts, tillsammans med riktlinjer som hjälper dig att lösa problemen.

    [![Logga analytics-åtgärd](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Justera frågeparametrarna och välj **Kör** när du är säker på att den är klar för värden. 



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Säkerhetscenters säkerhetsfunktioner för behållare. 

För annat relaterat material, se följande sidor: 

- [Rekommendationer för säkerhetscenter för behållare](recommendations-reference.md#recs-containers)
- [Aviseringar för AKS-klusternivå](alerts-reference.md#alerts-akscluster)
- [Aviseringar för värdnivå för behållare](alerts-reference.md#alerts-containerhost)
