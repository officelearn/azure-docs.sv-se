---
title: Azure ytterdörren Service - serverdelar och Serverdelspooler | Microsoft Docs
description: Den här artikeln hjälper dig att förstå vad är serverdelen och serverdelspooler för i ytterdörren konfiguration.
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
ms.openlocfilehash: 14cc87e8691c859274495a13cc0b73fa29ad22df
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726897"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Serverdelar och backend-pooler i Azure ytterdörren Service
Den här artikeln beskrivs de olika begreppen om hur du kan mappa programdistributionen med åtkomsten. Vi kommer också att förklara vad de olika framför dörren konfiguration runt om serverdelen för programmet betyder.

## <a name="backend-pool"></a>Serverdelspool
En serverdelspool i Front Door syftar på en uppsättning med likvärdiga servrar som kan ta emot samma typ av trafik för sina program. Det är alltså en logisk gruppering av dina programinstanser runtom i världen som kan ta emot samma trafik och svara med förväntat beteende. Dessa serverdelar distribueras vanligtvis över olika regioner eller i samma region. Dessutom kan dessa serverdelar kan vara i läget för aktiv-aktiv distribution eller annars kan definieras som en aktiv/passiv konfiguration.

Serverdelspool definierar också hur olika serverdelar bör alla utvärderas för deras hälsa via hälsoavsökningar och motsvarande hur belastningsutjämning mellan serverdelen ska ske.

### <a name="health-probes"></a>Hälsotillståndsavsökningar
Ytterdörren skickar regelbundna begäranden för HTTP/HTTPS-avsökning till var och en av dina konfigurerade serverdelar fastställa närhet och hälsotillståndet för varje serverdel så att läsa in balansera din förfrågningar från slutanvändare. Inställningar för avsökning av hälsotillstånd för en serverdelspool definierar hur vi avsöka hälsostatus för din serverdelar. Följande inställningar är tillgängliga för konfiguration för belastningsutjämning:

1. **Sökväg**: URL-sökväg där avsökningen-begäranden skickas till för alla serverdelar i serverdelspoolen. Exempel: om en av serverdelen är `contoso-westus.azurewebsites.net` och sökvägen är inställt på `/probe/test.aspx`, och sedan ytterdörren miljöer, förutsatt att protokollet har angetts till HTTP, skickar hälsotillstånd avsökningen begäranden till http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protokoll**: Definierar om hälsotillstånd avsökningen förfrågningar från åtkomsten till serverdelen skickas via HTTP eller HTTPS-protokollet.
3. **Intervall (sekunder)**: Det här fältet definierar frekvensen för hälsoavsökningar till serverdelen, det vill säga de intervall som var och en av miljöerna som ytterdörren skickar en avsökning. Obs! – anger om du letar efter snabbare redundans, du det här fältet till ett lägre värde. Men ju lägre värdet mer hälsosonden volym som serverdelen kommer att få. Låt oss ta ett exempel för att få en uppfattning om hur mycket avsökningen volym ytterdörren genererar på serverdelen. Låt oss säga intervallet är inställd på 30 sekunder och det finns cirka 90 ytterdörren miljöer eller POP globalt. Så var och en av serverdelen cirka får om 3-5 avsökningen begäranden per sekund.

Läs [hälsoavsökningar](front-door-health-probes.md) information.

### <a name="load-balancing-settings"></a>Inställningar för belastningsutjämning
Belastningsutjämning inläsningsinställningarna för serverdelspoolen definierar hur vi utvärdera hälsoavsökningar för serverdelen ska vara felfri eller ohälsosam och även hur vi behöver att belastningsutjämna trafiken mellan de olika serverdelen i serverdelspoolen. Följande inställningar är tillgängliga för konfiguration för belastningsutjämning:

