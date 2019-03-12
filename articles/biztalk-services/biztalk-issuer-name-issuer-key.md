---
title: Utfärdarens namn och nyckel i BizTalk Services | Microsoft Docs
description: Lär dig hur du hämtar Utfärdarens namn och nyckel för Service Bus eller ACS (Access Control) i BizTalk Services. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 5eac98ec88b960956c9a0931673e67f530aef8da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542190"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Utfärdarens namn och nyckel

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-tjänster som använder Service Bus-Utfärdarens namn och nyckel, och Access Control Utfärdarens namn och nyckel. Närmare bestämt:

| Aktivitet | Vilka Utfärdarens namn och nyckel |
| --- | --- |
| Distribuera ditt program från Visual Studio |Access Control Utfärdarens namn och nyckel |
| Konfigurera Azure BizTalk Services-portalen |Access Control Utfärdarens namn och nyckel |
| Skapa LOB-reläer med BizTalk Adapter-tjänster i Visual Studio |Service Bus-Utfärdarens namn och nyckel |

Det här avsnittet visas hur du kan hämta Utfärdarens namn och nyckel. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Access Control Utfärdarens namn och nyckel
Access Control Utfärdarens namn och nyckel som används av följande:

* Ditt program i Azure BizTalk-tjänst som skapats i Visual Studio: För att distribuera din BizTalk Service-programmet i Visual Studio till Azure, ange åtkomstkontroll Utfärdarens namn och nyckel. 
* Azure BizTalk Services-portalen: När du skapar en BizTalk Service och öppna BizTalk Services-portalen, registreras automatiskt din åtkomstkontroll Utfärdarens namn och nyckel för dina distributioner med samma Access Control-värden.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Få åtkomstkontroll Utfärdarens namn och nyckel

Om du vill använda ACS för autentisering och hämta värdena som Utfärdarens namn och nyckel, är de övergripande stegen:

1. Installera den [Azure Powershell-cmdlets](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Lägg till ditt Azure-konto: `Add-AzureAccount`
3. Returnera ditt prenumerationsnamn: `get-azuresubscription`
4. Välj din prenumeration: `select-azuresubscription <name of your subscription>` 
5. Skapa ett nytt namnområde: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Exempel:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. När det nya ACS-namnområdet skapas (vilket kan ta flera minuter), anges Utfärdarens namn och nyckel värdena i anslutningssträngen: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Sammanfattningsvis:  
Issuer Name = SharedSecretIssuer  
Nyckel = SharedSecretKey

Mer på den [New AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace) cmdlet. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus-Utfärdarens namn och nyckel
Service Bus-Utfärdarens namn och nyckel som används av BizTalk Adapter-tjänster. I din BizTalk Services-projekt i Visual Studio använder du BizTalk Adapter-tjänster för att ansluta till ett lokalt Line-of-Business (LOB)-system. För att ansluta, skapa enheterna LOB-reläer och ange din information för LOB-system. När du gör detta måste ange du också Service Bus-Utfärdarens namn och nyckel.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Att hämta Service Bus-Utfärdarens namn och nyckel
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Sök efter **Service Bus**, och ditt namnområde. 
3. Öppna den **principer för delad åtkomst** egenskaper, Välj din princip och visa den **Connection String** för namn och värden.  

## <a name="next"></a>Nästa
Avsnitt om ytterligare Azure BizTalk Services:

* [Installera SDK för Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Självstudier: Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Se även
* [Anvisningar: Använd ACS Management-tjänsten för att konfigurera tjänstidentiteter](https://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk Services: Developer, Basic, Standard och Premium diagram över utgåvor](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Etablering](https://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Etablering av statusdiagram](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Flikarna instrumentpanel, Övervakare och skalning](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Säkerhetskopiering och återställning](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Begränsning](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

