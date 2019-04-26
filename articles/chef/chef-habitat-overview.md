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
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388819"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Använda Habitat för att distribuera programmet till Azure
[Habitat](https://www.habitat.sh/) är ett program paketering och runtime-system som paketerar programmet och dess automation tillsammans som måttenhet för distribution. Detta skapar ultimate portabilitet för programmet, så att den kan distribueras till behållare, virtuella datorer, utan operativsystem eller PaaS, utan en omarbetning eller ompaketering.

Den här artikeln beskriver de främsta fördelarna med att använda Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernisera och flytta äldre program
Kostnadsfria äldre program från äldre operativsystem och mellanprogram genom att paketera dem med Habitat. Den resulterande artefakten som är portabelt och enkelt replatforms till en nyare infrastruktur som virtuella datorer eller behållare som körs i molnet.

## <a name="accelerate-container-adoption"></a>Accelerera användningen av behållare
Habitat löser kontinuerlig distribution av komplexa, mikrotjänstinriktade program genom att korrekt som representerar körningsberoenden. Flytta bortom enkel blå/grön distribution av enskilda komponenter och skapa sofistikerade distributionsbeteende utan att generera avancerad samordning för flöden.

## <a name="run-any-application-anywhere"></a>Kör alla program överallt
Program kan köras oförändrade i alla körningsmiljö med Habitat. Detta omfattar allt från datorer utan operativsystem och virtuella datorer till behållare (till exempel Docker), kluster-hanteringssystem (till exempel Mesosphere eller Kubernetes) och PaaS-system (till exempel Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrera Chef DevOps-arbetsflöde
Habitat projektet är en av ett projekt med öppen källkod från Chef-programvara med en stark community support. Habitat utnyttjar chefs erfarenhet av automatisering av infrastruktur att använda automatiseringsfunktioner för oöverträffad för program. Chef erbjuder kommersiella stöd för Habitat och bygger en smidig integrering mellan Habitat och Chef Automate för att automatisera programmets version livscykel, från utveckling till distribution.

## <a name="next-steps"></a>Nästa steg

* [Försök Habitat](https://www.habitat.sh/learn/)
