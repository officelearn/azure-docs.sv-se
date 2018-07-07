---
title: Vanliga frågor och kända problem med hanterad tjänstidentitet (MSI) för Azure Active Directory
description: Kända problem med hanterad tjänstidentitet för Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 05096050dfc29aebd2859b298eef884dcd9a1111
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908079"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Vanliga frågor och kända problem med hanterad tjänstidentitet (MSI) för Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

### <a name="does-msi-work-with-azure-cloud-services"></a>Fungerar MSI med Azure Cloud Services?

Nej, det finns inga planer på att stöd för MSI i Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Fungerar MSI med Active Directory Authentication Library (ADAL) eller Microsoft Authentication Library (MSAL)?

Nej, MSI är inte ännu integrerad med ADAL eller MSAL. Mer information om skaffa en MSI-token med hjälp av MSI-REST-slutpunkt finns [hur du använder en Azure VM hanterad tjänstidentitet (MSI) för tokenförvärv](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>Vad är säkerhetsgräns för en hanterad tjänstidentitet?

Säkerhetsgräns identitetsinformationen är den resurs som den är ansluten till. Säkerhetsgräns för en virtuell dator MSI är till exempel den virtuella datorn. Kod som körs på den virtuella datorn kan anropa MSI-slutpunkten och begära token. Det är samma möjligheter med andra resurser som har stöd för MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Ska jag använda MSI VM IMDS slutpunkten eller MSI VM-tillägget slutpunkten?

När du använder MSI med virtuella datorer, rekommenderar vi att du med hjälp av MSI IMDS slutpunkt. Azure Instance Metadata Service är en REST-slutpunkt som är tillgängliga för alla virtuella IaaS-datorer skapas via Azure Resource Manager. Några av fördelarna med att använda MSI över IMDS är:

1. Alla operativsystem för Azure IaaS som stöds kan använda MSI över IMDS. 
2. Behöver inte längre att installera ett tillägg på den virtuella datorn ska aktivera MSI. 
3. Certifikat som används av MSI finns inte längre i den virtuella datorn. 
4. IMDS slutpunkten är en välkänd icke-dirigerbara IP-adress som endast tillgängliga i den virtuella datorn. 

MSI-VM-tillägget är fortfarande tillgängligt som ska användas i dag; men framöver vi som standard IMDS-slutpunkten. MSI-VM-tillägget startar snart en utfasning på. 

Läs mer på Azure-instanstjänsten Metada [IMDS dokumentation](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Vad är Linux-distributioner som stöds?

Alla Linux-distributioner som stöds av Azure IaaS kan användas med MSI via IMDS-slutpunkt. 

Obs: VM-tillägget MSI stöder endast följande Linux-distributioner:
- CoreOS stabila
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Andra Linux-distributioner stöds inte för närvarande och tillägg kan misslyckas på distributioner som inte stöds.

Tillägget fungerar på CentOS 6,9. Men på grund av bristande stöd i 6,9 kommer tillägget inte automatisk omstart om kraschat eller stoppats. Den startar om när den virtuella datorn startas om. Om du vill starta om tillägget manuellt finns i [hur du starta om MSI-tillägget?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Hur du starta om MSI-tillägget?
På Windows och vissa versioner av Linux om tillägget slutar kan följande cmdlet användas för att starta om den manuellt:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Där: 
- Namn på tillägg och typ för Windows är: ManagedIdentityExtensionForWindows
- Tilläggsnamn och typ för Linux är: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Kända problem

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>”Automationsskript” misslyckas vid försök Se export schématu för MSI-tillägget

När hanterad tjänstidentitet har aktiverats på en virtuell dator, visas följande felmeddelande när du försöker använda funktionen ”automationsskript” för den virtuella datorn eller en resursgrupp:

![MSI automation skriptfel export:](../media/msi-known-issues/automation-script-export-error.png)

Managed Service Identity VM-tillägget stöder för närvarande inte möjligheten att exportera dess schema till en resursgruppmall. Därför visas inte den genererade mallen konfigurationsparametrar för att aktivera hanterad tjänstidentitet för resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [konfigurera en virtuell dator hanterad tjänstidentitet med hjälp av en mall](qs-configure-template-windows-vm.md).

När schemat exportfunktionen blir tillgängligt för MSI-VM-tillägget visas det i [exportera resursgrupper som innehåller VM-tillägg](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Konfigurationsbladet visas inte i Azure portal

Om bladet konfiguration av virtuell dator inte visas på den virtuella datorn, sedan har MSI inte aktiverats i portalen i din region ännu.  Kontrollera igen senare.  Du kan också aktivera MSI för den virtuella datorn med [PowerShell](qs-configure-powershell-windows-vm.md) eller [Azure CLI](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Det går inte att tilldela åtkomst till virtuella datorer i bladet åtkomstkontroll (IAM)

Om **VM** visas inte i Azure-portalen som ett alternativ för **tilldela åtkomst till** i **åtkomstkontroll (IAM)** > **Lägg till behörigheter**, och sedan hanterad tjänstidentitet inte har ännu aktiverats i portalen i din region. Kontrollera igen senare.  Du kan fortfarande välja den hanterade tjänstidentiteten för rolltilldelningen genom att söka för MSI tjänstens huvudnamn.  Ange namnet på den virtuella datorn i den **Välj** fältet och tjänstens huvudnamn som visas i sökresultatet.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuell dator inte kan startas efter flyttas från resursgrupp eller prenumeration

Om du flyttar en virtuell dator i körläge fortsätter det att köras under förflyttningen. Men efter överflyttningen att om den virtuella datorn stoppas och startas om, den kunna starta. Det här problemet inträffar eftersom den virtuella datorn uppdateras inte referensen till MSI-identitet och fortsätter att peka till den i den gamla resursgruppen.

**Lösning** 
 
Utlösa en uppdatering på den virtuella datorn så att den kan hämta rätt värden för MSI. Du kan göra en virtuell dator Egenskapsändring för att uppdatera referensen till MSI-identitet. Du kan till exempel ange en ny tagg-värde på den virtuella datorn med följande kommando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Det här kommandot anger en ny tagg ”fixVM” med värdet 1 för den virtuella datorn. 
 
Den virtuella datorn uppdateras med den korrekta MSI-resursen-URI genom att ange den här egenskapen och sedan du ska kunna starta den virtuella datorn. 
 
När den virtuella datorn har startats kan taggen tas bort med hjälp av följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Kända problem med tilldelade användaridentiteter

- Användartilldelningar tilldelade identitet är bara tillgängligt för virtuella datorer och VMSS. Viktigt: Tilldelade användaridentitet tilldelningar kommer att ändras under de kommande månaderna.
- Duplicerade tilldelade användaridentiteter på samma VM/VMSS genereras VM/VMSS misslyckas. Detta inkluderar identiteter som har lagts till med ett annat skiftläge. t.ex. MyUserAssignedIdentity och myuserassignedidentity. 
- Etablering av VM-tillägg till en virtuell dator kan misslyckas på grund av DNS-sökning fel. Starta om den virtuella datorn och försök igen. 
- Att lägga till en ”icke-existerande' Användartilldelad identitet gör att den virtuella datorn misslyckas. 
- Det går inte att skapa en Användartilldelad identitet med specialtecken (t.ex. understreck) i namnet.
- Användartilldelad identitetsnamn är begränsade till 24 tecken för slutpunkt till slutpunkt-scenario. Tilldelad användaridentiteter med namn som är längre än 24 tecken kan inte tilldelas.
- Om du använder tillägget för virtuell dator hanterad identitet, är gränsen som stöds 32 användartilldelade hanterade identiteter. Gränsen som stöds är 512 utan tillägget för virtuell dator hanterad identitet.  
- Om att lägga till en annan användare har tilldelats identitet, kanske clientID inte tillgänglig för begäranden token för VM-tillägget. Starta om MSI-VM-tillägget med följande två bash-kommandon som en lösning:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- När en virtuell dator har en Användartilldelad identitet, men inga systemtilldelad identitet, Användargränssnittet visar portalen MSI som inaktiverade. Aktivera systemtilldelad identitet genom att använda en Azure Resource Manager-mall, en Azure CLI eller SDK.
