---
title: Skapa och hantera Hybridanslutningar | Microsoft Docs
description: Lär dig hur du skapar en hybridanslutning, hantera anslutningen och installera Hybridanslutningshanteraren. MABS WABS
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
ms.openlocfilehash: 1751d33b5f6f6a506654daedd15bbd75ae271483
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26628856"
---
# <a name="create-and-manage-hybrid-connections"></a>Skapa och hantera hybridanslutningar

> [!IMPORTANT]
> BizTalk-Hybridanslutningar har dragits tillbaka och ersatts med App Service Hybrid-anslutningar. Mer information, inklusive hur du hanterar din befintliga BizTalk Hybridanslutningar finns i [Hybridanslutningar för Azure App Service](../app-service/app-service-hybrid-connections.md).

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Översikt över stegen
1. Skapa en Hybridanslutning genom att ange den **värdnamn** eller **FQDN** av lokal resurs i ditt privata nätverk.
2. Länka ditt Azure-webbappar eller Azure mobilappar till Hybrid-anslutningen.
3. Installera Hybridanslutningshanteraren på din lokala resursen och ansluta till specifika Hybridanslutning. Azure-portalen tillhandahåller en enda musklick för att installera och ansluta.
4. Hantera Hybridanslutningar och deras anslutningsnycklar.

Det här avsnittet listar de här stegen. 

> [!IMPORTANT]
> Det är möjligt att ställa in en Hybridanslutning slutpunkt på en IP-adress. Om du använder en IP-adress kan eller inte kan nå lokala resursen, beroende på din klient. Hybrid-anslutningen beror på klienten gör en DNS-sökning. I de flesta fall den **klienten** är programkoden. Om klienten inte att utföra en DNS-sökning (den inte försöker matcha IP-adressen som om det vore ett domännamn (x.x.x.x)), och sedan trafik inte skickas via Hybrid-anslutningen.
> 
> Till exempel (pseudocode) som du definierar **10.4.5.6** som värden lokalt:
> 
> **Följande scenario fungerar:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Följande scenario fungerar inte:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Skapa en Hybridanslutning
En Hybridanslutning kan skapas i [Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) **eller** med [BizTalk Services REST API: er](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Ytterligare adresser
* Du kan skapa flera Hybridanslutningar. Finns det [BizTalk-tjänst: utgåvor diagram](biztalk-editions-feature-chart.md) för antalet tillåtna anslutningar. 
* Varje Hybridanslutningen har skapats med ett par anslutningssträngar: programmet nycklar för att skicka och lokala nycklar som lyssnar. Varje par har en primär och en sekundär nyckel. 

## <a name="LinkWebSite"></a>Länka ditt Azure App Service Webbapp eller Mobilapp
Om du vill länka en Webbapp eller Mobilapp i Azure App Service till en befintlig Hybridanslutning, Välj **använder en befintlig Hybridanslutning** i bladet Hybridanslutningar. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Installera den Hybridanslutningshanteraren lokalt
När en Hybridanslutning har skapats kan du installera Hybridanslutningshanteraren på den lokala resursen. Du kan hämta från Azure-webbappar eller BizTalk Service. 

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
  
  * Hybridanslutningen i Azure konfigureras automatiskt för att använda programmet primära anslutningssträngen. 
  * Den lokala resursen konfigureras automatiskt för att använda primära lokala anslutningssträngen.
* Hybridanslutningshanteraren måste använda en giltig lokal anslutningssträng för auktorisering. Azure-Webbappar eller Mobilappar måste använda ett giltigt program-anslutningssträngen för auktorisering.
* Du kan skala Hybridanslutningar genom att installera en annan instans av Hybridanslutningshanteraren på en annan server. Konfigurera lokala lyssnaren för att använda samma adress som den första lokala lyssnaren. I så fall är trafiken slumpmässigt distribuerade (round robin) mellan active lokalt lyssnare. 

## <a name="ManageHybridConnection"></a>Hantera Hybridanslutningar

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) är också en bra resurs.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Kopiera/generera Hybrid-anslutningssträngar

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) är också en bra resurs.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Använda grupprincipinställningar för att styra vilka lokala resurser som används av en Hybridanslutning
1. Hämta den [Hybridanslutningshanteraren Administrationsmallar](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extrahera filerna.
3. På den dator som ändrar en Grupprincip, gör du följande:  
   
   * Kopiera den. ADMX-filer till den *%WINROOT%\PolicyDefinitions* mapp.
   * Kopiera den. ADML-filer till den *%WINROOT%\PolicyDefinitions\en-us* mapp.

När de har kopierats, kan du använda redigeraren för att ändra principen.

## <a name="next"></a>Nästa
[Översikt över hybrid-anslutningar](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Se även
[REST-API för att hantera BizTalk-tjänster på Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Diagram över utgåvor](biztalk-editions-feature-chart.md)  
[Skapa en BizTalk-tjänst](biztalk-provision-services.md)  
[BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
