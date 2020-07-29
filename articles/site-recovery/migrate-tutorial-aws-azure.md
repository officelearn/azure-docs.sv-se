---
title: Migrera virtuella AWS-datorer till Azure med Azure Site Recovery-tjänsten | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar virtuella Windows-datorer som körs i Amazon Web Services (AWS) till Azure med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281301"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrera virtuella AWS-datorer (Amazon Web Services) till Azure

I den här artikeln beskrivs alternativ för migrering av Amazon Web Services-instanser (AWS) till Azure.

## <a name="migrate-with-azure-migrate"></a>Migrera med Azure Migrate

Vi rekommenderar att du migrerar AWS-instanser till Azure med hjälp av tjänsten [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate tillhandahåller en central hubb för utvärdering och migrering av lokala datorer till Azure med hjälp av Azure Migrate, andra Azure-tjänster och verktyg från tredje part.

[Lär dig hur](../migrate/tutorial-migrate-aws-virtual-machines.md) du MIGRERAr AWS-instanser med Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrera med Site Recovery

Site Recovery bör endast användas för haveri beredskap och inte migrering.

Om du redan använder Azure Site Recovery och du vill fortsätta att använda den för AWS-migrering följer du samma steg som du använder för att konfigurera [haveri beredskap för fysiska datorer](physical-azure-disaster-recovery.md).


> [!NOTE]
> När du kör en redundansväxling för haveri beredskap, som ett sista steg, genomför du redundansväxlingen. När du migrerar AWS-instanser är **inchecknings** alternativet inte relevant. I stället väljer du alternativet **fullständig migrering** . 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs vanliga frågor](../migrate/resources-faq.md) om Azure Migrate.
