---
title: Vanliga frågor och andra frågor och kända problem med hanterade identiteter - Azure AD
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
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266538"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Vanliga frågor och andra problem med hanterade identiteter för Azure-resurser

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar (vanliga frågor)

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Fungerar hanterade identiteter för Azure-resurser med Azure Cloud Services?

Nej, det finns inga planer på att stödja hanterade identiteter för Azure-resurser i Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Fungerar hanterade identiteter för Azure-resurser med Active Directory Authentication Library (ADAL) eller Microsoft Authentication Library (MSAL)?

Nej, hanterade identiteter för Azure-resurser är ännu inte integrerade med ADAL eller MSAL. Mer information om hur du hämtar en token för hanterade identiteter för Azure-resurser med rest-slutpunkten finns i [Så här använder du hanterade identiteter för Azure-resurser på en Azure-virtuell dator för att hämta en åtkomsttoken](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Vad är säkerhetsgränsen för hanterade identiteter för Azure-resurser?

Säkerhetsgränsen för identiteten är den resurs som den är kopplad till. Till exempel är säkerhetsgränsen för en virtuell dator med hanterade identiteter för Azure-resurser aktiverade den virtuella datorn. Alla kod som körs på den virtuella datorn kan anropa de hanterade identiteterna för Azure-resursslutpunkten och begära token. Det är liknande upplevelse med andra resurser som stöder hanterade identiteter för Azure-resurser.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Vilken identitet ska IMDS som standard om du inte anger identiteten i begäran?

- Om den systemtilldelade hanterade identiteten är aktiverad och ingen identitet anges i begäran, kommer IMDS som standard att systemet tilldelas hanterad identitet.
- Om den hanterade identiteten inte är aktiverad och det bara finns en användare som tilldelats den hanterade identiteten, kommer IMDS som standard att den enda användare som tilldelats den hanterade identiteten. 
- Om den tilldelade systemtilldelade hanterade identiteten inte är aktiverad och det finns flera användartilldelade hanterade identiteter, krävs att du anger en hanterad identitet i begäran.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Ska jag använda de hanterade identiteterna för AZURE-resurser IMDS-slutpunkten eller slutpunkten för VM-tillägget?

När du använder hanterade identiteter för Azure-resurser med virtuella datorer rekommenderar vi att du använder IMDS-slutpunkten. Azure Instance Metadata Service är en REST-slutpunkt som är tillgänglig för alla virtuella IaaS-datorer som skapats via Azure Resource Manager. 

Några av fördelarna med att använda hanterade identiteter för Azure-resurser via IMDS är:
- Alla Azure IaaS-stödda operativsystem kan använda hanterade identiteter för Azure-resurser via IMDS.
- Behöver inte längre installera ett tillägg på den virtuella datorn för att aktivera hanterade identiteter för Azure-resurser. 
- Certifikaten som används av hanterade identiteter för Azure-resurser finns inte längre i den virtuella datorn.
- IMDS-slutpunkten är en välkänd icke-dirigerbar IP-adress, endast tillgänglig från den virtuella datorn.
- 1000 användartilldelade hanterade identiteter kan tilldelas en enda virtuell dator. 

De hanterade identiteterna för VM-tillägget för Azure-resurser är fortfarande tillgängliga. Vi utvecklar dock inte längre nya funktioner på den. Vi rekommenderar att du byter till IMDS-ändpunkten. 

Några av begränsningarna med att använda slutpunkten för vm-tillägget är:
- Begränsat stöd för Linux-distributioner: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- Endast 32 användartilldelade hanterade identiteter kan tilldelas den virtuella datorn.


De hanterade identiteterna för VM-tillägget för Azure-resurser kommer att vara utan stöd i januari 2019. 

Mer information om Azure Instance Metadata Service finns i [IMDS-dokumentation](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Återskapas hanterade identiteter automatiskt om jag flyttar en prenumeration till en annan katalog?

Nej. Om du flyttar en prenumeration till en annan katalog måste du manuellt återskapa dem och bevilja Azure RBAC-rolltilldelningar igen.
- För systemtilldelade hanterade identiteter: inaktivera och aktivera igen. 
- För användartilldelade hanterade identiteter: ta bort, återskapa och bifoga dem igen till nödvändiga resurser (t.ex. virtuella datorer)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan jag använda en hanterad identitet för att komma åt en resurs i en annan katalog/klient?

Nej. Hanterade identiteter stöder för närvarande inte scenarier för korskataloger. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Vilka Azure RBAC-behörigheter krävs för att hanterad identitet på en resurs? 

- Systemtilldelade hanterade identitet: Du behöver skrivbehörighet över resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Den här åtgärden ingår i resursspecifika inbyggda roller som [deltagare i virtuell dator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Användartilldelade hanterade identitet: Du behöver skrivbehörighet över resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Förutom [rolltilldelning för hanterad identitetsoperator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) över den hanterade identiteten.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hur startar du om de hanterade identiteterna för Azure-resurstillägg?
På Windows och vissa versioner av Linux, om tillägget stoppas, kan följande cmdlet användas för att manuellt starta om den:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Där: 
- Tilläggsnamn och typ för Windows är: ManagedIdentityExtensionForWindows
- Tilläggsnamn och typ för Linux är: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Kända problem

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automation script" misslyckas vid försök till schemaexport för hanterade identiteter för Azure-resurstillägg

När hanterade identiteter för Azure-resurser är aktiverade på en virtuell dator visas följande fel när du försöker använda funktionen "Automation script" för den virtuella datorn eller dess resursgrupp:

![Exportera fel i det hanterade identiteterna för Azure-resursautomatiseringsskript](./media/msi-known-issues/automation-script-export-error.png)

De hanterade identiteterna för AZURE Resources VM-tillägg (som planeras för utfasning i januari 2019) stöder för närvarande inte möjligheten att exportera schemat till en resursgruppmall. Därför visar den genererade mallen inte konfigurationsparametrar för att aktivera hanterade identiteter för Azure-resurser på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [Konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure med hjälp av en mall .](qs-configure-template-windows-vm.md)

När schemaexportfunktionen blir tillgänglig för hanterade identiteter för VM-tillägg för Azure resources (planeras för utfasning i januari 2019) visas den i [Exportera resursgrupper som innehåller VM-tillägg](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Den virtuella datorn kan inte starta efter att ha flyttats från resursgrupp eller prenumeration

Om du flyttar en virtuell dator i körtillståndet fortsätter den att köras under flytten. Men efter flytten, om den virtuella datorn stoppas och startas om, kommer det inte att starta. Det här problemet beror på att den virtuella datorn inte uppdaterar referensen till de hanterade identiteterna för Azure-resursidentitet och fortsätter att peka på den i den gamla resursgruppen.

**Workaround** 
 
Utlösa en uppdatering på den virtuella datorn så att den kan få rätt värden för hanterade identiteter för Azure-resurser. Du kan göra en vm-egenskapsändring för att uppdatera referensen till de hanterade identiteterna för Azure-resursidentitet. Du kan till exempel ange ett nytt taggvärde på den virtuella datorn med följande kommando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Det här kommandot anger en ny tagg "fixVM" med värdet 1 på den virtuella datorn. 
 
Genom att ange den här egenskapen uppdateras den virtuella datorn med rätt hanterade identiteter för Azure-resursresurs-URI, och sedan bör du kunna starta den virtuella datorn. 
 
När den virtuella datorn har startats kan taggen tas bort med följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Etablering av vm-tillägg misslyckas

Etablering av vm-tillägget kan misslyckas på grund av DNS-sökningsfel. Starta om den virtuella datorn och försök igen.
 
> [!NOTE]
> Vm-tillägget är planerat att vara avfasat senast i januari 2019. Vi rekommenderar att du går över till att använda IMDS-slutpunkten.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Överföra en prenumeration mellan Azure AD-kataloger

Hanterade identiteter uppdateras inte när en prenumeration flyttas/överförs till en annan katalog. Därför bryts alla befintliga systemtilldelade eller användartilldelade hanterade identiteter. 

Lösning för hanterade identiteter i en prenumeration som har flyttats till en annan katalog:

 - För systemtilldelade hanterade identiteter: inaktivera och aktivera igen. 
 - För användartilldelade hanterade identiteter: ta bort, återskapa och bifoga dem igen till nödvändiga resurser (t.ex. virtuella datorer)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Flytta en användartilldelad hanterad identitet till en annan resursgrupp/prenumeration

Om du flyttar en användartilldelad hanterad identitet till en annan resursgrupp bryts identiteten. Därför kan resurser (t.ex. virtuell dator) som använder den identiteten inte begära token för den. 
