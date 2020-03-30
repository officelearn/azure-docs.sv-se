---
title: Backends och backend pooler i Azure Ytterdörr | Microsoft-dokument
description: I den här artikeln beskrivs vad serverdels- och serverdelspooler finns i konfigurationen av ytterdörren.
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
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293475"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Backends och backend pooler i Azure Ytterdörr
I den här artikeln beskrivs begrepp om hur du mappar appdistributionen med Azure Front Door. Det förklarar också de olika termerna i Front Door konfiguration runt app backends.

## <a name="backends"></a>Serverdelar
En serverning är lika med en apps distributionsinstans i en region. Ytterdörren stöder både Azure- och icke-Azure-backends, så regionen är inte bara begränsad till Azure-regioner. Det kan också vara ditt lokala datacenter eller en appinstans i ett annat moln.

Front Door-serverdelen refererar till värdnamnet eller den offentliga IP-adressen för din app, som kan betjäna klientförfrågningar. Backends bör inte förväxlas med din databasnivå, lagringsnivå och så vidare. Backends ska ses som den offentliga slutpunkten för appens backend. När du lägger till en backend i en backend-pool för ytterdörren måste du också lägga till följande:

- **Värdtyp för backend**. Den typ av resurs som du vill lägga till. Ytterdörren stöder automatisk upptäckt av appens backends från apptjänst, molntjänst eller lagring. Om du vill ha en annan resurs i Azure eller till och med en icke-Azure-backend väljer du **Anpassad värd**.

    >[!IMPORTANT]
    >Under konfigurationen validerar API:er inte om serverdelen inte är tillgänglig från ytterdörrens miljöer. Se till att ytterdörren kan nå din bakdel.

- **Värdnamn för prenumeration och namn på namnet på namnet på namnet på namnet på namnet på namnet på namnet på namnet på namnet på namnet** Om du inte har valt **Anpassad värd** för värdtyp för backend väljer du din backend genom att välja lämplig prenumeration och motsvarande värdnamn för backend i användargränssnittet.

