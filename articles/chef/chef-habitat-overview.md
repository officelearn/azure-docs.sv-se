---
title: Använda Habitat för att distribuera ditt program till Azure
description: Lär dig hur du konsekvent distribuerar ditt program till virtuella Azure-datorer och behållare
keywords: azurblå,, devops, virtuella datorer, översikt, automatisera, livsmiljö
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158057"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Använda Habitat för att distribuera ditt program till Azure
[Habitat](https://www.habitat.sh/) är ett programpaketerings- och körningssystem som buntar ihop programmet och dess automatisering som distributionsenhet. Detta skapar ultimat portabilitet för programmet, så att det kan distribueras till behållare, virtuella datorer, bar metall eller PaaS, utan en omskrivning eller ompaketering.

I den här artikeln beskrivs de viktigaste fördelarna med att använda Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernisera och flytta äldre program
Gratis äldre program från äldre operativsystem och mellanprogram genom att packa om dem med Habitat. Den resulterande artefakten är bärbar och replatformar enkelt på nyare infrastruktur, till exempel virtuella datorer eller behållare som körs i molnet.

## <a name="accelerate-container-adoption"></a>Påskynda införandet av behållare
Habitat löser kontinuerlig distribution av komplexa, mikrotjänstorienterade program genom att korrekt representera körningsberoenden. Gå längre än enkel blå/grön distribution av enskilda komponenter och arkitektsofenstikerat distributionsbeteende utan att generera komplexa orkestreringsflöden.

## <a name="run-any-application-anywhere"></a>Kör alla program var som helst
Med Habitat kan program köras oförändrade i alla körningsmiljöer. Detta inkluderar allt från bare metal och virtuella datorer till behållare (t.ex. Docker), klusterhanteringssystem (till exempel Mesosphere eller Kubernetes) och PaaS-system (till exempel Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrera i Chef DevOps-arbetsflödet
Habitat-projektet är ett av ett open source-projekt från Chef Software, med en stark gemenskap av stöd. Habitat utnyttjar Chefs djupa erfarenhet av automatisering av infrastruktur för att ge applikationer oöverträffade automatiseringsfunktioner. Chef erbjuder kommersiellt stöd för Habitat och bygger en sömlös integration mellan Habitat och Chef Automate för att helt automatisera programutgivningscykeln, från utveckling till driftsättning.

## <a name="next-steps"></a>Nästa steg

* [Prova Habitat](https://www.habitat.sh/learn/)
