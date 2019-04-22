---
title: Serverdelar och backend-pooler i Azure ytterdörren Service | Microsoft Docs
description: Den här artikeln beskriver vilka serverdelar och backend-adresspooler finns framför dörren konfiguration.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 2372f49c7280ee5c817f3d2f98cc80a196dae5f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879207"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Serverdelar och backend-pooler i Azure ytterdörren Service
Den här artikeln beskriver begrepp om hur du ansluter din distribution med Azure ytterdörren Service. Den förklarar också olika villkoren i ytterdörren konfiguration runt serverdelar.

## <a name="backends"></a>Serverdelar
En serverdel är lika med en app-distribution-instans i en region. Ytterdörren tjänsten stöder både Azure och Azure-serverdelar, så att regionen inte är endast begränsad till Azure-regioner. Det kan också vara ditt lokala datacenter eller en app-instansen i ett annat moln.

Ytterdörren Service serverdelar avser värdnamnet eller offentlig IP-adress för din app, som kan fungera klientbegäranden. Serverdelar bör inte blandas ihop med databasnivån, lagringsnivå och så vidare. Serverdelar bör ses som den offentliga slutpunkten för app-serverdel. När du lägger till en serverdel i en serverdelspool ytterdörren du måste också lägga till följande:

- **Serverdelen värdtyp**. Typ av resurs som du vill lägga till. Ytterdörren tjänsten stöder automatisk identifiering av serverdelen app från app service, cloud Services eller lagring. Om du vill att en annan resurs i Azure eller till och med en icke-Azure-serverdel väljer **anpassade värden**.

    >[!IMPORTANT]
    >API: er verifiera inte om serverdelen inte är tillgänglig från ytterdörren miljöer under konfigurationen. Se till att åtkomsten kan nå serverdelen.

- **Prenumeration och serverdelen värdnamn**. Om du inte har valt **anpassade värden** för serverdelen värdtyp, väljer du din serverdel genom att välja lämplig prenumeration och motsvarande backend-värdnamnet i Användargränssnittet.

