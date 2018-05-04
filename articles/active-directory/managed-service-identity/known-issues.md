---
title: Vanliga frågor och kända problem med hanterade tjänsten identitet (MSI) för Azure Active Directory
description: Kända problem med hanterade tjänstidentiteten för Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 78148c6538efa06018628297a89681ec6ec3d32d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Vanliga frågor och kända problem med hanterade tjänsten identitet (MSI) för Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

### <a name="does-msi-work-with-azure-cloud-services"></a>Fungerar MSI med Azure Cloud Services?

Nej, det finns inga planer på att stöd för MSI i Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Fungerar MSI med Active Directory Authentication Library (ADAL) eller Microsoft Authentication Library (MSAL)?

Nej, MSI inte ännu integrerat med ADAL eller MSAL. Mer information om att förvärva en MSI-token med hjälp av MSI REST-slutpunkt finns [hur du använder en Azure VM hanterade tjänsten identitet (MSI) för token](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>Vad är en säkerhetsgräns för en hanterad tjänstidentitet?

Säkerhetsgräns identitet är den resurs som den är kopplad till. Till exempel är säkerhetsgräns för en virtuell dator MSI den virtuella datorn. All kod som körs på den virtuella datorn kan anropa MSI-slutpunkten och begära token. Det är liknande upplevelse med andra resurser som har stöd för MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Bör jag använda MSI VM IMDS slutpunkten eller MSI VM-tillägget slutpunkten?

När du använder MSI med virtuella datorer, gärna med hjälp av MSI IMDS slutpunkten. Tjänsten Azure instans Metadata är en REST-slutpunkt som är tillgängliga för alla IaaS-VM som skapats via Azure Resource Manager. Några av fördelarna med att använda MSI över IMDS är:

1. Alla operativsystem för Azure IaaS stöds kan använda MSI över IMDS. 
2. Behöver inte längre att installera tillägget på den virtuella datorn att aktivera MSI. 
3. Certifikat som används av MSI inte längre finns i den virtuella datorn. 
4. IMDS slutpunkten är en välkänd icke-dirigerbara IP-adress som endast tillgänglig i den virtuella datorn. 

MSI-VM-tillägget är fortfarande tillgängliga som ska användas i dag; dock däromkring vi kommer som standard med hjälp av IMDS-slutpunkten. MSI-VM-tillägget startar på en plan för utfasningen snart. 

Läs mer på Azure-tjänsten för instansen Metada [IMDS dokumentation](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Vad är Linux-distributioner som stöds?

Alla Linux-distributioner som stöds av Azure IaaS kan användas med MSI via IMDS slutpunkten. 

Obs: MSI VM-tillägget stöder bara följande Linux-distributioner:
- Virtuell CoreOS stabil
- CentOS 7.1
- 7.2 RedHat
- Ubuntu 15.04
- Ubuntu 16.04

Andra Linux-distributioner stöds inte för närvarande och tillägg kan misslyckas på distributioner som inte stöds.

Tillägget fungerar på CentOS 6,9. Men på grund av bristande stöd i 6,9 kommer tillägget inte automatisk omstart om kraschat eller stoppats. Den startas om när den virtuella datorn startas om. Om du vill starta om tillägget manuellt finns [hur du starta om MSI-tillägget?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Hur du starta om MSI-tillägget
I Windows och vissa versioner av Linux om tillägget slutar kan följande cmdlet användas för att starta om den manuellt:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Där: 
- Namn och typ för Windows är: ManagedIdentityExtensionForWindows
- Namn och typ för Linux är: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Kända problem

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>”Automatiseringsskriptet” misslyckas vid försök schemaexport för MSI-tillägg

När hanterade tjänstidentiteten är aktiverat på en virtuell dator, visas följande fel vid försök att använda funktionen ”automatiseringsskriptet” för den virtuella datorn eller dess resursgrupp:

![MSI automation export skriptfel](../media/msi-known-issues/automation-script-export-error.png)

Hanterad Service identitet VM-tillägget stöder för närvarande inte möjligheten att exportera dess schema till en resurs Gruppmall. Genererade mallen visas därför inte konfigurationsparametrar för att aktivera hanterade tjänstidentiteten på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall](qs-configure-template-windows-vm.md).

När schemat exportfunktionen blir tillgänglig för MSI VM-tillägg, visas det i [exportera resursgrupper som innehåller VM-tillägg](../../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Konfiguration av bladet visas inte i Azure-portalen

Om bladet VM-konfiguration inte visas på den virtuella datorn, sedan har MSI inte aktiverats i din region portalen ännu.  Kontrollera igen senare.  Du kan också aktivera MSI för din Virtuella med [PowerShell](qs-configure-powershell-windows-vm.md) eller [Azure CLI](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Det går inte att bevilja åtkomst till virtuella datorer i bladet Access Control (IAM)

Om **virtuella** visas inte i Azure-portalen som ett alternativ för **bevilja åtkomst till** i **Access Control (IAM)** > **Lägg till behörigheter**, och sedan hanterade tjänstidentiteten inte har aktiverats i din region portalen ännu. Kontrollera igen senare.  Du kan fortfarande välja hanterade tjänstidentiteten för rolltilldelningen genom att söka för MSI-tjänstens huvudnamn.  Ange namnet på den virtuella datorn i den **Välj** fältet och tjänstens huvudnamn visas i sökresultatet.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuell dator inte kan startas efter flyttas från resursgrupp eller prenumeration

Om du flyttar en virtuell dator körs, fortsätter den att köras under förflyttningen. Men efter överflyttningen, att om den virtuella datorn stoppas och startas om den kunna starta. Det här problemet beror på att den virtuella datorn uppdateras inte referensen till MSI-identitet och fortsätter att peka i gamla resursgruppen.

**Lösning** 
 
Starta en uppdatering på den virtuella datorn så att den får rätt värden för MSI. Du kan göra en VM egenskapen ändra om du vill uppdatera referens till MSI-identitet. Exempelvis kan du ange ett nytt taggvärde för den virtuella datorn med följande kommando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Det här kommandot anger en ny tagg ”fixVM” med värdet 1 för den virtuella datorn. 
 
Genom att ange den här egenskapen kan den virtuella datorn uppdateras med den korrekta MSI-resursen-URI och sedan du ska kunna starta den virtuella datorn. 
 
När den virtuella datorn har startats kan taggen tas bort med hjälp av följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-msi-preview"></a>Kända problem med användaren tilldelas MSI *(förhandsgranskning)*

- Det enda sättet att ta bort alla användare som tilldelats MSI: er tilldelas genom att aktivera systemet MSI. 
- Etablering av VM-tillägget till en virtuell dator kan misslyckas på grund av fel i DNS-sökning. Starta om den virtuella datorn och försök igen. 
- Om du lägger till en 'obefintlig' MSI kommer den virtuella datorn misslyckas. *Obs: Korrigering misslyckas tilldela identitet om MSI inte finns, används platta ut*
- Azure Storage-kursen är endast tillgängligt i centrala oss EUAP för tillfället. 
- Skapar en användare som tilldelats MSI med specialtecken (dvs understreck) i namn stöds inte.
- Om att lägga till en annan användare har tilldelats identitet, kanske clientID inte tillgänglig för begäranden token för den. Starta om MSI-VM-tillägget med följande två bash-kommandon som en lösning:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- VMAgent på Windows stöder för närvarande inte användaren tilldelas MSI. 
- När en virtuell dator har en användare som tilldelats MSI men inget system tilldelade MSI portalen Användargränssnittet visar MSI som aktiverad. Använda en Azure Resource Manager-mall, ett Azure CLI eller en SDK om du vill aktivera tilldelats MSI.
