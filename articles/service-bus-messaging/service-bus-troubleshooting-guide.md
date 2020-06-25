---
title: Fel söknings guide för Azure Service Bus | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure Service Bus meddelande undantag och föreslagna åtgärder som vidtas när undantaget inträffar.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b2759916e1f9ef0cec660157f577ff54cd39928
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85340457"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Fel söknings guide för Azure Service Bus
Den här artikeln innehåller fel söknings tips och rekommendationer för några problem som kan uppstå när du använder Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problem med anslutning, certifikat eller tids gräns
Följande steg kan hjälpa dig med fel sökning av problem med anslutning/certifikat/tids gräns för alla tjänster under *. servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Det hjälper till med att kontrol lera om du har problem med IP-filtrering eller problem med virtuella nätverk eller certifikat kedjan (vanligt vis när du använder Java SDK).

    Ett exempel på ett slutfört meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på fel meddelande:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrol lera om någon port är blockerad i brand väggen. Portarna som används är 443 (HTTPS), 5671 (AMQP) och 9354 (net Messaging/SBMP). Beroende på vilket bibliotek du använder används även andra portar. Här är exempel kommandot som kontrollerar om 5671-porten är blockerad. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    I Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot görs ett försök att upprätta 25 olika TCP-anslutningar varje sekund med tjänsten. Sedan kan du kontrol lera hur många av dem som har lyckats/misslyckats och även se TCP-anslutningssträngen. Du kan hämta `psping` verktyget härifrån. [here](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc` , `ping` och så vidare. 
- Få en nätverks spårning om föregående steg inte hjälper och analyseras med verktyg som [wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problem som kan uppstå med tjänst uppgraderingar/omstarter
Uppgraderingar och omstarter av backend-tjänsten kan orsaka följande påverkan på dina program:

- Begär Anden kan begränsas tillfälligt.
- Det kan finnas en minskning av inkommande meddelanden/begär Anden.
- Logg filen kan innehålla fel meddelanden.
- Programmen kan vara frånkopplade från tjänsten under några sekunder.

Om program koden använder SDK är principen för återförsök redan inbyggd och aktiv. Programmet återansluter utan betydande påverkan på programmet/arbets flödet.

## <a name="unauthorized-access-send-claims-are-required"></a>Obehörig åtkomst: skicka anspråk krävs
Du kanske ser det här felet när du försöker komma åt ett Service Bus ämne från Visual Studio på en lokal dator med hjälp av en användardefinierad hanterad identitet med Send-behörighet.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Lös problemet genom att installera biblioteket [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Mer information finns i [lokal utvecklings autentisering](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Information om hur du tilldelar behörigheter till roller finns i [autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Azure Service Bus resurser](service-bus-managed-service-identity.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Azure Resource Manager undantag](service-bus-resource-manager-exceptions.md). Den visar undantag som genereras när du interagerar med Azure Service Bus att använda Azure Resource Manager (via mallar eller direkta anrop).
- [Undantag för meddelanden](service-bus-messaging-exceptions.md). Den innehåller en lista över undantag som har genererats av .NET Framework för Azure Service Bus. 

