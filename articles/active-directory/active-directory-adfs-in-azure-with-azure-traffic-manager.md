---
title: AD FS-distribution med hög tillgänglighet över geografiska områden i Azure med Azure Traffic Manager | Microsoft Docs
description: I det här dokumentet lär du dig hur du distribuerar AD FS i Azure för hög tillgänglighet.
keywords: AD FS med Azure traffic manager, AD FS med Azure Traffic Manager, geografisk, multidatacenter, geografiska datacenter, multigeografiska datacenter, distribuera AD FS i azure, distribuera azure adfs, azure adfs, azure ad fs, distribuera adfs, distribuera ad fs, adfs i azure, distribuera adfs i azure, distribuera AD FS i azure, adfs azure, introduktion till AD FS, Azure, AD FS i Azure, iaas, ADFS, flytta adfs till azure
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: e984d3d590021e3dd9e46d0f12493889b2acc229
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26604787"
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>AD FS-distribution med hög tillgänglighet över geografiska områden i Azure med Azure Traffic Manager
[AD FS-distribution i Azure](active-directory-aadconnect-azure-adfs.md) innehåller detaljerade riktlinjer för hur du kan distribuera en enkel AD FS-infrastruktur för din organisation i Azure. Den här artikeln innehåller nästa steg för att skapa en distribution av AD FS i Azure med hög tillgänglighet över geografiska områden med hjälp av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Azure Traffic Manager hjälper till att skapa en högpresterande AD FS-infrastruktur med geografisk spridning och hög tillgänglighet för din organisation genom användning av metoder som finns tillgängliga för att passa infrastrukturens olika behov.

En AD FS-infrastruktur med hög tillgänglighet och geografisk spridning möjliggör:

* **Eliminering av enskild felpunkt:** Med redundansfunktionerna i Azure Traffic Manager kan du uppnå en AD FS-infrastruktur med hög tillgänglighet även om ett av datacentren i någon del av världen kraschar
* **Förbättrad prestanda:** Du kan använda distributionen som föreslås i den här artikeln för att tillhandahålla en högpresterande AD FS-infrastruktur som hjälper användare att autentisera snabbare. 

