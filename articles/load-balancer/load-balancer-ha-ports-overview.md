---
title: "Översikt över hög tillgänglighet portar i Azure | Microsoft Docs"
description: "Läs mer om hög tillgänglighet portar för belastningsutjämning på en intern belastningsutjämnare."
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
ms.openlocfilehash: 7a77e6ecbf59944c62aa4ae014bf5b8a5a7f7f1f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="high-availability-ports-overview"></a>Översikt över portar för hög tillgänglighet

Azure läsa in belastningsutjämning Standard hjälper dig att läsa in saldo TCP och UDP-flöden på alla portar samtidigt, när du använder en intern belastningsutjämnare. 

>[!NOTE]
> Funktionen för hög tillgänglighet (HA)-portar finns i Load Balancer Standard och är för närvarande under förhandsgranskning. Under förhandsgranskningen gör kanske funktionen inte har samma nivå av tillgänglighet och tillförlitlighet som funktioner som finns i den allmänna tillgänglighet-versionen. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Registrera dig för förhandsversionen av belastningen belastningsutjämnaren Standard att använda portar för hög tillgänglighet med Load Balancer Standard resurser. Följ instruktionerna för att registrera dig för att belastningsutjämnaren [Standard preview](https://aka.ms/lbpreview#preview-sign-up) samt.

En regel för hög tillgänglighet portar är en variant av en regel som konfigurerats på ett internt läsa in belastningsutjämning Standard för belastningsutjämning. Du kan förenkla din användning av belastningsutjämnare genom att tillhandahålla en enkel regel för att belastningsutjämna alla TCP och UDP-flöden som inkommer på alla portar för ett internt läsa in belastningsutjämning Standard. Belastningsutjämning beslut görs per flöde. Detta baseras på följande fem-tuppel-anslutning: källans IP-adress, källport, mål-IP-adress, målport och protokoll.

Funktionen för hög tillgänglighet portar hjälper dig med viktiga scenarier, till exempel hög tillgänglighet och skala för det virtuella nätverket-installationer (NVA) i virtuella nätverk. Det kan också när ett stort antal portar måste vara belastningsutjämnad. 

Hög tillgänglighet portar funktionen konfigureras när du ställer in frontend och backend-portar **0**, och protokoll som **alla**. Den interna belastningsutjämnaren resursen balanserar sedan alla TCP och UDP flöden, oavsett portnummer.

## <a name="why-use-ha-ports"></a>Varför använda portar för hög tillgänglighet?

### <a name="nva"></a>Virtuella nätverksenheter

Du kan använda NVAs för att skydda din arbetsbelastning i Azure från flera typer av säkerhetshot. När NVAs används i dessa scenarier kan de vara tillförlitliga och hög tillgänglighet och måste skala ut för begäran.

Du kan åstadkomma detta genom att lägga till NVA instanser till backend-poolen för Azure interna belastningsutjämnare och konfigurera en regel för hög tillgänglighet portar belastningsutjämnaren.

Hög tillgänglighet finns portar många fördelar för NVA HA scenarier:
- Snabb växling till Felfri instanser med per instans hälsoavsökningar
- Högre prestanda med skalbara till  *n* -aktiva instanser
- *N*-aktiva och aktivt-passivt scenarier
- Eliminera behovet av komplexa lösningar som Apache ZooKeeper-noder för att övervaka installationer

I följande diagram visas en distribution med nav och ekrar virtuellt nätverk. Ekrar force-tunneln sin trafik till det virtuella nätverket hubb och via en NVA innan de lämnar det betrodda utrymmet. NVAs finns bakom en intern belastning belastningsutjämnaren Standard med en konfiguration för hög tillgänglighet portar. All trafik kan bearbetas och därefter vidarebefordras.

![Diagram över NAV och eker-nätverk med NVAs som distribuerats i läget för hög tillgänglighet](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder NVAs bekräfta med respektive providern hur du bäst använder portar för hög tillgänglighet och vilka scenarier som stöds.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda portar för hög tillgänglighet för program som kräver belastningsutjämning av stora mängder portar. Du kan förenkla dessa scenarier genom att använda en intern [Load Balancer Standard](https://aka.ms/lbpreview) med hög tillgänglighet portar. En enda regel för belastningsutjämning ersätter flera enskilda belastningsutjämningsregler, ett för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Funktionen för hög tillgänglighet portar finns i den [samma regioner som Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Förhandsgranska registrering

Om du vill delta i förhandsversionen av funktionen hög tillgänglighet portar i Load Balancer Standard registrera prenumerationen för att få åtkomst. Du kan använda Azure CLI 2.0 eller PowerShell.

>[!NOTE]
>Om du vill använda den här funktionen måste du också registrera dig för belastningsutjämnaren [Standard preview](https://aka.ms/lbpreview#preview-sign-up), förutom funktionen portar för hög tillgänglighet. Registrering kan ta upp till en timme.

### <a name="sign-up-by-using-azure-cli-20"></a>Logga med hjälp av Azure CLI 2.0

1. Registrera funktionen med providern:
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. Den föregående åtgärden kan ta upp till 10 minuter att slutföra. Du kan kontrollera status för åtgärden med följande kommando:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Åtgärden har lyckats när funktionen registreringstillstånd returnerar **registrerade**, som visas här:
   
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
    
### <a name="sign-up-by-using-powershell"></a>Logga med hjälp av PowerShell

1. Registrera funktionen med providern:
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. Den föregående åtgärden kan ta upp till 10 minuter att slutföra. Du kan kontrollera status för åtgärden med följande kommando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Åtgärden har lyckats när funktionen registreringstillstånd returnerar **registrerade**, som visas här:
   
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

Följande är de konfigurationer som stöds eller undantag för funktionen portar för hög tillgänglighet:

- En enda frontend IP-konfiguration kan ha en enda DSR belastningsutjämningsregel med hög tillgänglighet portar eller den kan ha en enskild icke-DSR belastningsutjämningsregel med hög tillgänglighet portar. Det går inte att ha både.
- En enda IP-konfiguration nätverksgränssnitt kan bara ha en icke-DSR belastningsutjämningsregeln med hög tillgänglighet portar. Du kan konfigurera andra regler för den här ipconfig.
- En enda IP-konfiguration nätverksgränssnitt kan ha en eller flera DSR belastningsutjämningsreglerna med hög tillgänglighet portar, förutsatt att alla sina respektive frontend IP-konfigurationer är unika.
- Om alla regler för belastningsutjämning har hög tillgänglighet portar (DSR) kan två (eller fler) belastningsutjämnare regler som pekar på samma backend-pool finnas tillsammans. Detsamma gäller om alla regler är icke-hög tillgänglighet portarna (DSR och icke-DSR). Om det finns en kombination av portar för hög tillgänglighet och icke-HA portar regler, men finnas två sådana belastningsutjämningsregler inte samtidigt.
- Hög tillgänglighet portar funktionen är inte tillgänglig för IPv6.
- Flödet symmetri NVA scenarier stöds med ett enda nätverkskort. Se beskrivningen och diagram för [nätverks-virtuella installationer](#nva). 



## <a name="next-steps"></a>Nästa steg

- [Konfigurera portar för hög tillgänglighet på en intern belastning belastningsutjämnaren Standard](load-balancer-configure-ha-ports.md)
- [Lär dig mer om belastningen belastningsutjämnaren Standard preview](https://aka.ms/lbpreview)

