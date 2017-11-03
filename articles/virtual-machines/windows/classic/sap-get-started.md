---
title: "Med SAP på virtuella Windows-datorer | Microsoft Docs"
description: "Radera om hur du använder SAP på Windows-datorer (VM) i Microsoft Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Med hjälp av SAP på Windows-datorer i Azure
Cloud Computing är en välanvänd term som ökat i vikt inom IT-branschen, från småföretag upp till stora multinationella bolag. Microsoft Azure är Microsofts molntjänstplattform som erbjuder ett brett utbud av nya möjligheter. Kunder kan snabbt etablera och avetablera program som molntjänster så att de inte behöver hålla tillbaka på grund av tekniska eller budgetbegränsningar. Istället för att investera tid och budget i maskinvaruinfrastruktur, kan företag fokusera på programmet, verksamhetsprocesserna och dess fördelar för kunder och användare.

Microsoft erbjuder ett omfattande infrastruktur som en tjänst (IaaS)-plattform med Microsoft Azure-datorer. SAP NetWeaver-baserade program stöds på virtuella Azure-datorer (IaaS). Faktablad nedan beskrivs hur du planerar och implementerar SAP NetWeaver baserat program på Windows-datorer i Azure. Du kan också implementeras SAP NetWeaver baserat program på [virtuella Linux-datorer](../../linux/classic/sap-get-started.md).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver på Azure - hög tillgänglighet
Rubrik: SAP NetWeaver på Azure - klustring SAP ASCS/SCS instanser som använder Windows Server Failover-kluster på Azure med SIOS DataKeeper

Sammanfattning ”: det här dokumentet beskriver hur du använder SIOS DataKeeper för att ställa in en konfiguration med hög tillgänglighet SAP ASCS/SCS på Azure. SAP skyddar sina enda åtkomstpunkt för fel komponenter som SAP ASCS/SCS eller sätta replikering Services med redundanskluster i Windows Server-konfigurationer som kräver delade diskar. Dessa SAP-komponenter är nödvändiga för funktionerna i en SAP-system. Därför måste funktioner med hög tillgänglighet placeras på plats för att se till att dessa komponenter kan klara av fel på en server eller en virtuell dator som görs med Windows-kluster konfigurationer för bare metal- och Hyper-V-miljöer. Från och med augusti 2015 tillhandahålla inte Azure på själva delade diskar som krävs för Windows-baserade högtillgängliga konfigurationer som krävs för dessa viktiga SAP-komponenter. Med hjälp av produkten DataKeeper av SIOS, men Windows Server Failover Cluster konfigurationerna som behövs för SAP ASCS/SCS byggas på Azure IaaS-plattformen. Det här dokumentet beskrivs i ett tillvägagångssätt för steg för steg hur du installerar en konfiguration för Windows Server Failover-kluster med delad disk som tillhandahålls av SIOS Datakeeper i Azure. Dokumentet förklarar information i konfigurationer på Azure, Windows och SAP-sida som hög tillgänglighet konfigurationen fungerar på ett optimalt sätt. Dokumentet kompletterar SAP dokumentationen och SAP anteckningar som representerar primära resurser för installation och distribution av program på den angivna plattformar.

Uppdaterad: Augusti 2015

[Hämta denna guide nu](http://go.microsoft.com/fwlink/?LinkId=613056)

