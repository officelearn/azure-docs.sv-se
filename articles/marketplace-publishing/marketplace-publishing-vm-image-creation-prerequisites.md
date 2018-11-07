---
title: Tekniska krav för att skapa en avbildning av virtuell dator för Azure Marketplace | Microsoft Docs
description: Förstå kraven för att skapa och distribuera en avbildning av virtuell dator på Azure Marketplace för andra att köpa.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 79fb9869b37e82df3f41a50e4425e7c0cd08c841
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255276"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Tekniska krav för att skapa en avbildning av virtuell dator för Azure Marketplace
Läs noggrant innan du börjar processen och förstå var och varför ett steg utförs. Så mycket som möjligt du ska förbereda företagets information och andra data, ladda ned nödvändiga verktyg och/eller skapa tekniska komponenter innan du börjar skapa erbjudandet. De här objekten ska rensa från granska den här artikeln.  

## <a name="download-needed-tools--applications"></a>Ladda ned nödvändiga verktyg och program
Du bör ha följande till hands innan du börjar:

* Beroende på vilket operativsystem du vill skicka meddelanden, installera den [Azure PowerShell-cmdlets](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) eller [Linux kommandoradsgränssnittet verktyget](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) från den [Azure hämtar](https://azure.microsoft.com/downloads/) sidan.
* Installera Azure Storage Explorer från CodePlex.
* Hämta och installera verktyget för Test-certifiering för Azure Certified:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](https://go.microsoft.com/fwlink/?LinkID=526913). Behöver du en Windows-baserad dator att köra certification tool. Om du inte har en Windows-baserad dator som är tillgänglig, kan du köra verktyget med hjälp av en Windows-baserad virtuell dator i Azure.

## <a name="platforms-supported"></a>Plattformar som stöds
Du kan utveckla Azure-baserade virtuella datorer i Windows eller Linux. Vissa delar av publiceringsprocessen – till exempel skapa en Azure-kompatibel virtuell hårddisk (VHD) – Använd olika verktyg och steg beroende på vilket operativsystem du använder:  

* Om du använder Linux finns i avsnittet ”Skapa en Azure-kompatibel VHD (Linux-baserade)” för den [publiceringsguide för VM-avbildning](marketplace-publishing-vm-image-creation.md).
* Om du använder Windows finns i avsnittet ”Skapa en Azure-kompatibel VHD (Windows-baserade)” för den [publiceringsguide för VM-avbildning](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Du behöver åtkomst till en Windows-baserad dator till:
> 
> * Kör verktyget certifiering verifiering.
> * Skapa virtuell Hårddisk delas åtkomst signatur URL för VHD-certifiering överföringen.
> 
> 

## <a name="develop-your-vhd"></a>Utveckla din VHD
Du kan utveckla Azure-VHD: er i molnet eller lokalt:

* Molnbaserad utveckling innebär att alla utveckling steg utförs via en fjärranslutning på en virtuell Hårddisk på Azure.
* Lokal utveckling kräver att ladda ned en virtuell Hårddisk och utveckla den med hjälp av den lokala infrastrukturen. Även om det är möjligt rekommenderar vi inte den. Observera att utveckla för Windows eller SQL lokalt måste du ha licensnycklar relevanta lokalt. Du kan inte inkludera eller installera SQL Server när du har skapat en virtuell dator. Erbjudandet måste också baseras på en godkänd SQL-avbildning från Azure-portalen. Om du vill utveckla lokalt, måste du utföra några steg annorlunda än om du skapar i molnet. Du kan hitta relevant information i [skapa en VM-avbildning från en lokal](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
