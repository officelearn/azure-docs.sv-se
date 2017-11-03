---
title: "Utfärdarens namn och nyckeln för utfärdaren i BizTalk-tjänst | Microsoft Docs"
description: "Lär dig mer om att hämta Utfärdarens namn och utfärdaren nyckeln för Service Bus eller Access Control (ACS) i BizTalk-tjänst. MABS WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: b9fd985c23558596408e78eadae00dd0f95c4214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Utfärdarens namn och nyckel

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-tjänster använder Service Bus Utfärdarens namn och nyckeln för utfärdaren, och Access Control Utfärdarens namn och nyckeln för utfärdaren. Närmare bestämt:

| Aktivitet | Vilka Utfärdarens namn och en utfärdare nyckel |
| --- | --- |
| Distribuera programmet från Visual Studio |Access Control Utfärdarens namn och en utfärdare nyckel |
| Konfigurera Azure BizTalk-Services-portalen |Access Control Utfärdarens namn och en utfärdare nyckel |
| Skapa LOB-reläer med BizTalk-tjänst för nätverkskort i Visual Studio |Service Bus Utfärdarens namn och en utfärdare nyckel |

Det här avsnittet beskrivs stegen för att hämta Utfärdarens namn och nyckeln för utfärdaren. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Access Control Utfärdarens namn och en utfärdare nyckel
Access Control Utfärdarens namn och utfärdaren nyckeln används av följande:

* Ditt program för Azure BizTalk-tjänst som skapats i Visual Studio: för att distribuera programmet BizTalk Service i Visual Studio till Azure, ange Access Control Utfärdarens namn och nyckeln för utfärdaren. 
* Azure BizTalk-Services-portalen: När du skapar en BizTalk Service och öppna BizTalk-Services-portalen dina Access Control Utfärdarens namn och nyckeln för utfärdaren registreras automatiskt för dina distributioner med samma värden för åtkomstkontroll.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Access Control Utfärdarens namn och utfärdaren nyckel

Om du vill använda ACS för autentisering och hämta värdena Utfärdarens namn och nyckeln för utfärdaren, omfattar övergripande stegen:

1. Installera den [Azure Powershell-cmdlets](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Lägg till ditt Azure-konto:`Add-AzureAccount`
3. Returnera namnet på din prenumeration:`get-azuresubscription`
4. Välj din prenumeration:`select-azuresubscription <name of your subscription>` 
5. Skapa ett nytt namnområde:`new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Exempel:`new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. När det nya ACS-namnområdet har skapats (vilket kan ta flera minuter), anges Utfärdarens namn och nyckeln för utfärdaren värdena i anslutningssträngen: 

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
Utfärdarens namn = SharedSecretIssuer  
Utfärdaren nyckel = SharedSecretKey

Mer på den [ny AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx) cmdlet. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus Utfärdarens namn och en utfärdare nyckel
Service Bus Utfärdarens namn och nyckeln för utfärdaren som används av BizTalk-tjänst för nätverkskortet. I projektet BizTalk-tjänst i Visual Studio använder du BizTalk Adapter-tjänster för att ansluta till ett lokalt branschspecifika (LOB)-system. För att ansluta, skapa LOB-relä och ange din information för LOB-system. När du gör detta måste ange du också Service Bus Utfärdarens namn och nyckeln för utfärdaren.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Hämta Service Bus Utfärdarens namn och utfärdaren nyckel
1. Logga in på den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. I det vänstra navigeringsfönstret väljer **Service Bus**.
3. Välj namnområdet. I Aktivitetsfältet och välj **anslutningsinformationen**. Visar den **standard utfärdaren** (Utfärdarens namn) och **standard nyckeln** (utfärdaren nyckel). Deras värden kan kopieras.  

Sammanfattningsvis:  
Utfärdarens namn = standard utfärdare  
Utfärdaren nyckel = Standardnyckeln

## <a name="next"></a>Nästa
Avsnitt om ytterligare Azure BizTalk-tjänst:

* [Installera Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Självstudier: Azure BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Se även
* [Så här: använda ACS Management-tjänsten för att konfigurera tjänstidentiteter](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk-tjänst: Utvecklare, Basic, Standard och Premium-utgåvor diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk-tjänst: Etablering med hjälp av Azure klassiska portal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Etablering av statusdiagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Säkerhetskopiering och återställning](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Begränsning](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

