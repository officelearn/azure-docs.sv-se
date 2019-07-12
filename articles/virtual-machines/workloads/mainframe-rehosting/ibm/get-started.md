---
title: IBM arbetsbelastningar på Azure | Microsoft Docs
description: Använda en stordatorprogram emulator och andra tjänster från Microsoft-partners för att ange ny värd för dina IBM z/OS-arbetsbelastningar med Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 4acf2fe1bc63061f17f90d6737f586408520dc13
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621325"
---
# <a name="ibm-workloads-on-azure"></a>IBM arbetsbelastningar på Azure

Många IBM stordatorprogram arbetsbelastningar baserat på z/OS kan replikeras i Azure utan att förlora av funktioner och utan att användarna även upptäcka ändringar i underliggande systemen. Rehosting program på Azure får du stordatorprogram-liknande funktioner som du behöver dessutom flexibilitet, tillgänglighet, och potentiella besparingar i molnet.

Azure har stöd för integrering med befintliga miljöer för IBM stordatorprogram, så att du kan migrera applicates som passar, kör hybridlösningar där det behövs och migrerar över tid. Även om du kan helt skriva om befintliga stordatorprogram-baserade program för Azure, är det vanligare att rehost dem. Skriva om lägger till kostnaden och komplexiteten tid migreringsprojekt. Med att byta värd kan du:

- Flytta program till en molnbaserad emulator.

- Migrera databasen till en molnbaserad databas.

- Ersätt-moduler och kod med hjälp av kod omvandling motorer.

IBM-program, bland annat WebSphere och MQ, är dessutom nu på Azure Marketplace. Med en licens för IBM-program kan du utnyttja Azures infrastrukturskalning på begäran för att snabbt starta en virtuell dator.

Ett omfattande ekosystem är tillgängliga för att hjälpa dig att migrera IBM-stordatorsystem till Azure. De flesta följa en pragmatisk metod för återanvändning möjligt innan du utvecklar en stegvis distribution av skriva om eller byta ut appar. Få mer information och hjälp från partners på den [Azure Stordatormigrering center](https://azure.microsoft.com/migration/mainframe/).

**Nästa steg**

- [Stordatormigrering: myths och fakta](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Installera IBM zD & T dev/test-miljö på Azure](./install-ibm-z-environment.md)
- [Ange in ett program utvecklare kontrollerad Distribution (ADCD) i IBM zD & T v1](./demo.md)
- [IBM DB2 pureScale på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
