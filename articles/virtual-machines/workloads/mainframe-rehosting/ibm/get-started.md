---
title: IBM-arbetsbelastningar på Azure | Microsoft-dokument
description: Använd en stordatoremulator och andra tjänster från Microsoft-partner för att bemäsa dina IBM z/OS-arbetsbelastningar med Microsoft Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834581"
---
# <a name="ibm-workloads-on-azure"></a>IBM-arbetsbelastningar på Azure

Många IBM stordator arbetsbelastningar baserade på z / OS kan replikeras i Azure utan förlust av funktionalitet och utan att användarna ens märker förändringar i sina underliggande system. Rehosting-program på Azure ger dig de stordatorliknande funktioner du behöver plus molnets elasticitet, tillgänglighet och potentiella kostnadsbesparingar.

Azure stöder integrering med befintliga IBM-stordatormiljöer, så att du kan migrera de applikatorer som är meningsfulla, köra hybridlösningar där det behövs och migrera över tid. Även om du helt kan skriva om befintliga stordatorbaserade program för Azure är det vanligare att vara värd för dem igen. Omskrivning lägger till kostnader, komplexitet och tid i migreringsprojekt. Med rehosting kan du:

- Flytta program till en molnbaserad emulator.

- Migrera databasen till en molnbaserad databas.

- Byt ut moduler och kod med hjälp av kodomvandlingsmotorer.

Dessutom finns IBM-programvara, inklusive WebSphere och MQ, nu på Azure Marketplace. Med en licens för IBM-programvara kan du dra nytta av den infrastrukturskalning på begäran som tillhandahålls av Azure för att snabbt starta en virtuell dator.

Ett omfattande partnerekosystem är tillgängligt som hjälper dig att migrera IBM-stordatorsystem till Azure. De flesta följer en pragmatisk metod för återanvändning där det är möjligt innan man påbörjar en stegvis distribution av omskrivning eller ersättning av program. Få mer vägledning och hjälp från partner på [Azure Storframe Migration Center](https://azure.microsoft.com/migration/mainframe/).

**Nästa steg**

- [Stordatormigration: myter och fakta](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Installera IBM zD&T dev/test-miljö på Azure](./install-ibm-z-environment.md)
- [Konfigurera en ADCD (Application Developers Controlled Distribution) i IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
