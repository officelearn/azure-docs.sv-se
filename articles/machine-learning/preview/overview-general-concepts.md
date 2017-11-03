---
title: "Översikt över funktioner för förhandsgranskning av Azure Machine Learning | Microsoft Docs"
description: "En översikt över funktionerna förhandsversionen av Azure Machine Learning prenumerationer, konton, arbetsytor, projekt, t.ex."
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 3d4ba2ca6f7adc8b51030c02d9e9eeb2b9995bb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning - begrepp

Den här artikeln definierar och beskriver de koncept som du behöver veta för att använda Azure Machine Learning. 

![Hierarkin för begrepp](media/overview-general-concepts/hierarchy.png)

- **Prenumerationen:** en Azure-prenumeration ger dig åtkomst till resurser i Azure. Eftersom Azure Machine Learning är djupt integrerad med bearbetning, lagring, och många andra Azure-resurser och tjänster, kräver arbetsstationen att varje användare har åtkomst till en giltig Azure-prenumeration. Användare måste också ha behörighet i den prenumerationen för att skapa resurser.


- **Experiment konto:** experiment kontot är en Azure-resurs krävs av Azure ML och fakturering fordon. Den innehåller arbetsytor, som i sin tur innehåller projekt. Du kan lägga till flera användare, kallas _platser_, till ett experiment-konto. Du måste ha åtkomst till ett experiment-konto för att kunna använda Azure ML-arbetsstationen för att köra experiment. 


- **Modellen Hanteringskontot** ett konto för hantering av modellen är också en Azure-resurs krävs av Azure ML för att hantera modeller. Du kan använda den för att registrera modeller och manifesten, skapa container webbtjänster och distribuera dem lokalt eller i molnet. Det är också Azure ML andra fakturering vehicle.


- **Arbetsytan:** en arbetsyta är den primära komponenten för delning och samarbete i Azure ML. Projekt grupperas i en arbetsyta. En arbetsyta kan sedan delas med flera användare som har lagts till kontot experiment.


- **Projekt:** i Azure Machine Learning är en logisk behållare för allt arbete som utförs för att lösa ett problem i ett projekt. Mappas till en enda mapp på den lokala hårddisken, och du kan lägga till filer eller sub mappar till den. Ett projekt kan du kan också vara associerat med en Git-lagringsplats för källkontroll och samarbete.  

- **Experiment:** i Azure ML ett experiment är en eller flera kod källfiler som kan utföras från en enda kontaktpunkt. Den kan innehålla uppgifter, till exempel datapåfyllning funktionen tekniker, modell utbildning eller utvärdering av modellen. För närvarande Azure ML stöder Python eller PySpark-experimenten endast.


- **Modell:** i Azure Machine Learning modeller avser produkten av ett maskininlärningsexperiment. De är recept som när korrekt till data generera förutsagda värden. Modeller kan distribueras till test- eller produktionsmiljö miljöer och används för resultatfunktioner nya data. En gång i produktionen modeller kan övervakas för data om prestanda och drift och retrained som krävs. 

- **Beräkning mål:** beräkning mål är beräkningsresurser som du konfigurerar för att köra dina experiment. Det kan vara en lokal dator (Windows eller macOS), dockerbehållare som körs på den lokala datorn eller i en Linux-VM på Azure eller ett HDInsight Spark-kluster.


- **Kör:** tjänsten experiment definierar en körning som livslängden för ett experiment som körs i ett mål för beräkning. Azure ML samlar in information för varje kör automatiskt och visar hela historiken för en viss experiment i form av körningshistorik.

- **Miljö:** i Azure Machine Learning, en miljö som anger en viss databearbetningsresurs som används för att distribuera och hantera modeller. Det kan vara en lokal dator, en Linux VM på Azure eller en Kubernetes kluster som körs i Azure Container Service, beroende på kontext och konfiguration. Din modell finns i en dockerbehållare som körs i dessa miljöer och visas som en REST API-slutpunkt.


- **Hanterade modell:** modellen Management gör det möjligt att distribuera modeller som webbtjänster, hantera olika versioner av modeller och övervaka deras prestanda och mått. Hanterade modeller är registrerade med ett konto i Azure Machine Learning modellen Management.

- **Manifest:** när modellen hanteringssystemet distribuerar en modell i produktion, innehåller ett manifest som kan omfatta modellen, beroenden, bedömningsprofil skript, exempeldata och schema. Manifestet är recept som används för att skapa en avbildning för Docker-behållare. Använder hantering av modellen du Autogenerera manifesten, skapa olika versioner och hantera dessa manifest. 


- **Bilder:** du kan använda manifest för att generera (och återskapa) Docker-avbildningar. Container Docker bilder skapa flexibilitet för att kunna köra dem i molnet, på lokala datorer eller i IoT-enhet. Bilder är självständiga och inkluderar alla beroenden som krävs för resultatfunktioner nya data med modeller. 

- **Tjänster:** modellen Management kan du distribuera modeller som webbtjänster. Webbtjänsten logik och beroenden kapslas i en bild. Varje webbtjänst är en uppsättning behållare baserat på bilden påbörjas serviceförfrågningar till en viss URL. En webbtjänst räknas som en enda distribution.
