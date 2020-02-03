---
title: Konfigurera datavetenskapsmiljöer i Azure - Team Data Science Process
description: Ställ in data science-miljöer i Azure för användning i Team Data Science Process.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d11e1614307d5b6cb267ae72bae6f32adb4b244a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722228"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurera datavetenskapsmiljöer för användning i Team Data Science Process
Team Data Science Process använder olika data science-miljöer för lagring, bearbetning och analys av data. De inkluderar Azure Blob Storage, flera typer av virtuella Azure-datorer, kluster för HDInsight (Hadoop) och Azure Machine Learning-arbetsytor. Beslut om vilken miljö som ska använda beror på typen och mängden data till modelleras och målområdet för dessa data i molnet. 

* Råd om frågor att tänka på när du fattar det här beslutet finns i [Planera din Azure Machine Learning data vetenskaps miljö](plan-your-environment.md). 
* En förteckning över några av de scenarier som du kan stöta på när du utför avancerad analys finns i [scenarier för team data vetenskaps processen](plan-sample-scenarios.md)

I följande artiklar beskrivs hur du ställer in datavetenskapsmiljöer för olika data som används av Team Data Science Process.

* [Azure Storage – konto](../../storage/common/storage-account-create.md)
* [HDInsight-kluster (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassisk) arbets yta](../studio/create-workspace.md)

**Microsoft data science Virtual Machine (DSVM)** är också tillgängligt som en virtuell Azure-dator (VM). Den här virtuella datorn har redan installerats och konfigurerats med flera populära verktyg som vanligen används för dataanalys och maskininlärning. DSVM finns på både Windows och Linux. Mer information finns i [Introduktion till den molnbaserade data science Virtual Machine för Linux och Windows](../data-science-virtual-machine/overview.md).

Lär dig hur du skapar:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
