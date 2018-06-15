---
title: Automatisk uppdatering av Mobilitetstjänsten i Azure till Azure-katastrofåterställning | Microsoft Docs
description: En översikt över automatisk uppdatering av Mobilitetstjänsten, vid replikering av virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: rajanaki
ms.openlocfilehash: add80b17c76e7262f55e50cd07d4e9b053cfa1ff
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209839"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatisk uppdatering av Mobilitetstjänsten i Azure till Azure-replikering

Azure Site Recovery har en månatlig versionen takt där förbättringar av befintliga funktioner eller nya läggs och kända problem som eventuellt åtgärdas. Det betyder att för att fortsätta vara aktuella med tjänsten, måste du planera för distribution av dessa korrigeringar, varje månad. Användare kan i stället välja att tillåta Site Recovery för att hantera uppdateringar av komponenter för att undvika över huvud som är associerade med uppgraderingen. Detaljerad i den [arkitektur referens](azure-to-azure-architecture.md) för katastrofåterställning i Azure till Azure, Mobilitetstjänsten installeras på alla virtuella Azure-datorer för vilka replikering har aktiverats vid replikering av virtuella datorer från en Azure till en annan region. När du aktiverar automatisk uppdatering kan uppdateras webbtjänsttillägget Mobility med varje ny utgåva. Det här dokumentet beskriver följande:

- Hur fungerar automatisk uppdatering?
- Aktivera automatiska uppdateringar
- Vanliga problem och felsökning
 
## <a name="how-does-automatic-update-work"></a>Hur fungerar automatisk uppdatering

När du tillåter Site Recovery för att hantera uppdateringar är har en global runbook (som används av Azure-tjänster) distribuerats via ett automation-konto som skapas i samma prenumeration som valvet. Ett automation-konto används för en specifik valvet. Runbook kontrollerar för varje virtuell dator i ett valv som automatisk uppdatering är aktiverade och initierar en uppgradering av tillägget Mobilitetstjänsten om en nyare version är tillgänglig. Standardschemat för runbook-recurrs varje dag klockan 12:00 AM enligt tidszonen för den replikerade virtuella geo. Schemat för runbook kan också ändras via automation-kontot av användaren, om det behövs. 

> [!NOTE]
> Aktivera automatiska uppdateringar kräver inte en omstart av virtuella datorerna i Azure och påverkar inte pågående replikering.

## <a name="enable-automatic-updates"></a>Aktivera automatiska uppdateringar

Du kan välja att tillåta Site Recovery för att hantera uppdateringar på följande sätt:-

- [Som en del av steget Aktivera replikering](#as-part-of-the-enable-replication-step)
- [Aktivera/inaktivera tillägget uppdatera inställningarna inuti valvet](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Som en del av steget Aktivera replikering:

När du aktiverar replikering för en virtuell dator antingen starta [från vyn virtuella](azure-to-azure-quickstart.md), eller [från recovery services-ventilen](azure-to-azure-how-to-enable-replication.md), får du möjlighet att välja att antingen tillåta Site Recovery hantera uppdateringar för Site Recovery-tillägg eller för att hantera samma manuellt.

![Aktivera-replikering-automatisk uppdatering](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Aktivera/inaktivera tillägget uppdatera inställningarna inuti valvet

1. I valvet, gå till **hantera**-> **Site Recovery-infrastruktur**
2. Under **för Azure virtuella datorer**-> **tillägget uppdateringsinställningar**, klickar på växlingsknappen för att välja om du vill tillåta *ASR att hantera uppdateringar* eller *hantera manuellt*. Klicka på **Spara**.

![valvet växla-autuo-uppdatering](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> När du väljer *Tillåt ASR för att hantera*, inställningen tillämpas på alla virtuella datorer i motsvarande valv.


> [!Note] 
> Båda alternativen meddelar dig om automation-kontot som används för att hantera uppdateringar. Om du aktiverar den här funktionen för första gången i ett valv, skapas ett nytt automation-konto. Alla efterföljande aktivera replikeringar i samma valvet kommer att använda det tidigare skapade.

## <a name="common-issues--troubleshooting"></a>Vanliga problem och felsökning

Om det finns ett problem med automatiska uppdateringar kan meddelas du av samma under Configuration problem i instrumentpanelen för valvet. 

Om du försökte att aktivera Automatiska uppdateringar och den inte finns nedan för felsökning.

**Fel**: du har inte behörighet att skapa ett kör som-kontot Azure (tjänstens huvudnamn) och bevilja deltagarrollen till tjänstens huvudnamn. 
- Rekommenderad åtgärd: Se till att det inloggade kontot har tilldelats deltagaren och försök igen.
 
När automatiska uppdateringar är aktiverade, de flesta problem kan lagats av Site Recovery-tjänsten och du måste klicka på den '**reparera**-knappen.

![reparera-knappen](./media/azure-to-azure-autoupdate/repair.png)

Om reparera inte är tillgänglig, finns det felmeddelande som visas under rutan inställningar för tillägget.

 - **Fel**: det kör som-kontot har inte behörighet att komma åt recovery services-resurs.

    **Rekommenderad åtgärd**: ta bort och sedan [skapa kör som-kontot igen](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) eller se till att Automation kör som-kontots Azure Active Directory-program har åtkomst till recovery services-resursen.

- **Fel**: Kör som-kontot finns inte. Antingen en av dem har tagits bort eller inte skapats - Azure Active Directory-program, Service Principal, roll, Automation-certifikattillgång, Automation-anslutningstillgång- eller tumavtrycket inte är identiska mellan certifikat och anslutningen. 

    **Rekommenderad åtgärd**: ta bort och [skapa kör som-kontot igen](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
