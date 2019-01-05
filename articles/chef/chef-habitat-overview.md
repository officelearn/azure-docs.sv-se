---
title: Använda Habitat för att distribuera programmet till Azure
description: Lär dig hur du konsekvent distribuera programmet till Azure-datorer och behållare
keywords: Azure, chef, devops, virtuella datorer, översikt, automatisera, habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4847d9ce551c9acf1e4fb6325c770187b2cfd89f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052299"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Använda Habitat för att distribuera programmet till Azure
[Habitat](https://www.habitat.sh/) är en första av dess typ projekt för öppen källkod som erbjuder en helt ny metod för programhantering. Habitat gör programmet och dess automation enhet för distribution. När program placeras i en enkel ”habitat”, runtime-miljö, oavsett om det är en behållare, utan operativsystem eller PaaS, är inte längre fokus och begränsar inte programmet. 

Den här artikeln beskriver fördelarna med att använda Habitat.

## <a name="support-for-the-modern-application"></a>Stöd för det moderna programmet
Habitat programpaket är allt som ett program ska köras under dess livscykel. Kärnkomponenterna i habitats är:
- Förpackningsformat - program i ett paket med Habitat är atomiska, kan ändras och granskningsbar.
- Habitat övervakaren körs programpaket med medvetenhet om de paket peer relationer, uppgradera strategi och säkerhetsprinciper. Övervakaren Habitat konfigurerar och hanterar programmet för miljön finns.
- Habitat ekosystem ger också en build-tjänst som tar en Habitat byggplan skapar Habitat-paket och publicerar den till en anläggning.

## <a name="run-any-application-anywhere"></a>Kör alla program överallt
Program kan köras oförändrade i alla körningsmiljö med Habitat. Programmet kan vara allt från datorer utan operativsystem och virtuella datorer till behållare (till exempel Docker), kluster-hanteringssystem (till exempel Mesosphere eller Kubernetes) och PaaS-system (till exempel Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Enkelt port äldre program
Programmen är oberoende av den miljö som de ursprungligen var avsedda när äldre program är omslutna i ett paket med Habitat. Paketen kan snabbt flyttas till modernare miljöer, till exempel molnet eller behållare. Eftersom Habitat-paket som har en standard, passiv riktade användargränssnitt, blir dessutom äldre program mycket enklare att hantera.

## <a name="improve-the-container-experience"></a>Förbättra upplevelsen för behållare
Habitat minskar komplexiteten med att hantera behållare i produktionsmiljöer. Genom att automatisera programkonfiguration i en behållare, adresser Habitat de utmaningar utvecklare står inför när du flyttar behållarbaserade program från utvecklingsmiljö till produktion.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrera Chef DevOps-arbetsflöde
Projektet Habitat sponsras av Chef. Habitat utnyttjar chefs erfarenhet av automatisering av infrastruktur att använda automatiseringsfunktioner för oöverträffad för program. Chef kommer erbjuder kommersiella support för Habitat och säkerställa sömlös integrering mellan Habitat och Chef leverans för att automatisera programmets version livscykel, från utveckling till distribution.

## <a name="next-steps"></a>Nästa steg
* [Skapa en virtuell Windows-dator på Azure med Chef](/azure/virtual-machines/windows/chef-automation)