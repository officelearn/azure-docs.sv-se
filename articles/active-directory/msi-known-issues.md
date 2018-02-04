---
title: "Vanliga frågor och kända problem med hanterade tjänsten identitet (MSI) för Azure Active Directory"
description: "Kända problem med hanterade tjänstidentiteten för Azure Active Directory."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 0541e63a1d3467b9691032e66892efe8f0f0cad8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Vanliga frågor och kända problem med hanterade tjänsten identitet (MSI) för Azure Active Directory

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

### <a name="is-there-a-private-preview-available-for-additional-features"></a>Finns det en privat förhandsgranskning tillgängligt för ytterligare funktioner?

Ja. Om du vill att anses vara för registrering i privat förhandsvisning [sidan med våra anmälan](https://aka.ms/azuremsiprivatepreview).

### <a name="does-msi-work-with-azure-cloud-services"></a>Fungerar MSI med Azure Cloud Services?

Nej, det finns inga planer på att stöd för MSI i Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Fungerar MSI med Active Directory Authentication Library (ADAL) eller Microsoft Authentication Library (MSAL)?

Nej, MSI inte ännu integrerat med ADAL eller MSAL. Mer information om att förvärva en MSI-token med hjälp av MSI REST-slutpunkt finns [hur du använder en Azure VM hanterade tjänsten identitet (MSI) för token](msi-how-to-use-vm-msi-token.md).

### <a name="what-are-the-supported-linux-distributions"></a>Vad är Linux-distributioner som stöds?

Följande Linux-distributioner har stöd för MSI: 

- Virtuell CoreOS stabil
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04

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

![MSI automation export skriptfel](media/msi-known-issues/automation-script-export-error.png)

Hanterad Service identitet VM-tillägget stöder för närvarande inte möjligheten att exportera dess schema till en resurs Gruppmall. Genererade mallen visas därför inte konfigurationsparametrar för att aktivera hanterade tjänstidentiteten på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall](msi-qs-configure-template-windows-vm.md).

När schemat exportfunktionen blir tillgänglig för MSI VM-tillägg, visas det i [exportera resursgrupper som innehåller VM-tillägg](../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Konfiguration av bladet visas inte i Azure-portalen

Om bladet VM-konfiguration inte visas på den virtuella datorn, sedan har MSI inte aktiverats i din region portalen ännu.  Kontrollera igen senare.  Du kan också aktivera MSI för din Virtuella med [PowerShell](msi-qs-configure-powershell-windows-vm.md) eller [Azure CLI](msi-qs-configure-cli-windows-vm.md).

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
