---
title: Översikt över Azure Machine Learnings förhandsversionsfunktioner | Microsoft Docs
description: En översikt över funktionerna för förhandsgranskning Azure Machine Learning, prenumerationer, konton, arbetsytor, projekt, t.ex.
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: f63b9c077e64b642adfd8c7eed5026563eb6319a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647201"
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning - begrepp

Den här artikeln definierar och beskriver de begrepp som du behöver veta för att använda Azure Machine Learning. 

![Hierarki med begrepp](media/overview-general-concepts/hierarchy.png)

- **Prenumeration:** en Azure-prenumeration ger dig åtkomst till resurser i Azure. Eftersom Azure Machine Learning är djupt integrerad med beräkning, lagring, och många andra Azure-resurser och tjänster, kräver Workbench att varje användare har åtkomst till en giltig Azure-prenumeration. Användare måste också ha tillräcklig behörighet i prenumerationen för att skapa resurser.


- **Konto för experimentering:** experimenteringskontot är en Azure-resurs krävs av Azure ML och ett fakturering fordon. Den innehåller din arbetsytor, vilket i sin tur innehåller projekt. Du kan lägga till flera användare, kallas _platser_, till ett experimenteringskonto. Du måste ha åtkomst till ett experimenteringskonto för att kunna använda Azure ML Workbench för att köra experiment. 


- **Modellhanteringskontot** ett modellhanteringskonto är också en Azure-resurs krävs av Azure ML för att hantera modeller. Du kan använda den för att registrera modeller och manifest, skapa behållare webbtjänster och distribuera dem lokalt eller i molnet. Det är också andra fakturering fordon i Azure ML.


- **Arbetsyta:** en arbetsyta är den primära komponenten för delning och samarbete i Azure ML. Projekt är grupperade i en arbetsyta. En arbetsyta kan sedan delas med flera användare som har lagts till i experimenteringskontot.


- **Projekt:** i Azure Machine Learning är ett projekt en logisk behållare för allt arbete som utförs för att lösa ett problem. Det mappas till en enda mapp på den lokala hårddisken, och du kan lägga till filer eller undermappar till det. Ett projekt kan du kan också associeras med en Git-lagringsplats för källkontroll och samarbete.  

- **Experiment:** i Azure ML är en eller flera kod källfiler som kan utföras från en enda kontaktpunkt i ett experiment. Det får innehålla uppgifter som datainmatning, funktionsframställning, modellträning och utvärdering av modellen. För närvarande Azure ML stöder Python eller PySpark-experimenten endast.


- **Modell:** i Azure Machine Learning-modeller referera till produkten av ett maskininlärningsexperiment. De är recept som när tillämpas korrekt på data, skapa förutsagda värden. Modeller kan distribueras till test- eller produktionsmiljö miljöer och används för bedömning av nya data. En gång i produktion, modeller kan övervakas för data om prestanda och drift, och modellkomponenten efter behov. 

- **Beräkningsmål:** beräkningsmål är beräkningsresurs som du konfigurerar för att köra dina experiment. Det kan vara din lokala dator (Windows eller Mac OS), en Docker-behållare som körs på den lokala datorn eller i en Linux-VM på Azure eller ett HDInsight Spark-kluster.


- **Kör:** tjänsten experimentering definierar en körning som ett experiment körning i beräkningsmål livslängd. Azure ML samlar in information för varje körning automatiskt och presenterar hela historiken för ett visst experiment i form av körningshistorik.

- **Miljö:** i Azure Machine Learning, en miljö som anger en viss datorresurs som används för att distribuera och hantera dina modeller. Det kan vara din lokala dator, en Linux-VM på Azure eller ett Kubernetes-kluster som körs i Azure Container Service, beroende på kontext och konfiguration. Din modell finns i en Docker-behållare som körs i dessa miljöer och visas som en REST API-slutpunkt.


- **Hanterad modell:** modellhantering kan du distribuera modeller som webbtjänster, hantera olika versioner av dina modeller och övervaka prestanda och mått. Hanterade modeller är registrerade med ett konto i Azure Machine Learning-modellhantering.

- **Manifest:** när modellen hanteringssystemet distribuerar en modell i produktionen, den innehåller ett manifest som kan omfatta modellen, beroenden, bedömningsskript, exempeldata och schema. Manifestfilen är receptet används för att skapa en Docker-behållaravbildning. Med modellhantering kan du Autogenerera manifesten, skapa olika versioner och hantera dessa manifest. 


- **Bilder:** du kan använda manifest för att generera (och återskapa) Docker-avbildningar. Behållare Docker-avbildningar skapa flexibilitet att kunna köra dem i molnet, på lokala datorer eller på IoT-enheter. Avbildningarna är självständig och inkluderar alla beroenden som krävs för bedömningsfunktioner nya data med modeller. 

- **Tjänster:** modellhantering kan du distribuera modeller som webbtjänster. Webbtjänsten logik och beroenden är inkapslade i en bild. Varje webbtjänst är en uppsättning behållare baserat på avbildningen är klara att betjäna förfrågningar till en viss URL. En webbtjänst räknas som en distribution.
