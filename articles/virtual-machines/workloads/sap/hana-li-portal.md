---
title: Azure HANA stora instanser styra via Azure-portalen | Microsoft Docs
description: Hur sätt du kan identifiera och interagera med Azure HANA stora instanser via portalen
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b186aa2692033a774d1d8f294315fcc0f5e874d5
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58763257"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure HANA stora instanser kontrollen med Azure-portalen
Det här dokumentet beskriver hur sätt [HANA stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) presenteras i [Azure-portalen](https://portal.azure.com) och vilka aktiviteter kan utföras via Azure-portalen med enheter för stora HANA-instansen som är distribuerade åt dig. Synlighet för stora HANA-instanser i Azure-portalen är tillgängligt via en Azure resource provider för HANA stora instanser, som för närvarande är i offentlig förhandsversion

## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Visning av enheter för stora HANA-instansen i Azure portal
När du skickar en begäran om distribution av stora HANA-instansen uppmanas du att ange den prenumeration som du ansluter till HANA stora instanser samt. Det rekommenderas att använda samma prenumeration som du använder för att distribuera SAP-programnivån som fungerar mot stora HANA-instansen-enheter.
Som din första HANA stora instanser komma distribueras, en ny [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) har skapats i Azure-prenumeration du skickat in i distributionsbegäran för dina stora HANA-instanser.  Den nya resursgruppen visas en lista över alla dina stora HANA-instansen enheter du har distribuerat i den specifika prenumerationen.

För att hitta den nya Azure-resursgruppen kan visa du resursgruppen i din prenumeration genom att gå igenom det vänstra navigeringsfönstret i Azure Portal

![Navigeringsfönstret i Azure-portalen](./media/hana-li-portal/portal-resource-group.png)

I listan över resursgrupper du komma visas, måste du kanske filtrerar i prenumerationen som du använde för att ha HANA stora instanser som distribuerats

![Filtrera resursgrupper i Azure portal](./media/hana-li-portal/portal-filtering-subscription.png)

Efter filtrering att korrekt prenumeration kan du fortfarande ha en lång lista över resursgrupper. Leta efter en med en efter korrigering av **- Txxx** där ”xxx” finns tre siffror, som **-T050**. 

När du har hittat resursgruppens namn, en lista över information om den. I listan som du har fått gick ut:

![HLI lista i Azure-portalen](./media/hana-li-portal/portal-hli-units-list.png)

Alla enheter som visas är som representerar en enda HANA stora instanser-enhet som har distribuerats i din prenumeration. I det här fallet titta på åtta olika stora HANA-instansen enheter, som har distribuerats i din prenumeration.


## <a name="look-at-attributes-of-single-hli-unit"></a>Titta på attribut för HLI enhet
I listan med enheter för stora HANA-instansen du klicka på en enhet och få till information om den enda stora HANA-instansen-enheten. 

På skärmen får du en presentation av den enhet som ser ut som:

![Översikt över en HLI-enhet](./media/hana-li-portal/portal-show-overview.png)

Titta på olika attribut visas, dessa attribut knappt ser annorlunda ut än Azure VM-attribut. På rubriken vänstra sida visar resursgruppen, Azure-region, prenumerationsnamn och ID: T samt vissa taggar som du har lagt till. Som standard har ingen tagg som tilldelats i stora HANA-instansen-enheter. På höger sida av rubriken visas namnet på enheten som har tilldelats när distributionen har utförts. Operativsystemet visas samt IP-adressen. Som med virtuella datorer skriver du stora HANA-instans-enheten med antalet visas trådar och minne också. Mer information om de olika enheterna i stora HANA-instansen visas här:

- [Tillgängliga SKU:er för HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Storage-arkitektur för SAP HANA (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Ett ytterligare fält i den högra kolumnen av rubriken informerar om energinivån för stora HANA-instans-enheten.

> [!NOTE]
> Energinivån beskriver den maskinvara enheten har startats eller inaktivera. Det ger inte information om operativsystem som upp och köra. När du startar om en enhet för stora HANA-instansen, uppstår en liten tid där ändras tillståndet för enheten till **startar** att flytta till tillståndet för **startad**. Är i tillståndet för **startad** innebär att Operativsystemet startas eller att Operativsystemet har startats helt. Därför efter en omstart av enheten som inte förväntas omedelbart logga in på enheten när tillståndet växlar till **startad**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Kontrollera aktiviteter för en enda HANA stora instanser-enhet 
Du kan kontrollera aktiviteter för den specifika enheten utöver ger en översikt över stora HANA-instansen-enheter. En aktivitetslogg gick ut:

![Navigeringsfönstret i Azure-portalen](./media/hana-li-portal/portal-activity-list.png)

En av de viktigaste aktiviteterna registreras är omstarter av en enhet. Innehåller information som visas status för aktivitet, tidsstämpeln som aktiviteten har utlösts av prenumerations-ID som aktiviteten har utlösts och Azure-användare som utlöste aktiviteten. 

En annan aktivitet som komma registreras är ändringar till enheten i Azure meta-data. Förutom den omstart har initierats, ser du aktiviteten hos **skriva HANAInstances**. Den här typen av aktivitet utför inga ändringar på enheten stora HANA-instansen själv, men dokumentera ändringar av metadata för enheten i Azure. I de fall, vi har lagt till och ta bort en tagg (se nästa avsnitt).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Lägga till och ta bort en Azure-tagg till en enhet för stora HANA-instansen
En annan möjlighet som du har är att lägga till en [taggen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) till en enhet för stora HANA-instansen. Hur taggar komma tilldelas skiljer sig inte från att tilldela taggar till virtuella datorer. Med virtuella datorer taggar finns i Azure metadata och, för stora HANA-instanser som har samma begränsningar som taggar för virtuella datorer.

Ta bort taggar fungerar på samma sätt som med virtuella datorer. Båda verksamheterna, använder och tar bort en tagg visas i aktivitetsloggen för den specifika enheten stora HANA-instansen.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Kontrollera egenskaperna för en enhet för stora HANA-instansen
Avsnittet **egenskaper** innehåller viktig information som du får när instanser ska lämnas till dig. Det är ett avsnitt där du får den information som du kan kräva support fall eller som du behöver när du konfigurerar ögonblicksbild lagringskonfiguration. Det här avsnittet är därför en samling data om din instans, anslutning av instans till Azure och storage-serverdelen. Längst upp i avsnittet ser ut som:


![övre delen av HLI egenskaper i Azure-portalen](./media/hana-li-portal/portal-properties-top.png)

De första några dataobjekt du såg i översiktsskärmen redan. Men en viktig del av data är ExpressRoute Circuit ID som du får när du har fått de första distribuerade enheterna överlämnas. I vissa fall stöd kan hämta du tillfrågad om dessa data. En post för viktiga data visas längst ned på skärmbilden. Informationen som visas är den IP-adressen för huvudet för NFS-lagring som isolerar lagringen din **klient** i stacken stora HANA-instansen. Den här IP-adress krävs även när du redigerar den [konfigurationsfilen för lagring ögonblicksbild säkerhetskopior](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

När du rullar ned i egenskapsrutan, hämta ytterligare data som en unik resurs-ID för din enhet för stora HANA-instansen eller prenumerations-ID som har tilldelats till distributionen.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Starta om en enhet för stora HANA-instansen via Azure-portalen
Initierar en omstart av Linux-operativsystem har olika situationer där Operativsystemet inte kunde slutförs startas om. För att tvinga fram en omstart, som du behöver öppna en tjänstbegäran har Microsoft operations utföra power omstart av enheten stora HANA-instansen. Funktionerna i power måste starta om en enhet för stora HANA-instansen är nu integrerade i Azure-portalen. När du använder översikt över en del av den stora HANA-instansen-enheten kan visas knappen för omstart av ovanpå dataavsnittet

![Starta om steg #1 i Azure-portalen](./media/hana-li-portal/portal-restart-first-step.png)

När du att trycka på knappen Starta om, tillfrågas du om du verkligen vill starta om enheten. Enheten kommer att startas om eftersom du bekräftar genom att trycka på knappen ”Yes”.

> [!NOTE]
> Omstarten får en liten tid där ändras tillståndet för enheten till **startar** att flytta till tillståndet för **startad**. Är i tillståndet för **startad** innebär att Operativsystemet startas eller att Operativsystemet har startats helt. Därför efter en omstart av enheten som inte förväntas omedelbart logga in på enheten när tillståndet växlar till **startad**.


## <a name="open-a-support-request-for-hana-large-instances"></a>Öppna en supportbegäran för HANA stora instanser
Du kan skapa supportärenden specifikt för en HANA stora instans enhet samt från Azure portal visningen av stora HANA-instansen enheter. Du följer länken **ny supportbegäran** 

![Starta tjänsten begäran steg #1 i Azure-portalen](./media/hana-li-portal/portal-initiate-support-request.png)

För att få tjänsten av SAP HANA stora instanser visas på nästa skärm kanske du måste välja ”alla tjänster” enligt nedan

![Välj alla tjänster i Azure-portalen](./media/hana-li-portal/portal-create-service-request.png)

I listan över tjänster, hittar du tjänsten **stora SAP HANA-instansen**. När du väljer den tjänsten, kan du välja specifika problemtyper enligt:


![Välj problem klass i Azure-portalen](./media/hana-li-portal/portal-select-problem-class.png)

Under var och en av typerna av olika problem erbjuds ett urval av problemet undertyper som du måste välja för att beskriva problemet ytterligare. När du har valt undertypen namnge du nu ämne. När du är klar med urvalsprocessen kan flytta du till nästa steg i att skapa. I den **lösningar** avsnittet, som pekar till dokumentation runt HANA stora instanser, som kan ge en pekare till en lösning till problemet. Om du inte hittar en lösning för ditt problem i dokumentationen som föreslås, gå till nästa steg. I nästa steg ska kommer du att bli ombedd om problemet är med virtuella datorer eller med enheter för stora HANA-instansen. Detta hjälper till att dirigera supportbegäran till rätt specialister. 

![Information om supportärende i Azure-portalen](./media/hana-li-portal/portal-support-request-details.png)

När du har besvarat frågor och tillhandahålls ytterligare information, går du nästa steg för att kunna granska supportbegäran och överföringswebbadressen den.

## <a name="next-steps"></a>Nästa steg

- [Så här övervakar du SAP HANA (stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Övervaka och felsöka från HANA-sida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

