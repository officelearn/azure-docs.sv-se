---
title: 'Azure Databricks: vanliga frågor och hjälp'
description: Få svar på vanliga frågor och felsöknings information om Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8707aa55c1126af6a7fb2812d4c7314f031209d0
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597483"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Vanliga frågor om Azure Databricks

Den här artikeln innehåller de vanligaste frågorna som du kanske har relaterat till Azure Databricks. Här visas också några vanliga problem som du kan ha när du använder Databricks. Mer information finns i [Vad är Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Kan jag använda Azure Key Vault för att lagra nycklar/hemligheter som ska användas i Azure Databricks?
Ja. Du kan använda Azure Key Vault för att lagra nycklar/hemligheter som ska användas med Azure Databricks. Mer information finns i [Azure Key Vault-omfångs omfattningar](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#akv-ss).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Kan jag använda virtuella Azure-nätverk med Databricks?
Ja. Du kan använda ett Azure-Virtual Network (VNET) med Azure Databricks. Mer information finns i [distribuera Azure Databricks i Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Hur gör jag för att åtkomst Azure Data Lake Storage från en bärbar dator? 

Följ de här stegen:
1. I Azure Active Directory (Azure AD) etablerar du ett huvud namn för tjänsten och registrerar nyckeln.
1. Tilldela de nödvändiga behörigheterna till tjänstens huvud namn i Data Lake Storage.
1. Om du vill komma åt en fil i Data Lake Storage använder du autentiseringsuppgifterna för tjänstens huvud namn i antecknings boken.

Mer information finns i [använda Azure Data Lake Storage med Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Åtgärda vanliga problem

Här är några problem som kan uppstå med Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problem: den här prenumerationen har inte registrerats för användning av namn området Microsoft. Databricks

#### <a name="error-message"></a>Felmeddelande

"Den här prenumerationen är inte registrerad för att använda namn området Microsoft. Databricks. Se https://aka.ms/rps-not-found för hur du registrerar prenumerationer. (Kod: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Lösning

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Välj **prenumerationer**, den prenumeration du använder och sedan **resurs leverantörer**. 
1. I listan över resurs leverantörer, mot **Microsoft. Databricks**, väljer du **Registrera**. Du måste ha rollen deltagare eller ägare av prenumerationen för att kunna registrera resurs leverantören.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: ditt konto {email} har inte rollen ägare eller deltagare på Databricks-arbetsytans resurs i Azure Portal

#### <a name="error-message"></a>Felmeddelande

"Ditt konto {email} saknar ägare eller deltagar roll i Databricks-arbetsytans resurs i Azure Portal. Det här felet kan också inträffa om du är gäst användare i klienten. Be administratören att ge dig åtkomst eller lägga till dig som en användare direkt i Databricks-arbetsytan. " 

#### <a name="solution"></a>Lösning

Följande är några lösningar på problemet:

* För att initiera klienten måste du vara inloggad som en vanlig användare av klienten, inte som en gäst användare. Du måste också ha en deltagar roll i Databricks-arbetsytans resurs. Du kan ge en användare åtkomst från fliken **åtkomst kontroll (IAM)** i din Databricks-arbetsyta i Azure Portal.

* Det här felet kan också inträffa om ditt e-postdomännamn tilldelas till flera kataloger i Azure AD. Undvik det här problemet genom att skapa en ny användare i katalogen som innehåller prenumerationen med din Databricks-arbetsyta.

    a. I Azure Portal går du till Azure AD. Välj **användare och grupper**  > **Lägg till en användare**.

    b. Lägg till en användare med ett `@<tenant_name>.onmicrosoft.com` e-postmeddelande i stället för `@<your_domain>` e-post. Du kan hitta det här alternativet i **anpassade domäner**under Azure AD i Azure Portal.
    
    c. Ge den här nya användaren rollen **deltagare** i Databricks-arbetsytans resurs.
    
    d. Logga in på Azure Portal med den nya användaren och leta upp arbets ytan Databricks.
    
    e. Starta arbets ytan Databricks som den här användaren.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: ditt konto {email} har inte registrerats i Databricks 

#### <a name="solution"></a>Lösning

Om du inte skapade arbets ytan och du har lagts till som en användare, kontaktar du personen som skapade arbets ytan. Be personen att lägga till dig med hjälp av Azure Databricks administratörs konsolen. Instruktioner finns i [lägga till och hantera användare](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Om du har skapat arbets ytan och fortfarande får det här felet kan du prova att välja **initiera arbets yta** igen från Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problem: det gick inte att starta Cloud Provider när klustret konfigurerades (PublicIPCountLimitReached)

#### <a name="error-message"></a>Felmeddelande

"Det gick inte att starta moln providern: ett Cloud Provider-fel uppstod när klustret skulle konfigureras. Mer information finns i Databricks-hand boken. Azure-felkod: PublicIPCountLimitReached. Azure-fel meddelande: det går inte att skapa fler än 60 offentliga IP-adresser för den här prenumerationen i den här regionen. "

#### <a name="solution"></a>Lösning

Databricks-kluster använder en offentlig IP-adress per nod. Om din prenumeration redan har använt alla offentliga IP-adresser, bör du [begära att öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Välj **kvot** som **typ av problem**och **nätverk: arm** som **kvot typ**. I **information**, begär en offentlig IP-adress-kvot ökning. Om din gräns till exempel är 60 och du vill skapa ett kluster med 100-noder, begär du en gräns ökning till 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problem: en andra typ av moln leverantörs startfel vid konfiguration av klustret (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Felmeddelande

"Det gick inte att starta moln providern: ett Cloud Provider-fel uppstod när klustret skulle konfigureras. Mer information finns i Databricks-hand boken.
Azure-felkod: MissingSubscriptionRegistration Azure-fel meddelande: prenumerationen har inte registrerats för användning av namn området Microsoft. Compute. Se https://aka.ms/rps-not-found för att registrera prenumerationer. "

#### <a name="solution"></a>Lösning

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Välj **prenumerationer**, den prenumeration du använder och sedan **resurs leverantörer**. 
1. I listan över resurs leverantörer, mot **Microsoft. Compute**, väljer du **Registrera**. Du måste ha rollen deltagare eller ägare av prenumerationen för att kunna registrera resurs leverantören.

Mer detaljerade instruktioner finns i [resurs leverantörer och typer](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problem: Azure Databricks behöver behörigheter för att få åtkomst till resurser i din organisation som bara en administratör kan bevilja.

#### <a name="background"></a>Bakgrund

Azure Databricks är integrerat med Azure Active Directory. Du kan ange behörigheter inom Azure Databricks (till exempel på antecknings böcker eller kluster) genom att ange användare från Azure AD. För att Azure Databricks ska kunna visa namnen på användarna från din Azure AD, kräver det Läs behörighet till den informationen och samtycker till att ges. Om godkännandet inte redan är tillgängligt visas fel meddelandet.

#### <a name="solution"></a>Lösning

Logga in som global administratör till Azure Portal. För Azure Active Directory går du till fliken **användar inställningar** och ser till att **användarna kan godkänna att appar som har åtkomst till företags information för deras räkning** har angetts till **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Snabb start: kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Vad är Azure Databricks?](what-is-azure-databricks.md)

