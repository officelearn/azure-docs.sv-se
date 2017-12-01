---
title: "Felsök vanliga fel i Azure-distribution | Microsoft Docs"
description: "Beskriver hur du löser vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "distributionsfel för azure-distribution distribuera till azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: tomfitz
ms.openlocfilehash: db7561c31c0748ae5c1500ba8c39dfa79274901e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Felsöka vanliga Azure-distribution med Azure Resource Manager

Den här artikeln beskriver vissa vanliga Azure distributionsfel du kan stöta på och ger information om du vill åtgärda felen. Om du inte hittar felkoden för din distribution felet, se [hittar felkod](#find-error-code).

## <a name="error-codes"></a>Felkoder

| Felkod | Åtgärd | Mer information |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Följ namngivningsbegränsningar för lagringskonton. | [Lös lagringskontonamnet](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Kontrollera tillgängligt lagringsutrymme kontoegenskaperna. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Det kluster eller den region har inte resurser som är tillgänglig eller har inte stöd för den begärda VM-storleken. Försöka senare eller be en annan VM-storlek. | [Problem med etablering och fördelningen för Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) och [problem med etablering och fördelningen för Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Vänta tills den samtidiga åtgärden har slutförts. | |
| AuthorizationFailed | Ditt konto eller tjänstens huvudnamn har inte behörighet att slutföra distributionen. Kontrollera ditt konto hör till rollen och dess behörighet för omfånget för distributionen. | [Rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Du har skickat distribution värden som inte matchar vad som förväntas av Resource Manager. Kontrollera det inre statusmeddelanden för hjälp med felsökning. | [Mallreferensen](/azure/templates/) och [platser som stöds](resource-manager-template-location.md) |
| Konflikt | Du begär en åtgärd som inte tillåts i resursens aktuella tillstånd. Till exempel tillåts ändra storlek på diskar endast när du skapar en virtuell dator eller när den virtuella datorn har frigjorts. | |
| DeploymentActive | Vänta tills samtidiga distributionen till den här resursgruppen ska slutföras. | |
| DnsRecordInUse | DNS-postnamn måste vara unikt. Ange ett annat namn eller ändra den befintliga posten. | |
| ImageNotFound | Kontrollera inställningarna för VM-avbildning. | [Felsöka Linux bilder](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) och [felsöka Windows-avbildningar](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| InUseSubnetCannotBeDeleted | Det här felet kan uppstå när du försöker uppdatera en resurs, men begäran har bearbetats genom att ta bort och skapa resursen. Se till att ange alla värden som är oförändrade. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hämta åtkomsttoken för den lämpliga innehavaren. Du kan bara hämta token från den klient som kontot tillhör. | |
| InvalidContentLink | Du har troligen försökt att länka till en kapslad mall som inte är tillgänglig. Kontrollera den URI som du angav för den kapslade mallen. Om mallen finns i ett lagringskonto, kontrollera att URI: N är tillgänglig. Du kan behöva passera en SAS-token. | [Länkade mallar](resource-group-linked-templates.md) |
| InvalidParameter | Ett av de värden som du angav för en resurs matchar inte det förväntade värdet. Det här felet kan bero på många olika villkor. Till exempel ett lösenord kan vara otillräcklig eller en blobbnamnet kan vara felaktigt. Läs felmeddelandet att avgöra vilket värde som behöver åtgärdas. | |
| InvalidRequestContent | Värden krävs för din distribution värden som innehåller värden som inte förväntas eller saknas. Kontrollera värdena för din resurstypen. | [Mallreferensen](/azure/templates/) |
| InvalidRequestFormat | Aktivera felsökningsloggning vid körning av distributionen och kontrollera innehållet i begäran. | [Felsökningsloggning](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | Kontrollera resursnamnrymden som du angav i den **typen** egenskapen. | [Mallreferensen](/azure/templates/) |
| InvalidResourceReference | Resursen finns ännu inte eller är felaktigt refererar till. Kontrollera om du behöver lägga till ett beroende. Kontrollera att din användning av den **referens** funktion innehåller de obligatoriska parametrarna för ditt scenario. | [Lös beroenden](resource-manager-not-found-errors.md) |
| InvalidResourceType | Kontrollera resursen skriver du angav i den **typen** egenskapen. | [Mallreferensen](/azure/templates/) |
| InvalidTemplate | Kontrollera din mallens syntax för fel. | [Lös ogiltig mall](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Kontrollera om ditt konto tillhör samma klient som du distribuerar till resursgruppen. | |
| LinkedInvalidPropertyId | Resurs-ID för en resurs matchar inte korrekt. Kontrollera att du anger alla nödvändiga värden för resurs-ID, inklusive prenumerations-ID, resursgruppens namn, resurstyp, överordnade resurs (om det behövs) och resursnamnet. | |
| LocationRequired | Ange en plats för din resurs. | [Ange en plats](resource-manager-template-location.md) |
| MissingRegistrationForLocation | Kontrollera status för resource provider för registrering och platser som stöds. | [Lös registrering](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registrera prenumerationen med resursprovidern. | [Lös registrering](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Kontrollera registreringsstatus i resource provider. | [Lös registrering](resource-manager-register-provider-errors.md) |
| NotFound | Du kanske försöker distribuera en beroende resurs parallellt med en överordnad resurs. Kontrollera om du behöver lägga till ett beroende. | [Lös beroenden](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Distributionen är försöker utföra en åtgärd som överskrider kvoten för prenumerationen, resursgruppen eller region. Ändra distributionen för att hålla sig inom kvoterna om möjligt. I annat fall bör du begära en ändring av din kvoter. | [Lös kvoter](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Kontrollera att det finns en överordnad-resurs innan du skapar underordnat resurser. | [Lösa överordnade resursen](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | Den angivna IP-adressen innehåller ett adressintervall som krävs av Azure. Ändra IP-adress för att undvika reserverade intervallet. | [IP-adresser](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Den angivna IP-adressen ligger utanför intervallet för undernätet. Ändra IP-adressen ligger inom intervallet för undernätet. | [IP-adresser](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Vissa egenskaper kan inte ändras för en distribuerad resurs. När du uppdaterar en resurs, begränsa ändringarna till tillåtna egenskaper. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Prenumerationen innehåller en resursprincip som förhindrar att en åtgärd som du försöker utföra under distributionen. Hitta den princip som blockerar åtgärden. Om möjligt ändra distributionen för att uppfylla begränsningarna från principen. | [Lös principer](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Ange en resurs som inte innehåller ett reserverat namn. | [Reserverade resursnamn](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Vänta tills borttagningen ska slutföras. | |
| ResourceGroupNotFound | Kontrollera namnet på målresursgruppen för distributionen. Det måste finnas i din prenumeration. Kontrollera din prenumerationskontext. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Distributionen refererar till en resurs som inte kan matchas. Kontrollera att din användning av den **referens** funktion innehåller de parametrar som krävs för ditt scenario. | [Lösa referenser](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Distributionen försöker att skapa resurser som överskrider kvoten för prenumerationen, resursgruppen eller region. Om möjligt ändra din infrastruktur för att hålla sig inom kvoter. I annat fall bör du begära en ändring av din kvoter. | [Lös kvoter](resource-manager-quota-errors.md) |
| SkuNotAvailable | Välj SKU (till exempel VM-storlek) som är tillgänglig för den plats som du har valt. | [Lös SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Ange ett unikt namn för lagringskontot. | [Lös lagringskontonamnet](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Ange ett unikt namn för lagringskontot. | [Lös lagringskontonamnet](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Kontrollera prenumerationen, resursgruppen och namnet på det lagringskonto som du försöker använda. | |
| SubnetsNotInSameVnet | En virtuell dator kan bara ha ett virtuellt nätverk. När du distribuerar flera nätverkskort kan du se till att de hör till samma virtuella nätverk. | [Flera nätverkskort](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Hitta felkod

När det uppstår ett fel under distributionen av returnerar Resource Manager en felkod. Du kan se felmeddelandet via portalen, PowerShell eller Azure CLI. Yttre felmeddelandet kan vara för allmänna för felsökning. Leta efter inre meddelandet som innehåller detaljerad information om felet. Mer information finns i [fastställa felkoden](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>Nästa steg
* Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](resource-manager-deployment-operations.md).