1. **Exempel på storlek**: Den här egenskapen anger hur många exempel på hälsoavsökningar som vi behöver tänka på för utvärdering för backend-hälsotillstånd.
2. **Lyckad exempelstorlek**: Den här egenskapen definierar att av exemplet storlek enligt beskrivningen ovan, hur många exempel behöver vi Kontrollera för att lyckas anropa serverdelen som felfri. 
</br>Exempel: anta för ytterdörren du har angett hälsoavsökningen *intervall* till 30 sekunder, *exempel storlek* är inställd på '5' och *lyckade exempelstorlek* är inställd på ”3”. Och sedan vad den här konfigurationen innebär att varje gång det utvärdera hälsoavsökningar för din serverdel vi ska titta på de sista fem prover som skulle utsträckning senaste 150 sekunder (= 5 * 30 s) och om det inte finns 3 eller flera av dessa avsökningar lyckad vi deklarerar bakgrunden slutar feltillstånd. Vi antar att det har bara två lyckad avsökningar och, så vi markerar serverdelen som skadad. Nästa gång vi kör utvärderingen om vi hittar 3 i de sista fem avsökningarna kan markerar sedan vi serverdelen som felfria igen.
3. **Svarstid känslighet (ytterligare fördröjning)**: Fältet Svarstid känslighet definierar om du vill att dörren att skicka begäran till servrar som ligger inom intervallet känslighet när det gäller svarstider mätning eller vidarebefordra begäran till den närmaste serverdelen. Läs [lägsta svarstid baserat routningsmetod](front-door-routing-methods.md#latency) för ytterdörren vill veta mer.

## <a name="backend"></a>Serverdel
En serverdel motsvarar ett programs distribution instans i en region. Ytterdörren stöder både Azure samt icke-Azure-serverdelar och så regionen här bara är inte begränsad till Azure-regioner, men kan även vara ditt lokala datacenter eller en programinstans i några andra moln.

Serverdelar, refererar i samband med första dörrar till värdnamnet eller offentliga IP-Adressen för ditt program, som kan fungera klientbegäranden. Därför ska inte förväxlas med din databasnivån eller din lagringsnivå osv serverdelar men i stället bör ses som den offentliga slutpunkten för serverdelen för programmet.

När du lägger till en serverdel i en serverdelspool ytterdörren behöver du fylla i följande information:

1. **Serverdelen värdtyp**: Typ av resurs som du vill lägga till. Dörren har stöd för automatisk identifiering av serverdelar för din om från app service, cloud Services eller lagring. Om du vill att en annan resurs i Azure eller till och med en icke-Azure-serverdel, väljer du anpassad-värd. Observera - under konfigurationen av API: er inte validerar om serverdelen kan nås från ytterdörren miljöer, i stället måste du kontrollera att din serverdel kan nås av ytterdörren. 
2. **Prenumeration och serverdelen värdnamn**: Om du inte har valt ange anpassade värden för backend-värd, måste du definiera omfattningen av och välj din serverdel genom att välja lämplig prenumeration och motsvarande backend-värdnamnet från användargränssnittet.
3. **Serverdelen värdhuvud**: Det värdhuvudsvärde som skickas till serverdelen för varje begäran. Läs [serverdel värdhuvud](#hostheader) mer information.
4. **Prioritet**: Du kan tilldela prioriteringar till serverdelen olika när du vill använda en primär service-serverdelen för all trafik och ge säkerhetskopiering om primärt eller serverdelar för säkerhetskopiering är inte tillgänglig. Läs mer om [prioritet](front-door-routing-methods.md#priority).
5. **Vikt**: Du kan tilldela vikter till serverdelen olika när du vill distribuera trafik över en uppsättning serverdelar, jämnt eller enligt vikt koefficienter. Läs mer om [vikterna](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Värdadress för serverdel

Begäranden som vidarebefordras av åtkomsten till en serverdel har ett fält för värd rubrik som serverdelen använder för att hämta resurs. Värdet för det här fältet normalt kommer från serverdelen URI och har värd och port. Till exempel en begäran `www.contoso.com` har värdhuvudet `www.contoso.com`. Om du konfigurerar serverdelen med hjälp av Azure portal är det standardvärde som fylls i det här fältet namnet på serverdelen. Exempel: om din serverdel är `contoso-westus.azurewebsites.net`, så i Azure-portalen kommer att vara fylls värdet för serverdelen värdhuvud `contoso-westus.azurewebsites.net`. 
</br>Men om du använder Resource Manager-mallar eller annan kontrollmekanism och du inte anger det här fältet uttryckligen skickar ytterdörren sedan inkommande värdnamnet som värde för värdhuvud. Exempel: om en begäran har gjorts `www.contoso.com`, och serverdelen är `contoso-westus.azurewebsites.net` med backend-värd huvud anger som tom sedan ytterdörren anger du värdhuvudet som `www.contoso.com`.

De flesta serverdelar (t.ex Web Apps, Blob Storage och Cloud Services) kräver att värdhuvudet matchar domänen för serverdelen. Frontend-värden som dirigerar till din serverdel har dock ett annat värdnamn som www.contoso.azurefd.net. Om du ställer in serverdelen kräver värdhuvudet matchar värdnamnet för serverdelen, bör du kontrollera att ”serverdel host header” också har värdnamnet för serverdelen.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurera backend-värdhuvud för serverdelen
Fältet ”Backend-Värdrubriken” kan konfigureras för en serverdel i avsnittet backend-poolen.

1. Öppna ytterdörren resursen och klicka på backend-pool med serverdelen som ska konfigureras.

2. Lägg till en serverdel om du inte har lagt till eller redigera en befintlig. Ange ett anpassat värde fältet ”serverdel host header” du kan eller lämnas tomt används vilket innebär att värdnamnet för den inkommande begäranden kommer att användas som värdhuvudsvärdet.



## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).