## <a name="design-principles"></a>Designprinciper
![Övergripande design](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

De grundläggande designprinciperna blir samma som de som anges i designprinciperna i artikeln AD FS-distribution i Azure. Diagrammet ovan visar ett enkelt tillägg i den grundläggande distributionen till en annan geografisk region. Nedan visas några saker du bör tänka på när du utökar din distributionen till ett nytt geografiskt område

* **Virtuellt nätverk:** Du bör skapa ett nytt virtuellt nätverk i det geografiska område där du vill distribuera ytterligare en AD FS-infrastruktur. I diagrammet ovan visas Geo1 VNET och Geo2 VNET som de två virtuella nätverken i varsitt geografiskt område.
* **Domänkontrollanter och AD FS-servrar i nya geografiska VNET:** Vi rekommenderar distributionen av domänkontrollanter i det nya geografiska området så att AD FS-servrar i det nya området inte behöver kontakta en domänkontrollant i ett annat nätverk långt bort för att slutföra en autentisering, och därmed förbättra prestandan.
* **Lagringskonton:** Lagringskonton som är associerade med ett område. Eftersom du kommer att distribuera datorer i ett nytt geografiskt område måste du skapa nya lagringskonton som ska användas i området.  
* **Nätverkssäkerhetsgrupper:** Liksom lagringskonton kan nätverkssäkerhetsgrupper som skapas i ett område inte kan användas i ett annat geografiskt område. Därför måste du skapa nya nätverkssäkerhetsgrupper liknande dem i det första geografiska området för INT- och DMZ-undernät i det nya geografiska området.
* **DNS-etiketter för offentliga IP-adresser:** Azure Traffic Manager kan endast referera till slutpunkter via DNS-etiketter. Du måste därför skapa DNS-etiketter för den externa belastningsutjämnarens offentliga IP-adresser.
* **Azure Traffic Manager:** Microsoft Azure Traffic Manager låter dig styra distributionen av användartrafik till dina tjänstslutpunkter som körs i olika datacenter runtom i världen. Azure Traffic Manager arbetar på DNS-nivå. DNS-svar används för att dirigera användartrafik till globalt distribuerade slutpunkter. Klienterna ansluter därefter till dessa slutpunkter direkt. Med olika routningsalternativ för prestanda, viktat och prioritet kan du enkelt välja det routningsalternativ som passar bäst för din organisations behov. 
* **V-net till V-net anslutningsmöjlighet mellan två områden:** Du behöver inte ha anslutning mellan de virtuella nätverken i sig. Eftersom varje virtuellt nätverk har åtkomst till domänkontrollanter och själva har AD FS och WAP-server i sig kan de fungera utan någon anslutning mellan virtuella nätverk i olika områden. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Steg för att integrera Azure Traffic Manager
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Distribuera AD FS i det nya geografiska området
Följ stegen och riktlinjerna i [AD FS-distribution i Azure](active-directory-aadconnect-azure-adfs.md) för att distribuera samma topologi i det nya geografiska området.

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>DNS-etiketter för offentliga IP-adresser till internetuppkopplade (offentliga) belastningsutjämnare
Som nämns ovan kan Azure Traffic Manager endast referera till DNS-etiketter som slutpunkter och därför är det viktigt att skapa DNS-etiketter för den externa belastningsutjämnarens offentliga IP-adresser. Skärmbilden nedan visar hur du kan konfigurera din DNS-etikett för den offentliga IP-adressen. 

![DNS-etikett](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Distribuera Azure Traffic Manager
Följ stegen nedan om du vill skapa en Traffic Manager-profil. Mer information finns även i [Hantera en Azure Traffic Manager-profil](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Skapa en Traffic Manager-profil:** Ge din Traffic Manager-profil ett unikt namn. Namnet på profilen är en del av DNS-namnet och fungerar som ett prefix för Traffic Managers domännamnsetikett. Namn/prefix läggs till i .trafficmanager.net för att skapa en DNS-etikett för din Traffic Manager. Skärmbilden nedan visa Traffic Manager DNS-prefix angett som mysts och den resulterande DNS-etiketten kommer att bli mysts.trafficmanager.net. 
   
    ![Skapa Traffic Manage-profil](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Trafikroutningmetod:** Det finns tre routningalternativ i Traffic Manager:
   
   * Prioritet 
   * Prestanda
   * Viktad
     
     **Prestanda** är det rekommenderade alternativet för att uppnå en högresponsiv AD FS-infrastruktur. Du kan dock välja den routningmetod som bäst passar för dina distributionsbehov. AD FS-funktionaliteten påverkas inte av det routingalternativ som valts. Se [Traffic Manager trafikroutningsmetoder](../traffic-manager/traffic-manager-routing-methods.md) för mer information. Iovanstående skärmbildsexempel kan du se den valda metoden **Prestanda**.
3. **Konfigurera slutpunkter:** På Traffic Manager-sidan klickar du på slutpunkter och väljer Lägg till. Då öppnas en Lägg till slutpunkt-sida liknande skärmbilden nedan
   
   ![Konfigurera slutpunkter](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   Följ riktlinjer nedan för olika indata:
   
   **Typ:** Välj Azure slutpunkt eftersom vi kommer att peka till en Azure offentlig IP-adress.
   
   **Namn:** Skapa ett namn som du vill associera med slutpunkten. Detta är inte DNS-namnet och det har inte någon bäring på DNS-poster.
   
   **Målresurstyp:** Välj offentlig IP-adress som värde för denna egenskap. 
   
   **Målresurs:** Detta ger dig möjlighet att välja från olika DNS-etiketter som finns tillgängliga i din prenumeration. Välj DNS-etiketten som motsvarar den slutpunkt som du konfigurerar.
   
   Lägg till slutpunkt för varje geografiskt område som du vill att Azure Traffic Manager ska dirigera trafik till.
   Mer information och detaljerade steg för hur du lägger till/konfigurerar slutpunkter i Traffic Manager finns i [Lägg till, inaktivera, aktivera eller ta bort slutpunkter](../traffic-manager/traffic-manager-endpoints.md)
4. **Konfigurera avsökning:** Klicka på Konfigurering på Traffic Manager-sidan. På konfigureringssidan måste du ändra övervakningsinställningarna till att avsöka vid http-port 80 och relativa sökvägen /adfs/probe
   
    ![Konfigurera avsökning](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Se till att status för slutpunkterna är ONLINE när konfigurationen är klar**. Om alla slutpunkterna är i status 'degraderad' kommer Azure Traffic Manager göra ett bästa försök att routa trafiken och förutsätta att diagnostiken är felaktig och att alla slutpunkter kan nås.
   > 
   > 
5. **Modifiera DNS-poster för Azure Traffic Manager:** Din federationstjänst ska vara en CNAME-post till Azure Traffic Managers DNS-namn. Skapa en CNAME-post i de offentliga DNS-posterna så att alla som försöker nå federationstjänsten faktiskt når Azure Traffic Manager.
   
    För att till exempel peka federation service fs.fabidentity.com till Traffic Manager skulle du behöva uppdatera din DNS-resurspost till att vara följande:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Testa routning och AD FS-inloggning
### <a name="routing-test"></a>Routningtest
Ett mycket grundläggande test för routningen är att försöka pinga federationstjänstens DNS-namn från en dator i varje geografiskt område. Beroende på vald routningmetod kommer den slutpunkt som den faktiskt pingar att reflekteras i pingvisningen. Om du till exempel har valt prestandaroutning kommer slutpunkten närmast klientens område att nås. Nedan visas en ögonblicksbild av två pingar från två klientdatorer i olika regioner, en i Östasien och en i västra USA. 

![Routningtest](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>AD FS-inloggningstest
Det enklaste sättet är att testa AD FS är med hjälp av sidan IdpInitiatedSignon.aspx. För att kunna göra det måste IdpInitiatedSignOn vara aktiverat i AD FS-egenskaperna. Kontrollera din AD FS-konfiguration genom att följa stegen nedan.

1. Kör cmdleten nedan på AD FS-servern med hjälp av PowerShell för att aktivera egenskapen. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. Gå till https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx från valfri extern dator
3. Du bör se en AD FS-sida som den här:
   
    ![AD FS-test - autentiseringsfråga](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    och om inloggningen lyckas visas ett meddelande som det här nedan:
   
    ![AD FS-test - lyckad autentisering](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Relaterade länkar
* [Grundläggande AD FS-distribution i Azure](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Traffic Manager routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Nästa steg
* [Hantera en Azure Traffic Manager-profil](../traffic-manager/traffic-manager-manage-profiles.md)
* [Lägga till, inaktivera, aktivera eller ta bort slutpunkter](../traffic-manager/traffic-manager-endpoints.md) 

