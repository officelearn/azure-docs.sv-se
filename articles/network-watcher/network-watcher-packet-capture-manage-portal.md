---
title: Hantera infångade paket med Azure Network Watcher - Azure-portalen | Microsoft Docs
description: Lär dig hur du hanterar paket avbildningsfunktionen i Network Watcher med Azure portal.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 22bdd50f129a48ade97db323f904f7e652a00d39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725864"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Hantera infångade paket med Azure Network Watcher med hjälp av portalen

Network Watcher-infångade kan du skapa avbildning sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för avbildningssessionen att se till att du fångar upp trafiken som du vill. Det hjälper dig för att diagnostisera nätverk avvikelser både reaktivt och proaktivt infångade paket. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång till debug klient-/ serverkommunikation och mycket mer. Att kunna via fjärranslutning utlösa paket samlar in, underlättar arbetet med att köra ett infångat manuellt på en önskad virtuell dator, vilket sparar värdefull tid.

I den här artikeln får du lära dig att starta, stoppa, hämta och ta bort ett infångat paket. 

## <a name="before-you-begin"></a>Innan du börjar

Paketfångsten kräver följande anslutningar:
* Utgående anslutning till ett lagringskonto via port 443.
* Inkommande och utgående anslutning till 169.254.169.254
* Inkommande och utgående anslutning till 168.63.129.16

Om en nätverkssäkerhetsgrupp är kopplad till nätverksgränssnitt eller undernät som nätverksgränssnittet finns i, kan du säkerställa att regler finns som gör att portarna som tidigare. 

## <a name="start-a-packet-capture"></a>Starta ett infångat paket

1. I din webbläsare, navigerar du till den [Azure-portalen](https://portal.azure.com) och välj **alla tjänster**, och välj sedan **Network Watcher** i den **nätverksavsnittet**.
2. Välj **paketfångsten** under **diagnostiska verktyg**. Alla befintliga infångade visas, oavsett deras status.
3. Välj **Lägg till** att skapa ett infångat paket. Du kan välja värden för följande egenskaper:
   - **Prenumeration**: Den prenumeration som den virtuella datorn som du vill skapa paketet avbilda för finns i.
   - **Resursgrupp**: Resursgruppen för den virtuella datorn.
   - **Virtuell måldator**: Den virtuella dator som du vill skapa paketfångsten för.
   - **Namn på paketfångst**: Ett namn för paketfångsten.
   - **Storage-konto eller filen**: Välj **lagringskonto**, **filen**, eller båda. Om du väljer **filen**, insamlingen skrivs till en sökväg i den virtuella datorn.
   - **Lokal filsökväg**: Den lokala sökvägen på den virtuella datorn där paketfångsten sparas (endast giltigt när *filen* har valts). Sökvägen måste vara en giltig sökväg. Om du använder en Linux-dator, sökvägen måste börja med */var/samlar in*.
   - **Storage-konton**: Välj ett befintligt lagringskonto om du har valt *lagringskonto*. Det här alternativet är bara tillgängligt om du har valt **Storage**.
   
     > [!NOTE]
     > Premium storage-konton stöds för närvarande inte för lagring av paket samlar in.

   - **Maximalt antal byte per paket**: Antal byte från varje paket som har hämtats. Om inget anges används som alla byte avbildas.
   - **Maximalt antal byte per session**: Det totala antalet byte som har hämtats. När värdet har nåtts packet capture stoppas.
   - **Tidsgräns (sekunder)**: Tidsgränsen innan paketfångsten har stoppats. Standardvärdet är 18 000 sekunder.
   - Filtrering (valfritt). Välj **+ Lägg till filter**
     - **Protokoll**: Protokollet som används för att filtrera för paketfångsten. Tillgängliga värden är TCP, UDP och alla.
     - **Lokal IP-adress**: Filtrerar infångade för paket där den lokala IP-adressen matchar det här värdet.
     - **Lokal port**: Filtrerar infångade för paket där den lokala porten matchar det här värdet.
     - **IP-Fjärradress**: Filtrerar infångade för paket där IP-Fjärradress matchar det här värdet.
     - **Fjärrport**: Filtrerar infångade för paket där fjärrporten matchar det här värdet.
    
     > [!NOTE]
     > Värden för port och IP-adress kan vara ett enda värde, intervallet för värden eller ett intervall, till exempel 80 – 1024 för port. Du kan definiera så många filter som du behöver.

4. Välj **OK**.

När tidsgränsen på paketfångsten har upphört att gälla, paketfångsten har stoppats och kan granskas. Du kan också manuellt stoppa en paket-avbildningssessionen.

> [!NOTE]
> Portalen automatiskt:
>  * Skapar en nätverksbevakare i samma region som den region som den virtuella datorn som du har valt finns i, om regionen inte redan har en network watcher.
>  * Lägger till den *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) eller [Windows](../virtual-machines/windows/extensions-nwa.md) tillägg för virtuell dator till den virtuella datorn, om det inte redan är installerat.

## <a name="delete-a-packet-capture"></a>Ta bort ett infångat paket

1. Välj i vyn packet capture **...**  på höger sida av paketet samla in, eller högerklicka på en befintlig paketinsamling och välj **ta bort**.
2. Du uppmanas att bekräfta att du vill ta bort paketfångsten. Välj **Ja**.

> [!NOTE]
> Avbilda-filen i storage-konto eller på den virtuella datorn tas inte bort om du tar bort ett infångat paket.

## <a name="stop-a-packet-capture"></a>Stoppa ett infångat paket

Välj i vyn packet capture **...**  på höger sida av paketet samla in, eller högerklicka på en befintlig paketinsamling och välj **stoppa**.

## <a name="download-a-packet-capture"></a>Ladda ned ett infångat paket

När packet capture sessionen är klar, har avbilda filen överförts till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketfångsten har definierats under skapandet av paketfångsten. Ett praktiskt verktyg för att komma åt avbilda filer som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som du kan [hämta](https://storageexplorer.com/).

Om ett lagringskonto anges sparas packet capture filer till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Om du har valt **filen** när du har skapat avbildningen kan du visa eller ladda ned filen från den sökväg som du har konfigurerat på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Läs hur du automatiserar infångade paket med VM-aviseringar i [skapar en avisering utlösta infångade](network-watcher-alert-triggered-packet-capture.md).
- För att avgöra om viss trafik tillåts eller från en virtuell dator, se [diagnostisera problem med virtuella nätverk trafik filter](diagnose-vm-network-traffic-filtering-problem.md).
