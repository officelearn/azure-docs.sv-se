---
title: "Azure Event Hubs geo-katastrofåterställning | Microsoft Docs"
description: "Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Event Hubs"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Azure Event Hubs geo-återställning (förhandsgranskning)

När regionala Datacenter eventuellt driftstopp, är det viktigt för databearbetning fortsätta att arbeta i en annan region eller datacenter. Därför *geo-återställning* och *georeplikering* är viktiga funktioner för företag. Händelsehubbar i Azure stöder både geo-återställning och geo-replikering på namnområdesnivån. 

Geo-disaster recovery-funktionen av Händelsehubbar i Azure är en lösning för katastrofåterställning. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofåterställning scenarier och inte tillfälligt eller tillfälliga avbrott.

En detaljerad beskrivning av katastrofåterställning i Microsoft Azure finns [i den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminologi

**Länkning av**: namnområdet primära kallas *active* och tar emot meddelanden. Namnområdet för växling vid fel är *passiva* och tar inte emot meddelanden. Metadata mellan de båda är synkroniserade, så att både sömlöst kan godkänna meddelanden utan några program kodändringar. Upprätta katastrofberedskapskonfigurationen mellan den aktiva och passiva region kallas *länkning* regionerna.

**Alias**: ett namn för någon haveriberedskapskonfiguration som du skapat. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använda anslutningssträng med detta alias för att ansluta till ett namnområde.

**Metadata**: refererar till event hub-namn, konsumentgrupper, partitioner, enheter, enheter och egenskaper som är associerade med namnområde.

## <a name="enable-geo-disaster-recovery"></a>Aktivera geo-katastrofåterställning

Du aktiverar Händelsehubbar geo-återställning i steg 3: 

1. Skapa ett geo-par, som skapar en alias-anslutningssträng och erbjuder live metadata replikering. 
2. Uppdatera de befintliga klienten anslutningssträngarna till det alias som skapades i steg 1.
3. Påbörja redundans: geo-kopplingen har brutits och aliaset som pekar på sekundär namnområdet som det nya primära namnområdet.

Följande bild visar arbetsflödet:

![GEO-länkning flöde][1] 

### <a name="step-1-create-a-geo-pairing"></a>Steg 1: skapa en geo-länkning

För att skapa ett par mellan två regioner, behöver du ett namnområde för primära och sekundära namnområdet. Sedan kan du skapa ett alias för att skapa geo-par. När namnområden som har parats ihop med ett alias, replikeras metadata med jämna mellanrum i båda namnområden. 

Följande kod visar hur du gör detta:

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>Steg 2: Uppdatera befintlig klient-anslutningssträngar

När geo-kopplingen är klar, måste anslutningssträngar som pekar på den primära namnområden uppdateras för att peka mot anslutningssträngen alias. Hämta anslutningssträngar som visas i följande exempel:

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>Steg 3: påbörja en växling

Om en olycka inträffar, eller om du vill påbörja en växling till det sekundära namnområdet, metadata och data börjar flöda till sekundär namnområde. Eftersom program som använder alias anslutningssträngar, krävs ingen ytterligare åtgärd, när de startar automatiskt läsa från och skriva till händelsehubbar i sekundära namnområdet. 

Följande kod visar hur du utlöser växling vid fel:

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

När redundansväxlingen är klar och du behöver data som finns i namnområdet primära, om du vill extrahera data måste du använda en explicit anslutningssträngen till event hubs i primära namnområdet.

### <a name="other-operations-optional"></a>Andra åtgärder (valfritt)

Du kan också dela geo-länkning eller ta bort ett alias som visas i följande kod. Observera att om du vill ta bort en alias-anslutningssträng måste du först bryta geo-paring:

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>Överväganden för public preview

Observera följande viktiga aspekter för den här versionen:

1. Geo-katastrofåterställning är endast tillgänglig i norra centrala USA och södra centrala USA regioner. 
2. Funktionen stöds bara för nyskapade namnområden.
3. Endast metadata replikering har aktiverats i förhandsversionen. Faktiska data replikeras inte.
4. Med den här förhandsversionen finns utan kostnader för att aktivera funktionen. Dock kommer både primär och sekundär namnområden avgifter för enheterna som reserverat dataflöde.

## <a name="next-steps"></a>Nästa steg

* Den [på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) går igenom ett enkelt arbetsflöde som skapar ett geo-par och initierar en växling vid fel för en katastrofåterställning.
* Den [REST API-referensen](/rest/api/eventhub/disasterrecoveryconfigs) beskriver API: er för att utföra Geo-katastrofberedskapskonfigurationen.

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

