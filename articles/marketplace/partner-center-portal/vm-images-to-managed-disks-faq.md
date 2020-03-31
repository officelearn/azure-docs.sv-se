---
title: Vi flyttar virtuella datoravbildningar till hanterad disklagring på Azure Marketplace
description: För att ge snabbare och mer tillförlitlig lagring och stöd för nya marketplace-funktioner och funktioner flyttar vi vm-avbildningar på marketplace till hanterad disklagring.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285120"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Vi flyttar virtuella datoravbildningar (VM) på Azure Marketplace till hanterad disklagring

För att ge snabbare och mer tillförlitlig lagring och stöd för nya marketplace-funktioner och funktioner flyttar vi vm-avbildningar på marketplace till hanterad disklagring.

Från och med den 2 januari 2020 flyttar vi VM-avbildningar till hanterad disklagring i faser. I den första fasen flyttar vi bara avbildningar utan nya distributioner eller virtuella datorer som körs under de senaste 90 dagarna. Innan vi flyttar någon av bilderna skickar vi ett e-postmeddelande så att utgivaren vet vilka bilder som kommer att flyttas och när de flyttas.

Utgivare eller konsumenter behöver inte vidta några åtgärder och användarna påverkas inte. Marketplace-erbjudandena förblir tillgängliga och kunderna kommer fortfarande att kunna distribuera hanterade virtuella datorer från dessa avbildningar, under och efter flytten.

Om du har några frågor, vänligen [kontakta oss.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Skulle användarna av mina VM-avbildningar uppleva ett avbrott?

Användare av vm-avbildningarna kommer inte att uppleva ett avbrott. 

I den första fasen kommer vi bara att flytta VM-avbildningar som inte har några virtuella datorer som körs. Eftersom det inte finns några användare för dessa bilder kommer det inte att finnas någon inverkan. Även för de efterföljande faserna påverkas inte användarna.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Hur lång tid tar det för processen att slutföras?

Det kan ta upp till 24 timmar att slutföra migreringen.

### <a name="do-i-need-to-take-any-action"></a>Måste jag vidta några åtgärder?

Nej. Utgivare eller konsumenter behöver inte vidta några åtgärder.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Måste jag uppdatera mitt system för att anropa Cloud Portal-API:erna på ett annat sätt när de har flyttats till Lagring av hanterade diskar?

Nej. Dina befintliga API-anrop fortsätter att fungera.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Skulle alla mina VM-avbildningar flyttas till Hanterad disk samtidigt?

Vi kommer att flytta alla dina VM-avbildningar på samma dag. Vi kommer att meddela dig när de har flyttats.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Kan jag begära att flytta mina VM-avbildningar till en senare tidpunkt?

Vi rekommenderar att du flyttar bilderna på det schemalagda datumet. Men om du har problem, vänligen kontakta oss för att boka om flytten.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Kan jag publicera uppdateringar av mina VM-avbildningar under flytten?

Det går inte att göra uppdateringar av vm-avbildningarna under flytten.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Kommer publiceringsprocessen att ändras när min VM-avbildning har flyttats till Hanterad disk?

Nej, publiceringsprocessen förblir densamma. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Kan utgivaren flytta sina erbjudanden till Managed Disk?

Nej, utgivarena kan inte flytta sina erbjudanden till Hanterad disk. De måste vänta och deras bilder kommer att flyttas automatiskt. Vi skickar meddelanden till utgivaren innan vi gör några ändringar.
