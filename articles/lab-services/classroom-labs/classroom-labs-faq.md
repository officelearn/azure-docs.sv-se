---
title: Klassrumslabb i Azure Lab Services – Vanliga frågor och svar | Microsoft-dokument
description: Den här artikeln innehåller svar på vanliga frågor och svar om klassrumslabb i Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443507"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Klassrumslabb i Azure Lab Services – Vanliga frågor och svar (Vanliga frågor och svar)
Få svar på några av de vanligaste frågorna om klassrumslabb i Azure Lab Services. 

## <a name="quotas"></a>Kvoter

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Är kvoten per användare eller per vecka eller hela labbets varaktighet? 
Kvoten som du anger för ett labb är för varje elev under hela labbet. Och den [schemalagda körtiden för virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats en användare. Kvoten är för tiden utanför schematimmar som en deltagare tillbringar på virtuella datorer.  Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Påverkar det studentkvoten om en student-VM slås på en virtuell student? 
Nej. Det gör det inte. När professorn slår på den virtuella studenten påverkar den inte den kvot som tilldelats deltagaren. 

## <a name="schedules"></a>Scheman

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Startar alla virtuella datorer i labbet automatiskt när ett schema har angetts? 
Nej. Inte alla virtuella datorer. Endast de virtuella datorer som har tilldelats användare enligt ett schema. De virtuella datorer som inte har tilldelats en användare startas inte automatiskt. Det är avsiktligt. 

## <a name="lab-accounts"></a>Labbuppgiftskonton

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Varför kan jag inte skapa ett labb på grund av otillgänglighet av adressintervallet? 
Classroom labs kan skapa virtuella labb virtuella datorer inom ett IP-adressintervall som du anger när du skapar ditt labbkonto i Azure-portalen. När ett adressintervall anges, varje labb som skapas efter att det har tilldelats 512 IP-adresser för lab virtuella datorer. Adressintervallet för labbkontot måste vara tillräckligt stort för att rymma alla labb som du tänker skapa under labbkontot. 

Om du till exempel har ett block på /19 - 10.0.0.0/19, rymmer det här adressintervallet 8192 IP-adresser och 16 labs(8192/512 = 16 labb). I det här fallet misslyckas labbskapandet på 17:e labbet.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Vilka portintervall ska jag öppna på organisationens brandväggsinställning för att ansluta till virtuella labbdatorer via RDP/SSH?

Hamnarna är: 49152–65535. Klassrumslabb sitter bakom en belastningsutjämnare. Varje labb har en enda offentlig IP-adress och varje virtuell dator i labbet har en unik port. 

Du kan också se den privata IP-adressen för varje virtuell dator på fliken **Virtuell datorpool** på startsidan för labbet i Azure-portalen. Om du publicerar om ett labb ändras inte labbets offentliga IP-adress, men det privata IP- och portnumret för varje virtuell dator i labbet kan ändras. Du kan läsa mer i artikeln: [Brandväggsinställningar för Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Vilket offentligt IP-adressintervall ska jag öppna i organisationens brandväggsinställningar för att ansluta till virtuella labbdatorer via RDP/SSH?
Se [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519), som tillhandahåller det offentliga IP-adressintervallet för datacenter i Azure. Du kan öppna IP-adresserna för de regioner där dina labbkonton finns.

## <a name="virtual-machine-images"></a>Bilder av virtuella datorer

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Varför kan jag inte aktivera ytterligare avbildningsalternativ i listrutan för virtuella datoravbildningar när jag skapar ett nytt labb som en labbskapare?

När en administratör lägger till dig som labbskapare i ett labbkonto får du behörighet att skapa labb. Men du har inte behörighet att redigera några inställningar i labbkontot, inklusive listan över aktiverade avbildningar för virtuella datorer. Om du vill aktivera ytterligare avbildningar kontaktar du labbkontoadministratören för att göra det åt dig eller ber administratören att lägga till dig som deltagarroll i labbkontot. Rollen Deltagare ger dig behörighet att redigera avbildningslistan för den virtuella datorn i labbkontot.

## <a name="users"></a>Användare

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Hur många användare kan vara i ett klassrumslabb?
Du kan lägga till upp till 400 användare i ett klassrumslabb. 

## <a name="blog-post"></a>Blogginlägg
Prenumerera på [bloggen Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Uppdatera meddelanden
Prenumerera på uppdateringar av [Lab Services](https://azure.microsoft.com/updates/?product=lab-services) för att hålla dig informerad om nya funktioner i Lab Services.

## <a name="general"></a>Allmänt
### <a name="what-if-my-question-isnt-answered-here"></a>Tänk om min fråga inte besvaras här?
Om din fråga inte finns med här, låt oss veta, så att vi kan hjälpa dig att hitta ett svar.

- Post en fråga i slutet av denna FAQ. 
- Om du vill nå en bredare målgrupp lägger du upp en fråga på [Azure Lab Services – Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Skicka dina begäranden och idéer till [Azure Lab Services – User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

