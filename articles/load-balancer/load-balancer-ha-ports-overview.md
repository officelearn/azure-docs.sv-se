---
title: "Översikt över portar för hög tillgänglighet i Azure | Microsoft Docs"
description: "Lär dig mer om hög tillgänglighet portar för belastningsutjämning på en intern belastningsutjämnare"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>Översikt över portar för hög tillgänglighet (förhandsgranskning)

Azure läsa in belastningsutjämning Standard introducerar en ny möjligheten att läsa in saldo TCP och UDP-flöden på alla portar samtidigt när du använder en intern belastningsutjämnare. 

>[!NOTE]
> Hög tillgänglighet portar funktionen är tillgänglig med Load Balancer Standard och för närvarande under förhandsgranskning. Som förhandsversion kanske funktionen inte har samma tillgänglighet och pålitlighet som funktioner som är allmänt tillgängliga. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det är nödvändigt att registrera dig för belastningsutjämnaren belastningen Standard förhandsversionen ska använda HA portar med Load Balancer Standard resurser. Följ instruktionerna för att registrera förutom belastningsutjämnaren [Standard Preview](https://aka.ms/lbpreview#preview-sign-up) samt.

En regel för hög tillgänglighet portar är en variant av en regel som konfigurerats på ett internt läsa in belastningsutjämning Standard för belastningsutjämning.  Scenarier förenklas genom att tillhandahålla en regel för att belastningsutjämna alla TCP och UDP-flöden som inkommer på alla portar för ett internt läsa in belastningsutjämning Standard klientdel LB. Läsa in belastningsutjämning beslut görs per flöde baserat på fem-tuppel källans IP-adress, källport, mål-IP-adress, målport och-protokollet.

Hög tillgänglighet portar kan kritiska situationer som hög tillgänglighet och skala för nätverket virtuella installationer (NVA) i virtuella nätverk samt andra scenarier där ett stort antal portar måste vara belastningsutjämnade. 

Hög tillgänglighet portar har konfigurerats genom att ange frontend och backend-portar till **0** och protokollet till **alla**.  Den interna belastningsutjämnaren resursen balanserar nu alla TCP och UDP flöden oavsett portnummer.

## <a name="why-use-ha-ports"></a>Varför använda portar för hög tillgänglighet

### <a name="nva"></a>Virtuella nätverksenheter

Du kan använda virtuella nätverksinstallationer (NVA) för att skydda din arbetsbelastning i Azure från flera typer av säkerhetshot. När NVA används i dessa scenarier, måste de vara tillförlitliga, hög tillgänglighet och skalbar för begäran.

Du kan uppnå dessa mål i ditt scenario genom att lägga till NVA instanser i serverdelspoolen av Azure interna belastningsutjämnare och konfigurera en regel för hög tillgänglighet portar belastningsutjämnaren.

Hög tillgänglighet finns portar många fördelar för NVA HA scenarier:
- snabb växla över till Felfri instanser med per instans hälsoavsökningar
- högre prestanda med skalbara till n-aktiva instanser
- n aktiv och aktivt-passivt scenarier
- eliminera behovet av komplexa lösningar som Zookeeper-noder för att övervaka installationer

I följande exempel visas en distribution med nav och ekrar virtuellt nätverk med ekrar kraft tunneling sin trafik till det virtuella nätverket hubb och via en NVA innan de lämnar det betrodda utrymmet. NVAs finns bakom en intern belastningen belastningsutjämnaren Standard med hög tillgänglighet portkonfiguration.  All trafik kan bearbetas och därefter vidarebefordra. 

![hög tillgänglighet portarna exempel](./media/load-balancer-ha-ports-overview/nvaha.png)

Bild 1 - NAV och eker-nätverk med NVAs som distribuerats i läget för hög tillgänglighet

Om du använder virtuella nätverksinstallationer bekräfta med respektive providern hur du bäst använder HA portar och vilka scenarier som stöds.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda hög tillgänglighet portar för Programscenarier som kräver belastningen balanicng av stora mängder portar. Dessa scenarier kan förenklas med hjälp av en intern [läsa in belastningsutjämning Standard](https://aka.ms/lbpreview) med hög tillgänglighet portar där en enda regel för belastningsutjämning ersätter flera enskilda belastningsutjämningsregler, ett för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Hög tillgänglighet portar finns i den [samma regioner som Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Förhandsgranska registrering

Registrera prenumerationen för att få åtkomst med hjälp av Azure CLI 2.0 eller PowerShell för att delta i förhandsversionen av funktionen hög tillgänglighet portar i Load Balancer Standard.  Följ dessa tre steg:

>[!NOTE]
>Om du vill använda den här funktionen måste du också registrera för belastningsutjämnaren [Standard Preview](https://aka.ms/lbpreview#preview-sign-up) förutom HA portar. Registrering av hög tillgänglighet portar eller läsa in belastningsutjämning Standard förhandsversioner kan ta upp till en timme.

### <a name="sign-up-using-azure-cli-20"></a>Logga med Azure CLI 2.0

1. Registrera funktionen med providern
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. Den föregående åtgärden kan ta upp till 10 minuter att slutföra.  Du kan kontrollera status för åtgärden med följande kommando:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Gå vidare till steg 3 när funktionen registreringstillstånd returnerar 'Registrerade' enligt nedan:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Slutför registreringen förhandsgranskningen genom att registrera prenumerationen med resursprovidern:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>Logga med hjälp av PowerShell

1. Registrera funktionen med providern
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. Den föregående åtgärden kan ta upp till 10 minuter att slutföra.  Du kan kontrollera status för åtgärden med följande kommando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Gå vidare till steg 3 när funktionen registreringstillstånd returnerar 'Registrerade' enligt nedan:
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Slutför registreringen förhandsgranskningen genom att registrera prenumerationen med resursprovidern:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Begränsningar

Följande är de konfigurationer som stöds eller undantag för hög tillgänglighet portar:

- En enda klientdelens IP-konfiguration kan ha en enda regel DSR belastningsutjämnare med hög tillgänglighet portar eller den kan ha en enskild icke-DSR belastningsutjämningsregel med hög tillgänglighet portar. Det går inte att ha både.
- Konfigurationen för en IP-nätverket gränssnitt kan endast ha en icke-DSR belastningsutjämningsregeln med hög tillgänglighet portar. Inga andra regler kan konfigureras för den här ipconfig.
- Konfigurationen för en IP-gränssnittet i nätverket kan ha en eller fler DSR belastningen belastningsutjämnaren regler med hög tillgänglighet portar, under förutsättning att alla sina respektive klientdelens IP-konfigurationer är unika.
- Om alla belastningsutjämning reglerna är hög tillgänglighet portar (DSR), eller alla regler har, icke - hög tillgänglighet portar (DSR & icke DSR) samtidigt två (eller fler) belastningsutjämnare regler som pekar på samma backend-pool kan. Två sådana belastningsutjämningsregler finnas inte samtidigt om det är en kombination av regler för hög tillgänglighet och icke - hög tillgänglighet.
- Hög tillgänglighet portar är inte tillgänglig för IPv6.
- Flödet symmetri NVA scenarier stöds med enda nätverkskort. Se beskrivning och diagram för [virtuella nätverksinstallationer](#nva). 



## <a name="next-steps"></a>Nästa steg

- [Konfigurera hög tillgänglighet portar på ett internt belastningen belastningsutjämnaren Standard](load-balancer-configure-ha-ports.md)
- [Lär dig mer om belastningen belastningsutjämnaren Standard preview](https://aka.ms/lbpreview)

