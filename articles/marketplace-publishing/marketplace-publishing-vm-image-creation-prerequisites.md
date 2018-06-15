---
title: Tekniska krav för att skapa en avbildning av virtuell dator för Azure Marketplace | Microsoft Docs
description: Förstå kraven för att skapa och distribuera en avbildning av virtuell dator på Azure Marketplace för andra att köpa.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: cf1f061c28dd0c106823d34ad39aac5e577c8b41
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936663"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Tekniska krav för att skapa en avbildning av virtuell dator för Azure Marketplace
Läs noggrant innan du börjar processen och förstå varför och där varje steg utförs. Så mycket som möjligt du ska förbereda företagets information och annan information, hämta nödvändiga verktyg och skapa tekniska komponenter innan du börjar skapa erbjudande. Dessa objekt bör vara klart från granska den här artikeln.  

## <a name="download-needed-tools--applications"></a>Hämta nödvändiga verktyg och program
Du bör ha följande till hands innan du börjar:

* Beroende på vilket operativsystem du inriktar dig, installera den [Azure PowerShell-cmdlets](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) eller [Linux kommandoradsgränssnittet verktyget](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) från den [Azure hämtar](https://azure.microsoft.com/downloads/) sidan.
* Installera Azure Lagringsutforskaren från CodePlex.
* Hämta och installera verktyget certifikatutfärdare Test för Azure certifierade:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Behöver du en Windows-baserad dator köra verktyget certifikatutfärdare. Om du inte har en Windows-baserad dator som är tillgängliga kan du köra verktyget med hjälp av en Windows-baserad virtuell dator i Azure.

## <a name="platforms-supported"></a>Plattformar som stöds
Du kan utveckla Azure-baserade virtuella datorer i Windows eller Linux. Vissa delar av publiceringsprocessen – till exempel skapa en Azure-kompatibel virtuell hårddisk (VHD)--Använd olika verktyg och steg beroende på vilket operativsystem du använder:  

* Om du använder Linux finns i avsnittet ”Skapa en Azure-kompatibel virtuell Hårddisk (Linux-baserade)” för den [virtuella avbildningen publiceringsguide](marketplace-publishing-vm-image-creation.md).
* Om du använder Windows finns i avsnittet ”Skapa en Azure-kompatibel virtuell Hårddisk (Windows-baserade)” för den [virtuella avbildningen publiceringsguide](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Du behöver åtkomst till en Windows-baserad dator till:
> 
> * Kör verktyget certifikatutfärdare validering.
> * Skapa VHD delad åtkomst signatur URL-Adressen för VHD certifikatutfärdare överföring.
> 
> 

## <a name="develop-your-vhd"></a>Utveckla den virtuella Hårddisken
Du kan utveckla Azure virtuella hårddiskar i molnet eller lokalt:

* Molnbaserad utveckling innebär alla development steg utförs via fjärranslutning på en virtuell Hårddisk på Azure.
* Lokal utveckling kräver hämtning av en virtuell Hårddisk och utveckla den med hjälp av lokal infrastruktur. Även om det är möjligt, rekommenderas inte den. Observera att utveckla för Windows eller SQL lokalt måste du ha relevanta lokalt licensnycklar. Du kan inte innehålla eller installera SQL Server när du har skapat en virtuell dator. Erbjudandet måste också baseras på en godkända SQL-avbildning från Azure-portalen. Om du väljer att utveckla lokalt, måste du utföra några steg annorlunda än om du skapar i molnet. Du kan hitta relevant information i [skapa en lokal VM-avbildning](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
