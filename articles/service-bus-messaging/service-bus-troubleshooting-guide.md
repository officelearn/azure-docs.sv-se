---
title: Felsökningsguide för Azure Service Bus | Microsoft-dokument
description: Den här artikeln innehåller en lista över undantag från Azure Service Bus-meddelanden och föreslog åtgärder som ska vidtas när undantaget inträffar.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887780"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Felsökningsguide för Azure Service Bus
Den här artikeln innehåller felsökningstips och rekommendationer för några problem som kan visas när du använder Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Anslutnings-, certifikat- eller timeout-problem
Följande steg kan hjälpa dig att felsöka anslutnings-/certifikat-/timeout-problem för alla tjänster under *.servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Det hjälper med att kontrollera om du har IP-filtrering eller virtuellt nätverk eller certifikatkedjeproblem (vanligast när du använder java SDK).

    Ett exempel på lyckat meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på felmeddelande av fel:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrollera om någon port är blockerad i brandväggen. Portar som används är 443 (HTTPS), 5671 (AMQP) och 9354 (Net Messaging/SBMP). Beroende på vilket bibliotek du använder används även andra portar. Här är exempelkommandot som kontrollerar om 5671-porten är blockerad. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    På Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns återkommande anslutningsproblem kör du följande kommando för att kontrollera om det finns några ignorerade paket. Det här kommandot kommer att försöka upprätta 25 olika TCP-anslutningar var 1 sekund med tjänsten. Sedan kan du kontrollera hur många av dem som lyckades/misslyckades och även se TCP-anslutningsfördröjning. Du kan `psping` ladda ner verktyget [härifrån](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder `tnc` `ping`andra verktyg, till exempel , och så vidare. 
- Hämta en nätverksspårning om de föregående stegen inte hjälper och analyserar den med verktyg som [Wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problem som kan uppstå vid serviceuppgraderingar/omstarter
Uppgraderingar och omstarter av backend-tjänsten kan orsaka följande inverkan på dina program:

- Begäranden kan kort hållas strypt.
- Det kan finnas en minskning av inkommande meddelanden/begäranden.
- Loggfilen kan innehålla felmeddelanden.
- Programmen kan kopplas från tjänsten i några sekunder.

Om programkoden använder SDK är återförsöksprincipen redan inbyggd och aktiv. Programmet återansluter utan någon större påverkan på programmet/arbetsflödet.

## <a name="unauthorized-access-send-claims-are-required"></a>Obehörig åtkomst: Skicka anspråk krävs
Det här felet kan visas när du försöker komma åt ett Service Bus-ämne från Visual Studio på en lokal dator med hjälp av en användartilldelad hanterad identitet med sändningsbehörighet.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

LÃ¶s problemet genom att installera biblioteket [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Mer information finns i [Autentisering av lokal utveckling](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Mer information om hur du tilldelar behörigheter till roller finns i [Autentisera en hanterad identitet med Azure Active Directory för åtkomst till Azure Service Bus-resurser](service-bus-managed-service-identity.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Azure Resource Manager undantag](service-bus-resource-manager-exceptions.md). Den listundantag som genereras när du interagerar med Azure Service Bus med Azure Resource Manager (via mallar eller direktsamtal).
- [Undantag för meddelanden](service-bus-messaging-exceptions.md). Den innehåller en lista över undantag som genereras av .NET Framework för Azure Service Bus. 

