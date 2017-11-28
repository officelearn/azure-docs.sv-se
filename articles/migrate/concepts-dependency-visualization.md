---
title: Beroende visualisering i Azure migrera | Microsoft Docs
description: "En översikt över assessment beräkningar i tjänsten Azure migrera."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 78e52157-edfd-4b09-923f-f0df0880e0e0
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: e787cd6746b53c28fc1495b9a3ac62f2aa08f639
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="dependency-visualization"></a>Beroende visualiseringen

Den [Azure migrera](migrate-overview.md) services utvärderar grupper av lokala datorer för migrering till Azure. Om du vill gruppera datorer kan du använda beroende visualiseringen. Den här artikeln innehåller information om den här funktionen.


## <a name="overview"></a>Översikt

Beroende visualisering i Azure migrera kan du skapa grupper för migrering utvärdera med ökat förtroende. Du kan visa nätverk beroenden för specifika datorer eller i en grupp datorer med beroende visualiseringen. Detta är användbart för att se till att inga funktioner eller förlorade (eller datorer som har glömt) i migreringsprocessen när appar och arbetsbelastningar som körs på flera datorer.  

## <a name="how-does-it-work"></a>Hur fungerar det?

Azure migrera använder den [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) lösning i [logganalys](../log-analytics/log-analytics-overview.md) för beroende visualiseringen.
- När du skapar ett projekt för Azure-migrering, skapas en OMS logganalys-arbetsytan i din prenumeration.
- Arbetsytans namn är namnet du anger för projektet migrering med prefixet **migrera-**, och eventuellt suffixet med en siffra. 
- Navigera till logganalys-arbetsytan från den **Essentials** avsnitt i projektet **översikt** sidan.
- Arbetsytan skapade är märkta med nyckeln **MigrateProject**, och värdet **projektnamn**. Du kan använda dessa för att söka i Azure-portalen.  

    ![Log Analytics-arbetsyta](./media/concepts-dependency-visualization/oms-workspace.png)

Om du vill använda beroende visualiseringen måste hämta och installera agenter på varje lokal dator som du vill analysera.  

## <a name="do-i-need-to-pay-for-it"></a>Behöver jag betala för den?

Ja. Logganalys-arbetsytan skapas som standard, men det inte används om du inte använder beroende visualisering i Azure migrera. Om du använder beroende visualiseringen (eller Använd arbetsytan utanför Azure migrera), debiteras du för användning i arbetsytan.  [Lär dig mer](https://www.microsoft.com/cloud-platform/operations-management-suite) om prissättningen för Tjänstkarta lösning. 

## <a name="how-do-i-manage-the-workspace"></a>Hur hanterar arbetsytan?

Du kan använda logganalys-arbetsytan utanför Azure migrera. Den tas inte bort om du tar bort projektet migrering där den skapades. Om du inte längre behöver arbetsytan [ta bort den](../log-analytics/log-analytics-manage-access.md) manuellt.

Ta inte bort arbetsytan som skapats av Azure migrera om du tar bort migreringsprojekt. Om du gör fungerar beroenden inte som förväntat.

## <a name="next-steps"></a>Nästa steg

[Grupp datorer som använder datorn beroenden](how-to-create-group-machine-dependencies.md)