- **Serverdelen värdhuvud**. Det värdhuvudsvärde som skickas till serverdelen för varje begäran. Mer information finns i [serverdel värdhuvud](#hostheader).

- **Prioritet**. Tilldela prioritet till serverdelen olika när du vill använda en primär service-serverdelen för all trafik. Dessutom ge säkerhetskopieringar om primärt eller den säkerhetskopierade serverdelen är tillgängliga. Mer information finns i [prioritet](front-door-routing-methods.md#priority).

- **Vikt**. Tilldela vikter till serverdelen olika du distribuerar trafik över en uppsättning serverdelar, jämnt eller enligt vikt koefficienter. Mer information finns i [vikterna](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Värdadress för serverdel

Begäranden som vidarebefordras av åtkomsten till en serverdel innehåller en värd header-fält som serverdelen använder för att hämta resurs. Värdet för det här fältet normalt kommer från serverdelen URI och har värd och port.

Till exempel en begäran för www\.contoso.com har värden rubrik www\.contoso.com. Om du använder Azure-portalen för att konfigurera din serverdel, är standardvärdet för det här fältet namnet på serverdelen. Om din serverdel är contoso-westus.azurewebsites.net i Azure-portalen att autopopulated värdet för serverdelen värdhuvudet contoso westus.azurewebsites.net. Men om du använder Azure Resource Manager-mallar eller någon annan metod utan att explicit ange det här fältet skickar ytterdörren Service inkommande värdnamnet som värde för värdhuvudet. Om en begäran har gjorts för www\.contoso.com och serverdelen är contoso-westus.azurewebsites.net som har ett tomt header-fält, ytterdörren tjänsten anger du värdhuvudet som www\.contoso.com.

De flesta serverdelar (Azure Web Apps, Blob storage och Cloud Services) kräver att värdhuvudet matchar domänen för serverdelen. Dock frontend-värden som dirigerar till serverdelen kommer att använda ett annat värdnamn, till exempel www\.contoso.azurefd.net.

Om serverdelen kräver att värdhuvudet matchar värdnamnet serverdel, se till att värdhuvudet serverdel innehåller värden namnet serverdelen.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurera backend-värdhuvud för serverdelen

Så här konfigurerar du den **serverdel värdhuvud** för en serverdel i avsnittet backend-pool:

1. Öppna ytterdörren resursen och välj serverdelspoolen med serverdelen för att konfigurera.

2. Lägg till en serverdel om du inte har gjort det, eller redigera en befintlig.

3. Ange serverdel huvud anger att ett anpassat värde eller lämna det tomt. Värdnamnet för den inkommande begäranden ska användas som värd huvudets värde.

## <a name="backend-pools"></a>Serverdelspooler
En serverdelspool framför dörren tjänsten hänvisar till uppsättning servrar som tar emot liknande trafik för sina appar. Det är alltså en logisk gruppering av din app-instanser över hela världen som får samma trafik och svara med förväntat beteende. Dessa serverdelar distribueras över olika regioner eller i samma region. Alla serverdelar kan vara i läge för aktiv/aktiv distribution eller vad som har definierats som aktiv/passiv konfiguration.

En serverdelspool definierar hur olika serverdelar bör utvärderas via hälsoavsökningar. Den definierar även hur belastningsutjämning mellan dem sker.

### <a name="health-probes"></a>Hälsoavsökningar
Ytterdörren Service skickar regelbundna begäranden för HTTP/HTTPS-avsökning till var och en av dina konfigurerade serverdelar. Avsökningen begäranden avgör närhet och hälsotillståndet för varje serverdel så att läsa in balansera din förfrågningar från slutanvändare. Inställningar för avsökning av hälsotillstånd för en serverdelspool definierar hur vi avsöka hälsostatus för serverdelar. Följande inställningar är tillgängliga för belastnings-utjämnande konfiguration:

- **Sökvägen**. Den URL som används för avsökning begäranden för alla serverdelar i serverdelspoolen. Till exempel om en av serverdelen är contoso-westus.azurewebsites.net och sökvägen anges till /probe/test.aspx, sedan ytterdörren Service-miljöer, förutsatt att protokollet som har angetts till HTTP, skickar hälsotillstånd avsökningen begäranden till http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokollet**. Definierar om du vill skicka hälsotillstånd avsökningen begäranden från ytterdörren tjänsten till serverdelen med HTTP eller HTTPS-protokollet.

- **Intervall (sekunder)**. Definierar hur ofta hälsoavsökningar för serverdelen, eller hur ofta som var och en av miljöerna som ytterdörren skickar en avsökning.

    >[!NOTE]
    >Ange intervallet för snabbare redundans till ett lägre värde. Ju lägre värde, desto högre volymen för health-avsökningen får serverdelen. Till exempel om intervallet har angetts till 30 sekunder med 90 ytterdörren miljöer eller POP globalt varje serverdel får om 3-5 avsökningen begäranden per sekund.

Mer information finns i [hälsoavsökningar](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Inställningar för belastningsutjämning
Belastningsutjämning inställningarna för serverdelspoolen definierar hur vi utvärdera hälsokontroller av slutpunkter. Inställningarna avgör om serverdelen är felfri eller ohälsosam. De också kontrollera hur du belastningsutjämnar trafik mellan olika serverdelar i serverdelspoolen. Följande inställningar är tillgängliga för belastnings-utjämnande konfiguration:

- **Exempel på storlek**. Anger hur många exempel på hälsoavsökningar som vi behöver tänka på för utvärdering för backend-hälsotillstånd.

- **Lyckad exempelstorlek**. Definierar den exempelstorlek som tidigare nämnts antalet lyckade exempel som behövs för att anropa serverdelen felfritt. Anta exempelvis att en ytterdörren hälsotillstånd avsökningsintervallet är 30 sekunder, exempelstorlek är 5 sekunder och lyckade exempelstorlek är 3 sekunder. Varje gång det utvärdera hälsan avsökningar för din serverdel, vi tittar på de sista fem exempel över 150 sekunder (5 x 30). Minst tre lyckad avsökningar krävs för att deklarera serverdel som felfri.

- **Svarstid känslighet (ytterligare fördröjning)**. Definierar om du vill att dörren att skicka begäran till serverdelar i svarstidsintervallet mätning känslighet eller vidarebefordra begäran till den närmaste serverdelen.

Mer information finns i [lägsta svarstid baserat routningsmetod](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Nästa steg

- [Skapa en ytterdörren-profil](quickstart-create-front-door.md)
- [Hur ytterdörren fungerar](front-door-routing-architecture.md)