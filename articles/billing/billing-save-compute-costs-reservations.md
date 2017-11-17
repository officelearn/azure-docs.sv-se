---
title: "Spara pengar genom före betalar för virtuella datorer i Azure - Azure | Microsoft Docs"
description: "Läs mer om Azure reserverade virtuella datorinstans att spara på kostnaderna för virtuella datorer."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: ab97feac7dd92cdc64343e215f6c623a6de9ef60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Spara pengar på virtuella datorer med den reserverade virtuella instanser 
Den reserverade virtuella instanser kan du före betala för ett år eller tre års beräkningskapacitet att få rabatt på de virtuella datorerna som du använder. Minskar avsevärt kostnaderna för virtuella datorer, upp till 72 procent på betalning per användning priser – med Summa åtagande för ett år eller tre år. Den reserverade virtuella instanser är fakturering rabatt och påverkar inte runtime-tillståndet för virtuella datorer.

Du kan köpa en reserverad virtuell datorinstans den [Azure-portalen](https://aka.ms/reservations). Mer information finns i [Förskottsbetala för virtuella datorer och spara pengar med den reserverade virtuella instanser](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Varför ska jag köpa en reserverad virtuell datorinstans?
Om du har virtuella datorer som körs under långa perioder ger köpa en reserverad virtuell datorinstans dig det bästa effektiva priset. Till exempel om du kör kontinuerligt fyra instanser av Standard D2 i USA, västra region, debiteras utan en reservation du lön-som-gå-du priser. Om du köper en reserverad virtuell dator-instans för de fyra virtuella datorer, tillgång virtuella datorer direkt till fakturering. De debiteras inte längre på de lön-som-du gå priser. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Vilka avgifter omfattar en reserverad virtuell datorinstans?
En reservation omfattar bara virtuella infrastrukturen avgifterna för Windows eller Linux virtuella datorer. En reservation omfattar inte ytterligare avgifter för program, nätverk och lagring. För Windows-datorer kan du omfattar Windows licensieringskostnader med [Azure Hybrid förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Vem är berättigad att köpa en reserverad virtuell datorinstans?
Azure-kunder med dessa prenumerationer typer kan du köpa en reserverad virtuell dator-instans:
-   Enterprise-avtalstyp av prenumerationserbjudande (MS-AZR - 0017P).
-   [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) typ av prenumerationserbjudande (MS-AZR - 003 P).
Du måste ha rollen ”ägare” på prenumerationen du köper reserverade instans. För att köpa reservationer i enterprise-registrering aktivera företagsadministratören reservation inköp i EA-portal inställningen är aktiverat som standard.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Hur är ett reserverat virtuella Datorinstanser inköp faktureras?
Reservation inköpet debiteras betalningsmetod knutna till prenumerationen. Om du har en Enterprise-prenumeration dras reservation kostnaden från ditt summa saldo. Om ditt summa saldo inte täcker kostnaden för reservation, är du debiteras överförbrukning.
Om du har en prenumeration betala per användning debiteras det kreditkort som du har på ditt konto omedelbart. Om du använder debiteras per faktura visas avgifterna på fakturan nästa.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Hur tillämpas köpta reserverade virtuella datorinstans rabatten?
Den reserverade virtuella datorinstans rabatten gäller för virtuella datorer som matchar de attribut som du väljer när du köper reservationen. Attributen inkluderar området där de matchande virtuella datorerna kör. Om du vill rabatt reserverade VM-instans för fyra virtuella datorer som Standard D2 i regionen USA, västra markerar du den prenumeration där de virtuella datorerna körs. Om de virtuella datorerna körs i olika prenumerationer i registrering/konto, väljer du omfånget som delat. Delade scope kan reservation rabatten ska användas för alla prenumerationer.
Du kan ändra omfånget när du köper reserverade VM-instans. Om du vill ändra omfattningen finns i dokumentationen om hur du hanterar reservationer.

Reservation lågpristaxa gäller endast virtuella datorer i prenumerationer med enterprise eller erbjudna betala allteftersom-typer. Virtuella datorer som körs i en prenumeration med andra erbjudandetyper får ingen reservation rabatt. Företagsprenumerationer för utveckling och testning inte berättigad till reserverade fördelar för enterprise-registreringar.

Hur reservation påverkar virtuella fakturering förklaras i [förstå tillämpningen av reservation fakturering förmån](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="what-happens-when-the-reservation-term-expires"></a>Vad händer när reservation termen upphör att gälla?
I slutet av reservation termen fakturering rabatterna upphör att gälla och virtuella infrastrukturen faktureras enligt lön-som-du gå priset. Reservationer inte automatisk förnyelse. Om du vill fortsätta få fakturering rabatten, måste du köpa en ny instans för reserverade virtuella datorn. 

## <a name="sizes-and-regional-availability"></a>Storlekar och Regional tillgänglighet
Reservationer är tillgängliga för de flesta VM-storlekar med vissa undantag:
- Förhandsgranska VM-storlekar – oavsett storlek som är i förhandsvisning är inte tillgängliga för den reserverade virtuella datorinstans inköp.
- Moln – reserverade virtuella instanser är inte tillgängliga i Azure som tillhör amerikanska myndigheter, Tyskland eller Kina regioner. 
- Otillräcklig kvoten – A reserverade VM-instans som är begränsade till en enda prenumeration måste ha vCPU kvot som är tillgängliga i prenumerationen för den nya RI. Till exempel om målprenumerationen har en kvot på högst 10 vCPUs för D-serien familj, kan inte sedan du köpa en reserverad VM-instans för 11 Standard_D1 instanser. Kvot ingår för reservationer de virtuella datorerna som redan har distribuerats i prenumerationen. Om exempelvis prenumerationen har en kvot på 10 vCPUs för D-serien familj. Om den här prenumerationen har två standard_D1 instanser distribueras, kan du köpa en reserverad VM-instans för 10 standard_D1 instanser i den här prenumerationen. 
- Begränsningar för kapacitet – i sällsynta fall kan Azure gränser köp av ny reservationer för delmängd av VM-storlekar på grund av låg kapacitet i en region.

## <a name="next-steps"></a>Nästa steg
Starta sparar på dina virtuella datorer genom att köpa en [reserverade virtuella datorinstans](https://go.microsoft.com/fwlink/?linkid=861721). 

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
