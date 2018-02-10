---
title: "Felsöka rollbaserad åtkomstkontroll Azure RBAC | Microsoft Docs"
description: "Få hjälp med eller frågor om rollbaserad åtkomstkontroll resurser."
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: seohack1
ms.openlocfilehash: c2589aabce86f848fa1aa3e25b3f78be180c5525
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="troubleshooting-azure-role-based-access-control"></a>Felsökning av rollbaserad åtkomstkontroll i Azure 

Det här dokumentet artikeln innehåller svar på vanliga frågor om särskilda behörigheter som beviljas med roller, så att du vet vad som händer när du använder roller i Azure-portalen och kan felsöka problem med åtkomst till. Dessa roller omfattar alla typer av resurser:

* Ägare  
* Deltagare  
* Läsare  

Ha fullständig åtkomst till hanteringen av ägare och deltagare, men en deltagare kan inte ge åtkomst till andra användare eller grupper. Det blir lite mer intressant med läsarrollen så att den där vi tillbringar lite tid. Finns det [rollbaserad åtkomstkontroll komma-igång artikel](role-based-access-control-configure.md) mer information om hur du beviljar åtkomst.

## <a name="app-service-workloads"></a>App service arbetsbelastningar
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

![Resursgruppen för Web app](./media/role-based-access-control-troubleshooting/website-resource-model.png)

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

## <a name="virtual-machine-workloads"></a>Arbetsbelastningar på virtuella datorer
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

## <a name="see-more"></a>Mer information
* [Rollbaserad åtkomstkontroll](role-based-access-control-configure.md): komma igång med RBAC på Azure-portalen.
* [Inbyggda roller](role-based-access-built-in-roles.md): få information om de roller som levereras som standard i RBAC.
* [Anpassade roller i Azure RBAC](role-based-access-control-custom-roles.md): Lär dig att skapa anpassade roller som passar dina åtkomstbehov.
* [Skapa en profil över åtkomständringshistorik](role-based-access-control-access-change-history-report.md): hålla reda på hur du ändrar rolltilldelningar i RBAC.

