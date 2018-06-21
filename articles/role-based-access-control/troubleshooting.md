---
title: Felsökning av RBAC i Azure | Microsoft Docs
description: Felsöka problem med Azure rollbaserad åtkomstkontroll (RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 557d3330ef155181c050a18b14d31b65ba1f2dcf
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295411"
---
# <a name="troubleshooting-rbac-in-azure"></a>Felsökning av RBAC i Azure

Den här artikeln innehåller svar på vanliga frågor om rollbaserad åtkomstkontroll (RBAC), så att du vet vad som händer när du använder roller i Azure-portalen och kan felsöka problem med åtkomst till. Dessa roller omfattar alla typer av resurser:

* Ägare  
* Deltagare  
* Läsare  

Ha fullständig åtkomst till hanteringen av ägare och deltagare, men en deltagare kan inte ge åtkomst till andra användare eller grupper. Det blir lite mer intressant med rollen Läsare så att den där vi tillbringar lite tid. Information om hur du bevilja åtkomst, seee [hantera åtkomst med hjälp av RBAC och Azure portal](role-assignments-portal.md).

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Skrivåtkomst funktioner
Om du ger en användare läsåtkomst till en enkel webbapp inaktiveras vissa funktioner som du kan förvänta inte sig. Följande hanteringsfunktioner kräver **skriva** åtkomst till en webbapp (medarbetare eller ägare), och inte är tillgängliga i skrivskyddat läge scenarier.

* Kommandon (till exempel start, stopp, etc.)
* Ändra inställningar för allmän konfiguration, inställningar, inställningar för säkerhetskopiering och inställningarna för övervakning
* Åtkomst till publishing autentiseringsuppgifter och andra hemligheter som app-inställningar och anslutningssträngar
* Direktuppspelningsloggar
* Diagnostikloggar konfiguration
* Konsol (Kommandotolken)
* Aktiva och senaste distributioner (för kontinuerlig lokal git-distribution)
* Uppskattade utgifter
* Webbtest
* Virtuella nätverk (endast visas för en läsare om ett virtuellt nätverk redan har konfigurerats av en användare med skrivbehörighet).

Om du inte kommer åt dessa paneler kan behöva du be administratören för deltagare åtkomst till webbprogrammet.

### <a name="dealing-with-related-resources"></a>Hantera relaterade resurser
Webbprogram komplicerade av förekomsten av ett par olika resurser som samspelet. Här är en typisk resursgrupp med några webbplatser:

![Resursgruppen för Web app](./media/troubleshooting/website-resource-model.png)

Därför, om du ger någon åtkomst till bara webbappen många funktioner på webbplatsen bladet i Azure-portalen är inaktiverat.

Dessa artiklar kräver **skriva** åtkomst till den **programtjänstplanen** som motsvarar din webbplats:  

* Visa webbprogrammet har prisnivån (lediga eller Standard)  
* Skala configuration (antal instanser, storlek på virtuell dator, Autoskala inställningar)  
* Kvoter (lagring, bandbredd, CPU)  

Dessa artiklar kräver **skriva** åtkomst till hela **resursgruppen** som innehåller din webbplats:  

* SSL-certifikat och bindningar (SSL-certifikat kan delas mellan platser i samma resursgrupp och geografiska plats)  
* Varningsregler  
* Autoskala inställningar  
* Application Insights-komponenter  
* Webbtest  

## <a name="azure-functions"></a>Azure Functions
Vissa funktioner i [Azure Functions](../azure-functions/functions-overview.md) kräver skrivåtkomst. Till exempel om en användare har tilldelats rollen Läsare, kommer de inte att kunna visa funktioner i en funktionsapp. Portalen visar **(ingen åtkomst)**.

![Fungera apparna ingen åtkomst](./media/troubleshooting/functionapps-noaccess.png)

Användaren kan klicka på **plattformsfunktioner** och klicka sedan på **alla inställningar** att visa vissa inställningar som är relaterade till en funktionsapp (liknar ett webbprogram), men de kan inte ändra dessa inställningar.

## <a name="virtual-machine"></a>Virtuell dator
Mycket precis som med webbappar, vissa funktioner på bladet för virtuella datorer kräver skrivbehörighet till den virtuella datorn eller till andra resurser i resursgruppen.

Virtuella datorer är relaterade till domänen namn, virtuella nätverk, storage-konton och Varningsregler.

Dessa artiklar kräver **skriva** åtkomst till den **virtuella**:

* Slutpunkter  
* IP-adresser  
* Diskar  
* Tillägg  

Dessa kräver **skriva** åtkomst till både den **virtuella**, och **resursgruppen** (tillsammans med domännamn) som den är i:  

* Tillgänglighetsuppsättning  
* Belastningsutjämnade uppsättningen  
* Varningsregler  

Fråga din administratör om du inte kan komma åt någon av dessa paneler för deltagare åtkomst till resursgruppen.

## <a name="next-steps"></a>Nästa steg
* [Hantera åtkomst med hjälp av RBAC och Azure portal](role-assignments-portal.md)
* [Visa aktivitetsloggar för RBAC ändringar](change-history-report.md)

