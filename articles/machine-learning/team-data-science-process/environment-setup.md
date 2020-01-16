---
title: Konfigurera datavetenskapsmiljöer i Azure - Team Data Science Process
description: Ställ in data science-miljöer i Azure för användning i Team Data Science Process.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3e52f88136517339139bebd17dea929ac02201e3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982033"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurera datavetenskapsmiljöer för användning i Team Data Science Process
Team Data Science Process använder olika data science-miljöer för lagring, bearbetning och analys av data. De inkluderar Azure Blob Storage, flera typer av virtuella Azure-datorer, kluster för HDInsight (Hadoop) och Azure Machine Learning-arbetsytor. Beslut om vilken miljö som ska använda beror på typen och mängden data till modelleras och målområdet för dessa data i molnet. 

* Anvisningar för saker att tänka på när du gör det här beslutet finns i [planera din Azure Machine Learning Datavetenskapsmiljö](plan-your-environment.md). 
* En katalog med några av de scenarier som kan uppstå när du gör avancerad analys, se [scenarier för Team Data Science Process](plan-sample-scenarios.md)

I följande artiklar beskrivs hur du ställer in datavetenskapsmiljöer för olika data som används av Team Data Science Process.

* [Azure storage-konto](../../storage/common/storage-account-create.md)
* [Kluster för HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassisk) arbets yta](../studio/create-workspace.md)

Den **Microsoft Data Science Virtual Machine (DSVM)** är också tillgängligt som en avbildning i Azure-dator (VM). Den här virtuella datorn har redan installerats och konfigurerats med flera populära verktyg som vanligen används för dataanalys och maskininlärning. DSVM finns på både Windows och Linux. Mer information finns i [introduktion till den molnbaserade Data Science Virtual Machine för Linux och Windows](../data-science-virtual-machine/overview.md).

Lär dig hur du skapar:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
