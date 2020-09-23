---
title: Vanliga frågor och svar om Azure automanage för Virtual Machines
description: Svar på vanliga frågor om Azure automanage för virtuella datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: ed97f7861f5dd959fd41ac22b4e497f492dbc3a3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941940"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Vanliga frågor och svar om Azure automanage för virtuella datorer

Den här artikeln innehåller svar på några av de vanligaste frågorna om [Azure automanage för virtuella datorer](automanage-virtual-machines.md).

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera på [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Du kan också skicka in en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du **Hämta support**på [Sidan Support för Azure](https://azure.microsoft.com/support/options/).


## <a name="azure-automanage-for-virtual-machines"></a>Azure automanage för virtuella datorer

**Vilka är alla krav som krävs för att aktivera Azure automanage?**

Följande är förutsättningar för att aktivera Azure automanage:
- Endast Windows Server-VM
- Virtuella datorer måste köras
- Virtuella datorer måste finnas i en region som stöds
- Användaren måste ha rätt behörighet
- Endast icke-skala uppsättning virtuella datorer
- Virtuella datorer får inte länka till en Log Analytics-arbetsyta i en annan prenumeration

**Vilken RBAC-behörighet krävs för att aktivera autohantering?**

Användare måste ha ägar rollen. Användare kan också ha rollen deltagare tillsammans med rollen administratör för användar åtkomst för att tillämpa automanage.


**Vilka regioner stöds?**

Virtuella datorer i följande regioner stöds: Västeuropa, östra, USA, västra USA 2, centrala Kanada, västra centrala USA.


**Vilka funktioner hanterar Azure automatiskt?**

Hantera automatiskt registrering, konfiguration och övervakning under hela livs cykeln för den virtuella datorn de tjänster som listas [här](virtual-machines-best-practices.md).


**Kan jag anpassa konfigurationer på Azure automanage?**

Kunder kan anpassa inställningar för vissa tjänster, t. ex. Azure Backup kvarhållning, via konfigurations inställningar. En fullständig lista över inställningar som kan ändras finns i vår dokumentation [här](virtual-machines-best-practices.md).


**Fungerar Azure autohantering med både virtuella Linux-och Windows-datorer?**

För närvarande stöder hantering av virtuella Windows Server Azure-datorer.


**Kan jag selektivt tillämpa autohantering endast på en uppsättning virtuella datorer?**

Funktionen Hantera kan aktive ras med Klicka och punkt-enkelhet på valda nya och befintliga virtuella datorer. Den automatiska hanteringen kan också inaktive ras när som helst.


**Stöder Azure automatiska hantering av virtuella datorer i en skalnings uppsättning för virtuella datorer?**

Nej, Azure automanage stöder för närvarande inte virtuella datorer i en skalnings uppsättning för virtuella datorer.


**Hur mycket hanterar Azure autokostnaden?**

Automatisk Azure-hantering är tillgängligt utan extra kostnad i den offentliga för hands versionen. Anslutna Azure-resurser, till exempel Azure Backup, kommer att kosta.


**Kan jag använda automanage via Azure policy?**

Ja, vi har en inbyggd princip som automatiskt tillämpar automatisk hantering för alla virtuella datorer i det definierade omfånget. Du kommer också att ange konfigurations profilen (DevTest eller produktion) tillsammans med ditt konto för automatisk hantering. Läs mer om hur du aktiverar autohantering via Azure policy [här](virtual-machines-policy-enable.md).


**Vad är ett konto för autohantering?**

Kontot för automatisk hantering är en MSI (Hanterad tjänstidentitet) som tillhandahåller säkerhets kontexten eller den identitet under vilken de automatiserade åtgärderna sker.


**Kan jag ändra konfigurations profilen för min virtuella dator?**

För tillfället måste du inaktivera automatisk hantering för den virtuella datorn och sedan återaktivera automatisk hantering med önskad konfigurations profil och inställningar.


**Om min virtuella dator redan har kon figurer ATS för en tjänst, t. ex. Uppdateringshantering, kommer automatiskt att hantera den automatiskt?**
Nej, den automatiska hanteringen kommer inte att konfigurera om den. Vi börjar övervaka resurserna som är kopplade till tjänsten för drift.


**Varför har den virtuella datorn statusen Misslyckad i automanage-portalen?**

Om du ser status som *misslyckad*kan du felsöka distributionen via resurs gruppen som den virtuella datorn finns i. Gå till **resurs grupper**, välj din resurs grupp, klicka på **distributioner** och se statusen *misslyckades* där, med fel information.

**Hur kan jag få hjälp med fel sökning av autohantering?**

Du kan använda en [ärende biljett för teknisk support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). För **tjänst** alternativet söker du efter och väljer *hantera automatisk hantering* under avsnittet *övervakning och hantering* .


## <a name="next-steps"></a>Nästa steg

Försök att aktivera autohantering för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)