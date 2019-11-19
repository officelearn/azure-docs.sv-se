---
title: Använda livs miljöer för att distribuera ditt program till Azure
description: Lär dig hur du distribuerar ditt program konsekvent till Azure Virtual Machines och containers
keywords: Azure, chef, DevOps, Virtual Machines, Overview, automatiserad, livs miljö
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158057"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Använda livs miljöer för att distribuera ditt program till Azure
[Livs miljöer](https://www.habitat.sh/) är ett program för paketering och körning av program som samlar in programmet och dess automatisering tillsammans med distributions enheten. Detta skapar ultimat portabilitet för programmet, vilket gör att det kan distribueras till behållare, virtuella datorer, Bare Metal eller PaaS, utan en omskrivning eller ompaketering.

Den här artikeln beskriver de största fördelarna med att använda livs miljöer.

## <a name="modernize-and-move-legacy-applications"></a>Modernisera och flytta äldre program
Frigör äldre program från äldre operativ system och mellanprogram genom att paketera om dem med livs miljöer. Den resulterande artefakten är portabel och återanvänds enkelt på nyare infrastruktur, t. ex. virtuella datorer eller behållare som körs i molnet.

## <a name="accelerate-container-adoption"></a>Påskynda container införande
Livs miljöer löser den kontinuerliga distributionen av komplexa, mikrotjänstbaserade program genom att korrekt representera körnings beroenden. Flytta mer än enkla blå/gröna distributioner av enskilda komponenter och skapa sofistikerade distributions beteenden utan att generera komplexa Dirigerings flöden.

## <a name="run-any-application-anywhere"></a>Kör alla program var som helst
Med livs miljöer kan program köras oförändrade i alla körnings miljöer. Detta omfattar allt från Bare Metal och virtuella datorer till behållare (till exempel Docker), kluster hanterings system (t. ex. Mesosphere eller Kubernetes) och PaaS system (till exempel pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrera i chefens DevOps-arbetsflöde
Livs miljö projektet är ett av ett projekt med öppen källkod från chefs program vara, med en stark grupp support. Livs miljöer utnyttjar chefens djup upplevelse med infrastruktur automatisering för att få oöverträffade automatiserings funktioner för program. Chef erbjuder kommersiell support för livs miljöer och skapar en sömlös integrering mellan livs miljöer och chef som automatiserar program lanserings cykeln helt, från utveckling till distribution.

## <a name="next-steps"></a>Nästa steg

* [Testa livs miljöer](https://www.habitat.sh/learn/)
