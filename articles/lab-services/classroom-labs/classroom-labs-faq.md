---
title: Klass rum labb i Azure Lab Services – vanliga frågor och svar | Microsoft Docs
description: Få svar på vanliga frågor om klass rums labb i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 411037dd97350d877aff4e2d094c3408f168f9fd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648579"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Klass rums labb i Azure Lab Services vanliga frågor och svar
Få svar på några av de vanligaste frågorna om klass rums labb i Azure Lab Services. 

## <a name="quotas"></a>Kvoter

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Är kvoten per användare eller per vecka eller per hela tiden för labbet? 
Den kvot som du har angett för ett labb är för varje student under hela Labbets varaktighet. Och den [schemalagda körningen av virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer.  Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Scheman

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Startar alla virtuella datorer automatiskt i labbet när ett schema har angetts? 
Nej. Inte alla virtuella datorer. Endast de virtuella datorer som är tilldelade till användare enligt ett schema. De virtuella datorer som inte är tilldelade till en användare startas inte automatiskt. Det är avsiktligt. 

## <a name="lab-accounts"></a>Labb konton

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Varför kan jag inte skapa ett labb på grund av att adress intervallet inte är tillgängligt? 
Klass rums labb kan skapa virtuella labb datorer i ett IP-adressintervall som du anger när du skapar ditt labb konto i Azure Portal. När ett adress intervall anges, är varje labb som skapas efter att det har tilldelats 512 IP-adresser för virtuella labb datorer. Adress intervallet för labb kontot måste vara tillräckligt stort för att rymma alla labb som du planerar att skapa under labb kontot. 

Om du till exempel har ett block på/19-10.0.0.0/19 får detta adress intervall 8192 IP-adresser och 16 labb (8 192/512 = 16 labb). I det här fallet går det inte att skapa labbet vid skapande av 17 Lab.

## <a name="blog-post"></a>Blogginlägg
Prenumerera på [Azure Lab Services blogg](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Uppdatera meddelanden
Prenumerera på [labb tjänst uppdateringar](https://azure.microsoft.com/updates/?product=lab-services) för att hålla dig informerad om nya funktioner i labb tjänster.

## <a name="general"></a>Allmänt
### <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte visas här kan vi berätta för oss, så vi kan hjälpa dig att hitta ett svar.

- Publicera en fråga i slutet av dessa vanliga frågor och svar. 
- För att få en större mål grupp kan du skicka en fråga till [Azure Lab Services-Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Skicka dina förfrågningar och idéer till [Azure Lab Services-User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)för funktions förfrågningar.

