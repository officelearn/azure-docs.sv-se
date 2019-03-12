---
title: Använd SAP på Linux-datorer | Microsoft Docs
description: Läs om att använda SAP på virtuella Linux-datorer (VM:ar) i Microsoft Azure
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: e4c4afefe7a4746f2d8138bde7fea152f228a8bc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773528"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Använd SAP på Linux-datorer i Azure
Cloud Computing är en välanvänd term som ökat i vikt inom IT-branschen, från småföretag upp till stora multinationella bolag. Microsoft Azure är Microsofts molntjänstplattform som erbjuder ett brett utbud av nya möjligheter. Kunder kan snabbt etablera och avetablera program som molntjänster så att de inte behöver hålla tillbaka på grund av tekniska eller budgetbegränsningar. Istället för att investera tid och budget i maskinvaruinfrastruktur, kan företag fokusera på programmet, verksamhetsprocesserna och dess fördelar för kunder och användare.

Microsoft erbjuder en omfattande infrastruktur som en tjänst (IaaS)-plattform med Microsoft Azure-datorer. SAP NetWeaver-baserade program stöds på virtuella Azure-datorer (IaaS). Vitboken nedan beskriver hur du planerar och implementerar SAP NetWeaver-baserade program på Windows-datorer i Azure. Du kan också implementera SAP NetWeaver-baserade program på [Windows virtuella datorer](../../virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>SAP NetWeaver på Azure SUSE Linux-datorer
Rubrik: Testa SAP NetWeaver på Microsoft Azure virtuella SUSE Linux-datorer

Sammanfattning: Det finns inga officiella SAP-stöd för att köra SAP NetWeaver på virtuella Linux-datorer vid denna tidpunkt. Dock kunder kanske vill göra en del testning eller överväga att köra SAP-demo eller utbildningssystem på virtuella Linux-datorer så länge det finns inget behov av att kontakta supporten för SAP. Den här artikeln bör hjälp med att konfigurera virtuella SUSE Linux-datorer för att köra SAP och ger vissa grundläggande tips för att undvika vanliga fallgropar.

Uppdaterad: December 2015

[Den här artikeln finns här](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

