---
title: Skapa och hantera Hybridanslutningar | Microsoft Docs
description: Lär dig mer om att skapa en hybridanslutning, hantera anslutningen och installera Hybridanslutningshanteraren. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 9d659262195fef0cc6871bac409dd5914b70f401
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916129"
---
# <a name="create-and-manage-hybrid-connections"></a>Skapa och hantera hybridanslutningar

> [!IMPORTANT]
> BizTalk-Hybridanslutningar har dragits tillbaka och ersatts med App Service Hybrid-anslutningar. Mer information, inklusive hur du hanterar din befintliga BizTalk Hybridanslutningar finns i [Hybridanslutningar för Azure App Service](../app-service/app-service-hybrid-connections.md).
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Översikt över stegen
1. Skapa en Hybridanslutning genom att ange den **värdnamn** eller **FQDN** av en lokal resurs i ditt privata nätverk.
2. Länka ditt Azure web apps eller Azure-appar till Hybridanslutningen.
3. Installera Hybridanslutningshanteraren på den lokala resursen och ansluta till specifika Hybridanslutningen. Azure-portalen är det enda musklick för att installera och ansluta.
4. Hantera Hybridanslutningar och anslutningsnycklar.

Det här avsnittet listar de här stegen. 

> [!IMPORTANT]
> Det är möjligt att ställa in en Hybridanslutning slutpunkt på en IP-adress. Om du använder en IP-adress kan du kanske eller kanske inte att nå den lokala resursen, beroende på din klient. Hybridanslutningen är beroende av klienten gör en DNS-sökning. I de flesta fall den **klienten** är din programkod. Om klienten inte utför en DNS-sökning (det görs inga försök att matcha IP-adressen som om det är ett domännamn (x.x.x.x)), och sedan trafik inte skickas via Hybridanslutningen.
> 
> Till exempel (pseudocode) som du definierar **10.4.5.6** som din lokala värd:
> 
> **Följande scenario fungerar:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on premises host`
> 
> **Följande scenario fungerar inte:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Skapa en Hybridanslutning
Du kan skapa en Hybridanslutning i [Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) **eller** med [BizTalk Services REST API: er](/previous-versions/azure/reference/dn232347(v=azure.100)). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Ytterligare adresser
* Du kan skapa flera Hybridanslutningar. Se den [BizTalk Services: Diagram över utgåvor](biztalk-editions-feature-chart.md) för antalet tillåtna anslutningar. 
* Varje Hybridanslutning har skapats med ett par anslutningssträngar: Nycklar för programmet som skickar och nycklar som lyssnar på plats. Varje par har en primär och en sekundär nyckel. 

## <a name="LinkWebSite"></a>Länka ditt Azure App Service-Webbapp eller Mobilapp
Om du vill länka en Webbapp eller Mobilapp i Azure App Service till en befintlig Hybridanslutning, Välj **använda en befintlig anslutning för Hybrid** på bladet Hybrid Connections. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Installera Hybridanslutningshanteraren lokala platser
När en Hybridanslutning har skapats kan du installera Hybridanslutningshanteraren på lokal resurs. Den kan hämtas från Azure-webbappar eller från BizTalk Service. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) är också en bra resurs.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Ytterligare adresser
* Hybridanslutningshanteraren kan installeras på följande operativsystem:
  
  * Windows Server 2008 R2 (.NET Framework 4.5 + och Windows Management Framework 4.0 + krävs)
  * Windows Server 2012 (Windows Management Framework 4.0 + krävs)
  * Windows Server 2012 R2
* När du har installerat Hybridanslutningshanteraren inträffar följande: 
  
  * Hybridanslutningen finns i Azure konfigureras automatiskt för att använda primär anslutningssträng i programmet. 
  * Den lokala resursen konfigureras automatiskt för att använda lokal primär anslutningssträng.
* Hybridanslutningshanteraren måste använda en giltig lokal anslutningssträng för auktorisering. Azure Web Apps eller Mobile Apps måste använda ett giltigt program-anslutningssträngen för auktorisering.
* Du kan skala Hybrid Connections genom att installera en annan instans av Hybridanslutningshanteraren på en annan server. Konfigurera lokala lyssnaren för att använda samma adress som den första lyssnaren på plats. I det här fallet är trafiken slumpmässigt distribuerade (round robin) mellan de aktiva lokala lyssnarna. 

## <a name="ManageHybridConnection"></a>Hantera Hybridanslutningar

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) är också en bra resurs.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Kopiera/återskapa Hybrid-anslutningssträngar

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) är också en bra resurs.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Använd grupprinciper för att styra de lokala resurser som används av en Hybridanslutning
1. Ladda ned den [Hybridanslutningshanteraren Administrationsmallar](https://www.microsoft.com/download/details.aspx?id=42963).
2. Extrahera filerna.
3. På den dator som ändrar en Grupprincip, gör du följande:  
   
   * Kopiera den. ADMX-filer till den *%WINROOT%\PolicyDefinitions* mapp.
   * Kopiera den. ADML-filer till den *%WINROOT%\PolicyDefinitions\en-us* mapp.

Har kopierats kan använda du Redigeraren för att ändra principen.

## <a name="next"></a>Nästa
[Översikt över hybridanslutningar](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Se även
[REST API för att hantera BizTalk Services i Microsoft Azure](/previous-versions/azure/reference/dn232347(v=azure.100))  
[BizTalk Services: Versionsdiagram](biztalk-editions-feature-chart.md)  
[Skapa en BizTalk-tjänst](biztalk-provision-services.md)  
[BizTalk Services: Flikarna instrumentpanel, Övervakare och skalning](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