- **Värdhuvudet för backend**. Värdhuvudvärdet som skickas till serverhuvudet för varje begäran. Mer information finns i [Backend-värdhuvudet](#hostheader).

- **Prioritet**. Tilldela prioriteter till dina olika backends när du vill använda en primär tjänst backend för all trafik. Ange även säkerhetskopior om de primära eller säkerhetskopieringsbackarna inte är tillgängliga. Mer information finns i [Prioritet](front-door-routing-methods.md#priority).

- **Vikt**. Tilldela vikter till dina olika backends för att fördela trafik över en uppsättning backends, antingen jämnt eller enligt viktkoefficienter. Mer information finns i [Vikter](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Värdhuvud för bakåtsträvning

Begäranden som vidarebefordras via ytterdörren till en bakåtsida innehåller ett värdhuvudfält som backend använder för att hämta den riktade resursen. Värdet för det här fältet kommer vanligtvis från backend URI och har värd och port.

En begäran som görs `www.contoso.com` för har till exempel värdhuvudet www.contoso.com. Om du använder Azure-portalen för att konfigurera serveringen är standardvärdet för det här fältet värdnamnet för serverd. Om din backend är contoso-westus.azurewebsites.net, i Azure-portalen, kommer det autobefolkade värdet för backend-värdhuvudet att vara contoso-westus.azurewebsites.net. Men om du använder Azure Resource Manager-mallar eller någon annan metod utan att uttryckligen ange det här fältet, skickar Front Door det inkommande värdnamnet som värde för värdhuvudet. Om begäran gjordes för\.www contoso.com och din backend är contoso-westus.azurewebsites.net som har ett tomt huvudfält,\.kommer Ytterdörren att ange värdhuvudet som www contoso.com.

De flesta appserverade (Azure Web Apps, Blob storage och Cloud Services) kräver att värdhuvudet matchar domänen för backend. Den klientdelsvärd som dirigerar till din backend använder dock ett annat värdnamn, till exempel www.contoso.net.

Om din backend kräver att värdhuvudet matchar värdnamnet för att matcha värdnamnet för backend kontrollerar du att värdhuvudet innehåller värdnamnets sidhuvud.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurera serverhuvudet för serverhuvudet för serverhuvudet

Så här konfigurerar du **serverdelshuvudfältet** för en serverdel i serverdelspoolavsnittet:

1. Öppna din front door-resurs och välj serverdelspoolen med serverdelen för att konfigurera.

2. Lägg till en backend om du inte har gjort det, eller redigera en befintlig.

3. Ställ in värdhuvudfältet för värdhuvudet för backend till ett anpassat värde eller lämna det tomt. Värdnamnet för den inkommande begäran används som värdhuvudvärde.

## <a name="backend-pools"></a>Backend-pooler
En backend pool i Ytterdörren hänvisar till den uppsättning backends som får liknande trafik för sin app. Med andra ord är det en logisk gruppering av dina appinstanser över hela världen som tar emot samma trafik och svarar med förväntat beteende. Dessa backends distribueras över olika regioner eller inom samma region. Alla serversida kan vara i aktivt/aktivt distributionsläge eller vad som definieras som aktiv/passiv konfiguration.

En serverningspool definierar hur de olika serversekvenserna ska utvärderas via hälsoavsökningar. Den definierar också hur belastningsutjämning sker mellan dem.

### <a name="health-probes"></a>Hälsotillståndsavsökningar
Ytterdörren skickar periodiska HTTP/HTTPS-avsökningsbegäranden till var och en av dina konfigurerade serverdelar. Avsökningsbegäranden avgör närheten och hälsan för varje serverdel för att belastningsutjämna dina slutanvändares begäranden. Hälsoavsökningsinställningar för en serverd-pool definierar hur vi avsöker hälsostatus för app-serverd. Följande inställningar är tillgängliga för belastningsutjämningskonfiguration:

- **Sökväg**: URL:en som används för avsökningsbegäranden för alla serverdelar i serverdapoolen. Om till exempel en av dina serverdelar är contoso-westus.azurewebsites.net och sökvägen är inställd på /probe/test.aspx, skickar frontdörrmiljöer, förutsatt att\:protokollet är inställt på HTTP, hälsoavsökningsbegäranden till http //contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokoll**: Definierar om hälsoavsökningsbegäranden ska skickas från ytterdörren till serverdelserna med HTTP- eller HTTPS-protokoll.

- **Metod**: DEN HTTP-metod som ska användas för att skicka hälsoavsökningar. Alternativen inkluderar GET eller HEAD (standard).
    > [!NOTE]
    > För lägre belastning och kostnad på dina serverdelser rekommenderar Ytterdörren att du använder HEAD-begäranden om hälsosonder.

- **Intervall (sekunder)**: Definierar frekvensen av hälsoavsökningar till dina serverdelss, eller de intervall i vilka var och en av frontdörrmiljöerna skickar en avsökning.

    >[!NOTE]
    >För snabbare redundans anger du intervallet till ett lägre värde. Ju lägre värde, desto högre hälsoavsökningsvolym får dina servercentraler. Om intervallet till exempel är inställt på 30 sekunder med säga 100 pops för ytterdörren globalt, får varje serverdel cirka 200 avsökningsbegäranden per minut.

Mer information finns i [Hälsoavsökningar](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Inställningar för belastningsutjämning
Belastningsutjämningsinställningar för serverdapoolen definierar hur vi utvärderar hälsoavsökningar. De här inställningarna avgör om serveringen är felfri eller ohälsosam. De kontrollerar också hur man laddar trafik mellan olika backend-enheter i backend-poolen. Följande inställningar är tillgängliga för belastningsutjämningskonfiguration:

- **Exempelstorlek**. Identifierar hur många exempel på hälsosonder som vi behöver tänka på för hälsoutvärdering av serveringsbehov.

- **Lyckad provstorlek**. Definierar exempelstorleken som tidigare nämnts, antalet lyckade exempel som behövs för att kalla backend felfri. Anta till exempel att ett hälsoavsökningsintervall för frontdörren är 30 sekunder, samplingsstorleken 5 och en lyckad provstorlek är 3. Varje gång vi utvärderar hälsosonder för din serverad tittar vi på de senaste fem proverna under 150 sekunder (5 x 30). Minst tre framgångsrika sonder krävs för att förklara serverd som friska.

- **Latenskänslighet (ytterligare latens)**. Definierar om du vill att ytterdörren ska skicka begäran till bakåtsidor inom känslighetsområdet för svarstidsmätning eller vidarebefordra begäran till närmaste backend.

Mer information finns i [Minsta svarstidsbaserad routningsmetod](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Nästa steg

- [Skapa en profil för ytterdörren](quickstart-create-front-door.md)
- [Hur ytterdörren fungerar](front-door-routing-architecture.md)