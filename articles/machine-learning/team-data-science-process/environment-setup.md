---
title: Konfigurera datavetenskapsmiljöer i Azure – Team Data Science Process
description: Konfigurera datavetenskapsmiljöer på Azure för användning i Team Data Science Process.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063941"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurera datavetenskapsmiljöer för användning i Team Data Science Process
Team Data Science Process använder olika datavetenskapliga miljöer för lagring, bearbetning och analys av data. De omfattar Azure Blob Storage, flera typer av virtuella Azure-datorer, HDInsight (Hadoop) kluster och Azure Machine Learning-arbetsytor. Beslutet om vilken miljö som ska användas beror på vilken typ och mängd av data som ska modelleras och målmålet för dessa data i molnet. 

* Mer information om frågor som ska beaktas när du fattar det här beslutet finns i [Planera din Azure Machine Learning Data Science Environment](plan-your-environment.md). 
* En katalog över några av de scenarier du kan stöta på när du gör avancerad analys finns [i Scenarier för teamdatavetenskapsprocessen](plan-sample-scenarios.md)

Följande artiklar beskriver hur du ställer in de olika datavetenskapsmiljöer som används av Team Data Science Process.

* [Azure-lagringskonto](../../storage/common/storage-account-create.md)
* [HDInsight -kluster (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassisk) arbetsyta](../studio/create-workspace.md)

**Microsoft Data Science Virtual Machine (DSVM)** är också tillgänglig som en virtuell Azure-avbildning (Virtual Machine). Den här virtuella datorn är förinstallerad och konfigurerad med flera populära verktyg som ofta används för dataanalys och maskininlärning. DSVM finns på både Windows och Linux. Mer information finns i [Introduktion till den molnbaserade virtuella datavetenskapsdatorn för Linux och Windows](../data-science-virtual-machine/overview.md).

Läs om hur du skapar:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
