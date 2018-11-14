---
title: Azure Stack Windows Server relaterade vanliga frågor och svar | Microsoft Docs
description: Lista över Azure Stack Marketplace vanliga frågor och svar för Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: bf70f9a74b58758e03800d7f6fb92a8f8754828a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613103"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server i Azure Stack Marketplace vanliga frågor och svar

Den här artikeln får du svar på några vanliga frågor om Windows Server-avbildningar i den [Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Marketplace-objekt

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Hur uppdaterar jag till en nyare Windows-avbildning?

Börja med att kontrollera om alla Azure Resource Manager-mallar som avser specifika versioner. I så, fall uppdatera mallarna, eller Behåll äldre avbildningsversioner. Det är bäst att använda **versionen: senaste**.

Sedan om alla Virtual Machine Scale Sets refererar till en specifik version, bör du tänka om dessa kommer att skalas senare och Bestäm om du vill behålla äldre versioner. Ta bort äldre avbildningar i Marketplace innan du laddar ned nya om inget av dessa villkor gäller. Använd Marketplace Management för att göra detta om det är hur ursprungligt hämtades. Ladda ned den nya versionen.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Vad är licensalternativ för Windows Server Marketplace-avbildningar på Azure Stack?

Microsoft erbjuder två versioner av Windows Server-avbildningar i Azure Stack Marketplace:

- **Betala för det du använder**: dessa avbildningar kör Windows-mätare fullt pris. 
   Vem ska använda: Enterprise Agreement (EA)-kunder som använder den *förbrukning faktureringsmodellen*; CSP: er som inte vill använda SPLA licensiering.
- **Bring Your Own License (BYOL)**: dessa avbildningar kör grundläggande taxor.
   Vem ska använda: EA-kunder med en Windows Server-licens; CSP: er som använder SPLA licensiering.

Azure Hybrid Använd förmånen (AHUB) stöds inte på Azure Stack. Kunder som licensierar genom ”kapacitet” modellen måste använda BYOL-avbildning. Om du testar med Azure Stack Development Kit (ASDK), kan du använda något av dessa alternativ.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Vad händer om jag har hämtat fel version för att erbjuda Mina klienter/användare?

Ta bort fel version först via hantering av Marketplace. Vänta tills den har slutförts helt (titta på meddelanden för slutförande, inte bladet för hantering av Marketplace). Ladda ned rätt version.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Vad händer om min användaren felaktigt markerat kryssrutan ”Jag har en licens” i tidigare Windows bygger och de inte har någon licens?

Se [konvertera Windows Server-datorer med förmånen tillbaka till betala per användning](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Vad händer om jag har en äldre bild och min användaren har glömt att markera kryssrutan ”Jag har en licens” eller vi använder våra egna avbildningar och vi har rättigheten för Enterprise-avtal?

Se [konvertera en befintlig virtuell dator med hjälp av Azure Hybrid-förmånen för Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Observera att Azure Hybrid-förmånen gäller inte för Azure Stack, men effekten av den här inställningen gäller.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Vad gäller för andra virtuella datorer som använder Windows Server, till exempel SQL och Machine Learning Server?

Dessa avbildningar gäller den **licenseType** parameter, så att de är betala som du använder. Du kan ställa in den här parametern (se föregående vanliga frågor och svar-svar). Detta gäller endast för Windows Server-programvara inte till lager produkter, till exempel SQL, som kräver att du kan använda din egen licens. Betala per användning gäller när du använder licensiering inte för överlappande programprodukter.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Jag har ett Enterprise Agreement (EA) och kommer att använda min EA Windows Server-licens; hur kan jag vara säker på att avbildningar debiteras korrekt?

Du kan lägga till **licenseType: Windows_Server** i en Azure Resource Manager-mall. Den här inställningen måste läggas till varje virtuell dator resurs-block.

## <a name="activation"></a>Aktivering

Om du vill aktivera en Windows Server-dator på Azure Stack, måste följande villkor vara uppfyllda:

- OEM har angett rätt BIOS markören på varje värdsystem i Azure Stack.
- Windows Server 2012 R2 och Windows Server 2016 måste använda [automatisk aktivering av virtuell dator](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Nyckelhanteringstjänsten (KMS) och andra aktiveringstjänster stöds inte på Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Hur kan jag kontrollera att min virtuella dator har aktiverats?

Kör följande kommando från en upphöjd kommandotolk: 

```shell
slmgr /dlv
``` 

Om den aktiveras korrekt, visas det här tydligt anges och värdnamnet visas i den `slmgr` utdata. Inte bero på vattenstämplar på skärmen som de inte kanske är uppdaterad, eller visar från en annan virtuell dator bakom själv.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Den virtuella datorn har inte ställts in att använda AVMA, hur kan jag åtgärda det?

Kör följande kommando från en upphöjd kommandotolk: 

```shell
slmgr /ipk <AVMA key> 
```

Finns i artikeln [automatisk aktivering av virtuell dator](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) för nycklarna som ska användas för din avbildning.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Jag skapa min egen Windows Server-avbildningar, hur kan jag kontrollera att de använder AVMA?

Vi rekommenderar att du kör den `slmgr /ipk` från kommandoraden med rätt nyckel innan du kör den `sysprep` kommando. Eller inkludera AVMA-nyckeln i en Unattend.exe-installationsfil.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Jag försöker använda Mina Windows Server 2016-avbildning som har skapats på Azure och inte aktivera eller med hjälp av KMS-aktivering.

Kör `slmgr /ipk`-kommandot. Azure-avbildningar kan inte korrekt återgår till AVMA, men om de kan nå Azure KMS-system, de aktiveras. Vi rekommenderar att du ser till dessa virtuella datorer är inställd på att använda AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Jag har utfört alla dessa steg, men ändå aktiverar inte mina virtuella datorer.

Kontakta din maskinvaruleverantör för att kontrollera att rätt BIOS-markörer har installerats.

### <a name="what-about-earlier-versions-of-windows-server"></a>Vad gäller tidigare versioner av Windows Server?

[Automatisk aktivering av virtuell dator](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) stöds inte i tidigare versioner av Windows Server. Du måste du aktivera de virtuella datorerna manuellt.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Azure Stack Marketplace-översikt](azure-stack-marketplace.md)
- [Hämta marketplace-objekt från Azure till Azure Stack](azure-stack-download-azure-marketplace-item.md)
