---
title: Vanliga frågor och kända problem med hanterade identiteter för Azure-resurser
description: Kända problem med hanterade identiteter för Azure-resurser.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e958aa82eb1e2fbf21a44df333533c6da058a966
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448482"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Vanliga frågor och kända problem med hanterade identiteter för Azure-resurser

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Fungerar hanterade identiteter för Azure-resurser med Azure Cloud Services?

Nej, det finns inga planer på att stöd för hanterade identiteter för Azure-resurser i Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Fungerar hanterade identiteter för Azure-resurser med Active Directory Authentication Library (ADAL) eller Microsoft Authentication Library (MSAL)?

Nej, hanterade identiteter för Azure-resurser ännu inte är integrerad med ADAL eller MSAL. Mer information om skaffa en token som hanterade identiteter för Azure-resurser med hjälp av REST-slutpunkt finns [hur du använder hanterade identiteter för Azure-resurser på en Azure virtuell dator för att hämta en åtkomsttoken](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Vad är säkerhetsgränsen hanterade identiteter för Azure-resurser?

Säkerhetsgräns identitetsinformationen är den resurs som den är ansluten till. Till exempel säkerhetsgräns för en virtuell dator med hanterade identiteter för Azure-resurser aktiverad, är den virtuella datorn. Kod som körs på den virtuella datorn kan anropa hanterade identiteter för Azure-resurser token för slutpunkt och begäran. Det är samma möjligheter med andra resurser som stöder hanterade identiteter för Azure-resurser.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Vilka identity kommer IMDS standard om inte anger identiteten i begäran?

- Om systemtilldelad hanterad identitet är aktiverad och ingen identitet har angetts i begäran, som IMDS standard i systemet som tilldelats hanterad identitet.
- Om systemtilldelad hanterad identitet inte har aktiverats och finns bara en användare som tilldelats hanterad identitet, som IMDS standard till den enda användaren som tilldelats hanterad identitet. 
- Om systemtilldelad hanterad identitet inte har aktiverats, och flera användare som tilldelats hanterade identiteter finns, sedan ange en hanterad identitet i begäran måste anges.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Ska jag använda de hanterade identiteterna för Azure-resurser IMDS slutpunkt eller VM-tillägget slutpunkten?

När du använder hanterade identiteter för Azure-resurser med virtuella datorer, bör du använda IMDS-slutpunkten. Azure Instance Metadata Service är en REST-slutpunkt som är tillgängliga för alla virtuella IaaS-datorer skapas via Azure Resource Manager. 

Några av fördelarna med att använda hanterade identiteter för Azure-resurser över IMDS är:
- Alla operativsystem för Azure IaaS som stöds kan använda hanterade identiteter för Azure-resurser via IMDS.
- Inte längre behöver du installera ett tillägg på den virtuella datorn att aktivera hanterade identiteter för Azure-resurser. 
- Certifikat som används av hanterade identiteter för Azure-resurser finns inte längre i den virtuella datorn.
- IMDS slutpunkten är en välkänd icke-dirigerbara IP-adress som endast tillgängliga i den virtuella datorn.
- 1000 användartilldelade hanterade identiteter kan tilldelas till en enskild virtuell dator. 

Hanterade identiteter för VM-tillägg för Azure-resurser är fortfarande tillgänglig. Vi är dock inte längre att utveckla nya funktioner på den. Vi rekommenderar att du väljer att använda IMDS-slutpunkten. 

Vissa begränsningar med att använda VM-tillägg-slutpunkten är:
- Begränsat stöd för Linux-distributioner: CoreOS stabila, CentOS 7.1, 7.2 för Red Hat, Ubuntu 15.04, Ubuntu 16.04
- Endast 32 användartilldelade hanterade identiteter kan tilldelas till den virtuella datorn.


Obs! Hanterade identiteter för Azure-resurser VM-tillägget kommer att januari 2019 support upphör. 

Läs mer på Azure Instance Metadata Service [IMDS dokumentation](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Hanterade identiteter återskapas automatiskt om jag flyttar en prenumeration till en annan katalog?

Nej. Om du flyttar en prenumeration till en annan katalog, måste du manuellt återskapa dem och ge Azure RBAC-rolltilldelningar igen.
- För system tilldelade hanterade identiteter: inaktivera och återaktivera. 
- För användare tilldelade hanterade identiteter: ta bort, skapa och koppla dem till resurser (t.ex. virtuella datorer)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan jag använda en hanterad identitet för att komma åt en resurs i en annan katalog/klient?

Nej. Hanterade identiteter stöder för närvarande inte mellan directory scenarier. 

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hur du starta om de hanterade identiteterna för tillägget för Azure-resurser
På Windows och vissa versioner av Linux om tillägget slutar kan följande cmdlet användas för att starta om den manuellt:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Där: 
- Namn på tillägg och typ för Windows är: ManagedIdentityExtensionForWindows
- Tilläggsnamn och typ för Linux är: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Kända problem

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>”Automationsskript” misslyckas vid försök schemat exporten av hanterade identiteter för tillägget för Azure-resurser

När hanterade identiteter för Azure-resurser är aktiverat på en virtuell dator, visas följande felmeddelande när du försöker använda funktionen ”automationsskript” för den virtuella datorn eller en resursgrupp:

![Hanterade identiteter för Azure-resurser automationsskript exportfel](./media/msi-known-issues/automation-script-export-error.png)

Hanterade identiteter för Azure-resurser VM-tillägg (planerad för utfasning i januari 2019) har för närvarande inte stöd för möjligheten att exportera dess schema till en resursgruppmall. Därför visas inte den genererade mallen konfigurationsparametrar för att aktivera hanterade identiteter för Azure-resurser på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av en mallar](qs-configure-template-windows-vm.md).

När schemat exportfunktionen blir tillgängliga för hanterade identiteter för VM-tillägg för Azure-resurser (planerad för utfasning i januari 2019), kommer den att listas i [exportera resursgrupper som innehåller VM-tillägg](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuell dator inte kan startas efter flyttas från resursgrupp eller prenumeration

Om du flyttar en virtuell dator i körläge fortsätter det att köras under förflyttningen. Men efter överflyttningen att om den virtuella datorn stoppas och startas om, den kunna starta. Det här problemet inträffar eftersom den virtuella datorn uppdateras inte referensen till de hanterade identiteterna för Azure-resurser identitet och fortsätter att peka till den i den gamla resursgruppen.

**Lösning** 
 
Utlösa en uppdatering på den virtuella datorn så att den kan hämta rätt värden för de hanterade identiteterna för Azure-resurser. Du kan göra en virtuell dator Egenskapsändring för att uppdatera referensen till de hanterade identiteterna för Azure-resurser identitet. Du kan till exempel ange en ny tagg-värde på den virtuella datorn med följande kommando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Det här kommandot anger en ny tagg ”fixVM” med värdet 1 för den virtuella datorn. 
 
Den virtuella datorn uppdaterar med rätt hanterade identiteter för Azure-resurser resurs-URI genom att ange den här egenskapen och sedan du ska kunna starta den virtuella datorn. 
 
När den virtuella datorn har startats kan taggen tas bort med hjälp av följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>VM-tillägget etablering misslyckas

Etablering av VM-tillägget kan misslyckas på grund av DNS-sökning fel. Starta om den virtuella datorn och försök igen.
 
> [!NOTE]
> VM-tillägget är planerat för utfasning av januari 2019. Vi rekommenderar att du flyttar till med hjälp av IMDS-slutpunkt.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Överföra en prenumeration mellan Azure AD-kataloger

Hanterade identiteter inte uppdateras när en prenumeration har flyttats/överförs till en annan katalog. Alla existerande systemtilldelade eller användartilldelade hanterade identiteter kommer därför att brytas. 

Lösning för hanterade identiteter i en prenumeration som har flyttats till en annan katalog:

 - För system tilldelade hanterade identiteter: inaktivera och återaktivera. 
 - För användare tilldelade hanterade identiteter: ta bort, skapa och koppla dem till resurser (t.ex. virtuella datorer)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Flytta en hanterad Användartilldelad identitet till en annan resursgrupp/prenumeration

Om du flyttar en hanterad Användartilldelad identitet till en annan resursgrupp kommer identiteten att avbryta. Resurser (t.ex. VM) med hjälp av den identiteten kommer därför inte att kunna begära token för den. 
