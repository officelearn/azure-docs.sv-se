---
title: Azure HANA Stora instanser styr via Azure Portal | Microsoft-dokument
description: Beskriver hur du kan identifiera och interagera med Azure HANA Stora instanser via portalen
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099821"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Kontroll av Azure HANA – stora instanser med Azure Portal
Det här dokumentet beskriver hur [STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) presenteras i [Azure-portalen](https://portal.azure.com) och vilka aktiviteter som kan utföras via Azure-portalen med STORA HANA-instansenheter som distribueras åt dig. Synlighet för STORA HANA-instanser i Azure-portalen tillhandahålls via en Azure-resursprovider för stora HANA-instanser, som för närvarande är i offentlig förhandsversion

## <a name="register-hana-large-instance-resource-provider"></a>Registrera resursprovider för STORA INSTANSER I HANA
Vanligtvis är din Azure-prenumeration som du använde för HANA Large Instance-distributioner registrerad för HANA Large Instance Resource Provider. Men om du inte kan se att du har distribuerat HANA-enheter för stora instanser bör du registrera resursleverantören i din Azure-prenumeration. Det finns två sätt att registrera hana-resursleverantören för stora instanser

### <a name="register-through-cli-interface"></a>Registrera dig via CLI-gränssnittet
Du måste vara inloggad på din Azure-prenumeration, som används för HANA Large Instance-distributionen via Azure CLI-gränssnittet. Du kan (om)registrera HANA Large Instance Provider med det här kommandot:
    
    az provider register --namespace Microsoft.HanaOnAzure

Mer information finns i artikeln [Azure-resursleverantörer och -typer](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registrera dig via Azure-portalen
Du kan (om)registrera HANA Large Instance Resource Provider via Azure-portalen. Du måste lista din prenumeration i Azure Portal och dubbelklicka på prenumerationen, som användes för att distribuera dina HANA Large Instance-enheter. En du är på översiktssidan för din prenumeration, välj "Resursleverantörer" som visas nedan och skriv "HANA" i sökfönstret. 

![Registrera HLI RP via Azure Portal](./media/hana-li-portal/portal-register-hli-rp.png)

I skärmbilden som visas registrerades resursleverantören redan. Om resursleverantören ännu inte är registrerad trycker du på "registrera om" eller "registrera".

Mer information finns i artikeln [Azure-resursleverantörer och -typer](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Visning av HANA-enheter för stora instanser i Azure-portalen
När du skickar en HANA-distributionsbegäran om stor instans uppmanas du att ange den Azure-prenumeration som du ansluter till HANA-stora instanser också. Vi rekommenderar att du använder samma prenumeration som du använder för att distribuera SAP-programlagret som fungerar mot HANA-enheter för stora instanser.
När dina första STORA HANA-instanser distribueras skapas en ny [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) i Azure-prenumerationen som du skickade i distributionsbegäran för dina STORA HANA-instanser.  Den nya resursgruppen listar alla dina STORA HANA-instansenheter som du har distribuerat i den specifika prenumerationen.

För att hitta den nya Azure-resursgruppen listar du resursgruppen i din prenumeration genom att navigera genom det vänstra navigeringsfönstret i Azure-portalen

![Navigeringsfönster i Azure-portalen](./media/hana-li-portal/portal-resource-group.png)

I listan över resursgrupper visas du, du kan behöva filtrera på prenumerationen som du använde för att distribuera HANA-stora instanser

![Filtrera resursgrupper i Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

När du har filtrerat till rätt prenumeration kan du fortfarande ha en lång lista med resursgrupper. Leta efter en med en post-fix av **-Txxx** där "xxx" är tre siffror, som **-T050**. 

När du hittade resursgruppen anger du information om den. Listan du fick kan se ut som:

![HLI-lista i Azure-portalen](./media/hana-li-portal/portal-hli-units-list.png)

Alla enheter som visas representerar en enda HANA-enhet för stor instans som har distribuerats i din prenumeration. I det här fallet tittar du på åtta olika HANA-enheter för stora instanser, som har distribuerats i din prenumeration.

Om du har distribuerat flera HANA Large Instance-klienter under samma Azure-prenumeration hittar du flera Azure-resursgrupper 


## <a name="look-at-attributes-of-single-hli-unit"></a>Titta på attribut för en enda HLI-enhet
I listan över HANA Large Instance-enheter kan du klicka på en enda enhet och komma till information om den enda HANA Large Instance-enheten. 

På översiktsskärmen, efter att ha klickat på "Visa mer", får du en presentation av enheten, som ser ut som:

![Visa översikt över en HLI-enhet](./media/hana-li-portal/portal-show-overview.png)

Om man tittar på de olika attribut som visas, dessa attribut ser knappast annorlunda än Azure VM attribut. I sidhuvudet till vänster visas resursgruppen, Azure-regionen, prenumerationsnamnet och ID:et samt några taggar som du har lagt till. Som standard har HANA Large Instance-enheterna ingen tagg tilldelad. Till höger i huvudet visas namnet på enheten som tilldelats när distributionen gjordes. Operativsystemet visas liksom IP-adressen. Som med virtuella datorer visas även typen AV HANA Large-instansenheter med antalet CPU-trådar och minne. Mer information om de olika HANA Large Instance-enheterna visas här:

- [Tillgängliga SKU:er för HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA-lagringsarkitektur (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Ytterligare data på den högra nedre sidan är revideringen av HANA Large Instance stämpel. Möjliga värden:

- Revidering 3
- Revidering 4

Revision 4 är den senaste arkitekturen som släpps av STORA HANA-instanser med stora förbättringar i nätverksfördröjningen mellan virtuella Azure-datorer och HANA-instansenheter som distribueras i Revision 4-stämplar eller rader.
En annan mycket viktig information finns i det nedre högra hörnet av översikten med namnet på Azure Proximity Placement Group som skapas automatiskt för varje distribuerad HANA-enhet för stor instans. Den här proximity placement-gruppen måste refereras när du distribuerar virtuella Azure-datorer som är värdar för SAP-programlagret. Genom att använda [azure proximity placement group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) som är associerad med HANA Large Instance-enheten kontrollerar du att virtuella Azure-datorer distribueras i närheten av HANA Large Instance-enheten. Hur närhetsplaceringsgrupper kan användas för att hitta SAP-programlagret i samma Azure-datacenter som Revision 4-värdbaserade HANA-enheter för stor instans beskrivs i [Azure Proximity Placement Groups för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).

Ytterligare ett fält i den högra kolumnen i sidhuvudet informerar om strömtillståndet för HANA Large-instansenheten.

> [!NOTE]
> Energitillståndet beskriver om maskinvaruenheten är påslagen eller avstängd. Det ger inte information om operativsystemet är igång. När du startar om en HANA-enhet för stora instanser får du en liten tid där enhetens tillstånd ändras till **Börjar** röra sig till tillståndet **för Startat**. Att vara i tillståndet **för Startat** innebär att operativsystemet startar eller att operativsystemet har startats upp helt. Efter en omstart av enheten kan du därför inte förvänta dig att omedelbart logga in på enheten så snart tillståndet växlar till **Startat**.
> 

Om du trycker på "Se mer" visas ytterligare information. Ytterligare en information visar revideringen av HANA Large Instance-stämpeln, enheten har distribuerats in. Se artikeln [Vad är SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) för de olika revideringarna av HANA Large Instance-stämplar

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Kontrollera aktiviteter för en enda HANA-enhet för stora instanser 
Förutom att ge en översikt över HANA Large Instance-enheterna kan du kontrollera aktiviteter för den aktuella enheten. En aktivitetslogg kan se ut som:

![Navigeringsfönster i Azure-portalen](./media/hana-li-portal/portal-activity-list.png)

En av de viktigaste aktiviteterna som registreras är omstarter av en enhet. De data som anges innehåller aktivitetens status, tidsstämpeln som aktiviteten utlöste, prenumerations-ID som aktiviteten utlöstes från och den Azure-användare som utlöste aktiviteten. 

En annan aktivitet som registreras är ändringar i enheten i Azure-metadata. Förutom omstarten som initierats kan du se aktiviteten i **Skriv HANAInstances**. Den här typen av aktivitet utför inga ändringar på hana-enheten för stor instans, men dokumenterar ändringar i metadata för enheten i Azure. I det angivna fallet har vi lagt till och tagit bort en tagg (se nästa avsnitt).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Lägga till och ta bort en Azure-tagg till en HANA-enhet för stor instans
En annan möjlighet du har är att lägga till en [tagg](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) till en HANA Large Instance-enhet. Hur taggar tilldelas skiljer sig inte från att tilldela taggar till virtuella datorer. Precis som med virtuella datorer finns taggarna i Azure-metadata och har för STORA HANA-instanser samma begränsningar som taggar för virtuella datorer.

Att ta bort taggar fungerar på samma sätt som med virtuella datorer. Båda aktiviteterna, om du använder och tar bort en tagg, visas i aktivitetsloggen för den specifika HANA-enheten för stora instanser.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Kontrollera egenskaper för en HANA-enhet för stor instans
Avsnittet **Egenskaper** innehåller viktig information som du får när instanserna överlämnas till dig. Det är ett avsnitt där du får all information som du kan behöva i supportärenden eller som du behöver när du ställer in konfiguration av ögonblicksbilder av lagring. Som sådan är det här avsnittet en samling data runt din instans, anslutningen av instansen till Azure och lagring serverdelen. Den övre delen av avsnittet ser ut som:


![övre delen av HLI-egenskaper i Azure-portalen](./media/hana-li-portal/portal-properties-top.png)

De första dataobjekten, du såg i översiktsskärmen redan. Men en viktig del av data är ExpressRoute Circuit ID, som du fick som de första distribuerade enheterna överlämnades. I vissa supportärenden kan du bli tillfrågad om dessa data. En viktig datainmatning visas längst ner på skärmbilden. De data som visas är IP-adressen för NFS-lagringshuvudet som isolerar din lagring till **din klient** i HANA Large Instance-stacken. Den här IP-adressen behövs också när du redigerar [konfigurationsfilen för säkerhetskopior av ögonblicksbilder av lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

När du rullar nedåt i egenskapsfönstret får du ytterligare data som ett unikt resurs-ID för din HANA Large Instance-enhet eller prenumerations-ID som har tilldelats distributionen.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Starta om en STORINSTANSENHET FÖR HANA via Azure-portalen
Initiera en omstart av Linux-operativsystemet, det fanns olika situationer där operativsystemet inte kunde avsluta en omstart framgångsrikt. För att tvinga fram en omstart måste du öppna en tjänstbegäran för att microsoft-åtgärder skulle kunna starta om en omstart av HANA-enheten för stor instans. Funktionen för en omstart av en HANA-stor instans är nu integrerad i Azure-portalen. När du befinner dig i översiktsdelen av HANA-enheten för stora instanser visas knappen för omstart ovanpå dataavsnittet

![Starta om steg #1 i Azure-portalen](./media/hana-li-portal/portal-restart-first-step.png)

När du trycker på omstartsknappen får du frågan om du verkligen vill starta om enheten. Som du bekräftar genom att trycka på knappen "Ja", kommer enheten att starta om.

> [!NOTE]
> I omstartsprocessen kommer du att uppleva en liten tid där enhetens tillstånd ändras till **Börjar** flytta in i tillståndet **för Startat**. Att vara i tillståndet **för Startat** innebär att operativsystemet startar eller att operativsystemet har startats upp helt. Efter en omstart av enheten kan du därför inte förvänta dig att omedelbart logga in på enheten så snart tillståndet växlar till **Startat**.

> [!IMPORTANT]
> Beroende på mängden minne i DIN HANA Large Instance-enhet kan det ta upp till en timme att starta om och starta om maskinvaran och operativsystemet


## <a name="open-a-support-request-for-hana-large-instances"></a>Öppna en supportbegäran för stora HANA-instanser
Utanför Azure-portalvisningen för HANA Large Instance-enheter kan du skapa supportbegäranden specifikt för en stor HANA-instansenhet. När du följer länken **Ny supportbegäran** 

![initiera steg för tjänstbegäran #1 i Azure-portalen](./media/hana-li-portal/portal-initiate-support-request.png)

För att få tjänsten av SAP HANA Stora instanser som anges i nästa skärm, kan du behöva välja "Alla tjänster" som visas nedan

![Välj alla tjänster i Azure-portalen](./media/hana-li-portal/portal-create-service-request.png)

I listan över tjänster hittar du tjänsten **SAP HANA Large Instance**. När du väljer den tjänsten kan du välja specifika problemtyper som visas:


![Välj problemklass i Azure-portalen](./media/hana-li-portal/portal-select-problem-class.png)

Under var och en av de olika problemtyperna erbjuds du ett urval av problemundertyper som du måste välja för att karakterisera problemet ytterligare. När du har valt undertypen kan du nu namnge ämnet. När du är klar med urvalsprocessen kan du gå vidare till nästa steg i skapandet. I avsnittet **Lösningar** pekar du på dokumentation kring STORA HANA-instanser, vilket kan ge en pekare till en lösning på problemet. Om du inte kan hitta en lösning på problemet i den föreslagna dokumentationen går du vidare till nästa steg. I nästa steg kommer du att tillfrågas om problemet är med virtuella datorer eller med HANA-enheter för stora instanser. Denna information hjälper till att rikta supportbegäran till rätt specialister. 

![Information om supportärende i Azure-portalen](./media/hana-li-portal/portal-support-request-details.png)

När du svarade på frågorna och lämnade ytterligare information kan du gå nästa steg för att granska supportbegäran och skicka den.

## <a name="next-steps"></a>Nästa steg

- [Så här övervakar du SAP HANA (stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Övervaka och felsöka från HANA-sida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

