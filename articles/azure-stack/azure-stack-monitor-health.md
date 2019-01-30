---
title: Övervaka hälsotillstånd och aviseringar i Azure Stack | Microsoft Docs
description: Lär dig mer om att övervaka hälsotillstånd och aviseringar i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 3e37b8f45d7068586d20b9c4b3ccdabb017d0416
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242866"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Övervaka hälsotillstånd och aviseringar i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack omfattar övervakning av infrastruktur funktioner som hjälper dig att visa hälsotillstånd och aviseringar för en Azure Stack-region. Den **regionshantering** panel, Fäst som standard i administratörsportalen för Providerprenumeration standard visar en lista över alla distribuerade regioner där Azure Stack. Panelen visar antal aktiva kritiska meddelanden och varningsmeddelanden aviseringar för varje region. Panelen är ingången till hälsotillstånd och aviseringar funktionerna i Azure Stack.

![Panelen Regionshantering](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Förstå health i Azure Stack

Resursprovidern hälsotillstånd hanterar hälsotillstånd och aviseringar. Azure Stack-infrastrukturkomponenter registrera med resursprovidern hälsa under Azure Stack-distribution och konfiguration. Den här registreringen gör det möjligt för visning av hälsotillstånd och aviseringar för varje komponent. Health i Azure Stack är ett enkelt begrepp. Om det finns aviseringar för en registrerad instans av en komponent, hälsotillståndet för komponenten återspeglar sämsta active aviseringens allvarlighetsgrad: varning eller kritisk.

## <a name="alert-severity-definition"></a>Allvarlighetsgrad för avisering definition

I Azure Stack genererar aviseringar med bara två allvarlighetsgrader: **varning** och **kritiska**.

- **Varning**  
  En operatör kan lösa varningen på ett sätt som är schemalagda. Aviseringen påverkar vanligtvis inte användaren arbetsbelastningar.

- **Kritiska**  
  En operatör bör hantera kritisk varning med angelägenhetsgrad. Det här är problem som håller på att påverka eller påverkar snart Azure Stack-användare.


## <a name="view-and-manage-component-health-state"></a>Visa och hantera komponentens hälsotillstånd

Du kan visa hälsotillståndet för komponenter i administratörsportalen och via REST-API och PowerShell.

Om du vill visa hälsotillståndet i portalen klickar du på den region som du vill visa i den **regionshantering** panelen. Du kan visa hälsotillstånd av infrastrukturroller och resursprovidrar.

![Lista över infrastrukturroller som](media/azure-stack-monitor-health/image2.png)

Du kan klicka på en resursprovider eller infrastrukturrollen om du vill visa mer detaljerad information.

> [!WARNING]  
> Om du klickar på en infrastruktur-roll och klicka sedan på rollinstansen, det finns alternativ för att **starta**, **starta om**, eller **avstängning**. Använd inte de här åtgärderna när du tillämpar uppdateringar på ett integrerat system. Utför **inte** använda dessa alternativ i en miljö med Azure Stack Development Kit. Dessa alternativ bara är utformade för en miljö med integrerade system, där det finns fler än en rollinstans per infrastrukturrollen. Starta om en rollinstans (särskilt AzS-Xrp01) i development kit gör datorn instabil. Publicera din fråga till felsökningshjälp den [Azure Stack-forum](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Visa aviseringar

Lista över aktiva aviseringar för varje Azure Stack-region som är tillgängliga direkt från den **regionshantering** bladet. Den första ikonen i standardkonfigurationen är den **aviseringar** panelen som visar en sammanfattning av de viktiga och varningar för regionen. Du kan fästa panelen aviseringar som andra panelen på det här bladet på instrumentpanelen för snabb åtkomst.

![Aviseringar panelen som visar en varning](media/azure-stack-monitor-health/image3.png)

Genom att välja den övre delen av den **aviseringar** sida vid sida, gå till listan över alla aktiva varningar för regionen. Om du väljer du antingen den **kritisk** eller **varning** radartikel i panelen, som du navigerar till en filtrerad lista över aviseringar (kritiskt eller varning). 

Den **aviseringar** bladet stöder möjligheten att filtrera både (aktiv eller stängd) och allvarlighetsgrad (kritiskt eller varning). Standardvyn visar alla aktiva aviseringar. Alla stängda aviseringar tas bort från systemet efter sju dagar.

![Filterfönstret för att filtrera efter kritiska eller Varningsstatus](media/azure-stack-monitor-health/alert-view.png)

Den **visa API** visar REST-API som används för att generera listvyn. Den här åtgärden ger ett snabbt sätt att bekanta dig med REST API-syntax som du kan använda för att fråga aviseringar. Du kan använda detta API i automation eller för integrering med ditt befintliga datacenter övervakning, rapportering och biljetter lösningar.

Du kan klicka på en specifik avisering om du vill visa mer information. Varningsinformationen visar alla fält som är kopplade till aviseringen och gör navigeringen snabb till berörda komponenten och källan till aviseringen. Följande avisering inträffar till exempel om en av rollinstanserna infrastruktur kopplas från eller är inte tillgänglig.  

![Bladet aviseringsinformation](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Reparera aviseringar

Du kan välja **reparera** i vissa aviseringar.

När du väljer den **reparera** åtgärd utför åtgärder som är specifika för aviseringen att lösa problemet. När valt statusen för den **reparera** åtgärden är tillgänglig som en portal-meddelande.

![Reparation pågår](media/azure-stack-monitor-health/repair-in-progress.png)

Den **reparera** rapporterar åtgärden lyckades eller underlåtenhet att slutföra åtgärden i samma portal-meddelande blad.  Om en reparationsåtgärden misslyckas för en varning, kan du köra den **reparera** åtgärd från sådant. Om reparationsåtgärden har slutförts **inte** kör den **reparera** åtgärd.

![Reparationen har slutförts](media/azure-stack-monitor-health/repair-completed.png)

Efter rollinstans infrastruktur är tillbaka online, stängs automatiskt den här aviseringen. Många, men inte alla aviseringen stängs automatiskt när det underliggande problemet har åtgärdats. Aviseringar som ger en reparation åtgärd knapp stängs automatiskt om Azure Stack löser problemet.  För alla andra aviseringar, väljer **Stäng avisering** när du har utfört steg. Om problemet kvarstår, genererar en ny avisering i Azure Stack. Om du har löst problemet aviseringen är stängd och kräver inga fler steg.

## <a name="next-steps"></a>Nästa steg

[Hantera uppdateringar i Azure Stack](azure-stack-updates.md)

[Regionshantering i Azure Stack](azure-stack-region-management.md)
