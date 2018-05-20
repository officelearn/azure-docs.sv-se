---
title: Vad är Azure reserverade instanser? -Azure fakturering | Microsoft Docs
description: Läs mer om Azure reserverade VM-instanser och priser för att spara på kostnaderna för virtuella datorer och få bästa möjliga effektiva pris VM.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 93be4bb037af400599b88bb71f34143ee65a5deb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="what-are-azure-reserved-vm-instances"></a>Vad är Azure reserverade VM-instanser?
[Azure VM-instanser som reserverat](https://azure.microsoft.com/pricing/reserved-vm-instances) hjälper dig spara pengar genom att före betala för ett år eller tre års beräkningskapacitet så att du att få rabatt på de virtuella datorerna som du använder. Azure-reserverade instanser kan avsevärt minska kostnaderna virtuella – upp till 72 procent på betalning per användning priser – med Summa åtagande för ett år eller tre år. Reserverade instanser ange fakturering rabatt och påverkar inte runtime-tillståndet för de virtuella datorerna.

Du kan köpa reserverade instansen (RI) den [Azure-portalen](https://aka.ms/reservations). Mer information finns i [Förskottsbetala för virtuella datorer och spara pengar med reserverade instanser](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Varför ska jag köpa en reserverad instans?
Om du har virtuella datorer som körs under långa perioder ger det billigaste alternativet om du köper reserverade instans. Till exempel om du kör kontinuerligt fyra instanser av en Standard D2 VM i USA, västra region, debiteras utan en reserverad instans du med betalning per användning priser. Om du köper en instans som är reserverade för de fyra virtuella datorer, tillgång virtuella datorer direkt till fakturering. De är inte längre debiteras med betalning per användning priser. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Vilka avgifter omfattar en reserverad instans?
En reserverad instans omfattar bara virtuella infrastrukturen avgifterna för Windows eller Linux virtuella datorer. En reserverad instans omfattar inte ytterligare avgifter för program, nätverk och lagring. För Windows-datorer kan du omfattar Windows licensieringskostnader med [Azure Hybrid förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Vem är berättigad att köpa en reserverad instans?
Azure-kunder med dessa prenumerationer typer kan du köpa en reserverad instans:
-   Enterprise-avtalstyp av prenumerationserbjudande (MS-AZR - 0017P).
-   [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) typ av prenumerationserbjudande (MS-AZR - 003 P). Du måste ha rollen ”ägare” på prenumerationen du köper reserverade instans. När du köper reserverade instanser i en enterprise-registrering aktivera företagsadministratören reserverade inköp i EA-portalen. Den här inställningen är aktiverad som standard.
-   Cloud Solution Providers (CSP) partners kan använda Azure Portal eller [Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations) att köpa reserverade instanser.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Hur är ett reserverat instans inköp faktureras?
Reserverade inköpet debiteras betalningsmetod knutna till prenumerationen. Om du har en Enterprise-prenumeration dras reserverade kostnaden från ditt summa saldo. Om ditt summa saldo inte täcker kostnaden för den reserverade instansen, är du debiteras överförbrukning.
Om du har en prenumeration betala per användning debiteras det kreditkort som du har på ditt konto omedelbart. Om du använder debiteras per faktura visas avgifterna på fakturan nästa.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Hur tillämpas köpta reserverade rabatten?
Reserverade rabatten gäller för virtuella datorer som matchar de attribut som du väljer när du köper reserverade instansen. Attributen inkluderar området där de matchande virtuella datorerna kör. Om du vill reserverade rabatt för fyra virtuella datorer som Standard D2 i regionen USA, västra markerar du den prenumeration där de virtuella datorerna körs. Om de virtuella datorerna körs i olika prenumerationer i registrering/konto, väljer du omfånget som delat. Delade scope kan reserverade rabatten ska användas för alla prenumerationer. Du kan ändra omfånget när du köper reserverade instans. Om du vill ändra omfattningen finns i dokumentationen om hur du hanterar ensamrätt instanser.

Reserverade lågpristaxa gäller endast virtuella datorer som är associerade med enterprise eller betala per användning prenumerationstyper. Virtuella datorer som körs i en prenumeration med andra erbjudandetyper får inte reserverade rabatt. Företagsprenumerationer för utveckling och testning inte berättigad till reserverade fördelar för enterprise-registreringar.

För att bättre förstå hur reserverade påverkar din virtuella fakturering, se [förstå tillämpningen av reserverade instanser fakturering förmån](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Vad händer när reserverade termen upphör att gälla?
I slutet av reserverade termen fakturering rabatterna upphör att gälla och virtuella infrastrukturen faktureras enligt lön-som-du gå priset. Reserverade instanser inte automatisk förnyelse. Om du vill fortsätta få fakturering rabatten, måste du köpa en ny reserverad instans. 

## <a name="sizes-and-regional-availability"></a>Storlekar och Regional tillgänglighet
Reserverade instanser är tillgängliga för de flesta VM-storlekar med vissa undantag:
- Virtuella datorer i Preview – alla VM-serien eller som är i förhandsgranskningen är inte tillgängliga för reserverade inköp.
- Moln – reserverade instanser är inte tillgängliga i Azure som tillhör amerikanska myndigheter, Tyskland eller Kina regioner. 
- Otillräcklig kvoten – en reserverad instans som är begränsade till en enda prenumeration måste ha vCPU kvot som är tillgängliga i prenumerationen för den nya RI. Till exempel om målprenumerationen har en kvot på högst 10 vCPUs för D-serien, kan sedan du köpa en instans som är reserverade för 11 Standard_D1 instanser. Kvot ingår för reserverade instanser de virtuella datorerna som redan har distribuerats i prenumerationen. Till exempel om prenumerationen har en kvot på 10 vCPUs för D-serien och har två standard_D1 instanser distribueras kan köpa du en instans som är reserverade för 10 standard_D1 instanser i den här prenumerationen. 
- Kapacitet begränsningar – i sällsynta fall kan Azure gränser inköp av nya reserverade instanser för delmängd av VM-storlekar, på grund av låg kapacitet i en region.

## <a name="next-steps"></a>Nästa steg
Starta sparar på dina virtuella datorer genom att köpa en [Azure reserverade instans](https://go.microsoft.com/fwlink/?linkid=861721). 

Mer information om reserverade instanser finns i följande artiklar:

- [Förskottsbetala virtuella datorer med reserverade instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reserverade instanser](billing-manage-reserved-vm-instance.md)
- [Förstå hur de reserverade rabatterna används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå reserverade användning för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)
- [Reserverade instanser i programmet för Partner Center Cloud Solution Providers (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
