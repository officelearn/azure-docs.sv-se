---
title: "Azure Databricks: Vanliga frågor och hjälper dig | Microsoft Docs"
description: "Få svar på vanliga frågor och felsökningsinformation om Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: nitinme
ms.openlocfilehash: 5da6ffc346cc0e7f0f83bf4a4c33600b668a17ca
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Vanliga frågor och svar om Azure Databricks

Den här artikeln innehåller de vanligaste frågorna som du kan ha relaterade till Azure Databricks. Dessutom visas några vanliga problem som du kanske när du använder Databricks. Mer information finns i [vad är Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>Kan jag använda min egen nycklar för kryptering av lokala? 
I den aktuella versionen stöds med egna nycklar från Azure Key Vault inte. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Kan jag använda virtuella Azure-nätverk med Databricks?
Ett nytt virtuellt nätverk skapas som en del av Databricks etablering. I den här versionen kan du inte använda ditt eget virtuella Azure-nätverket.

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Hur kommer jag åt Azure Data Lake Store från en bärbar dator? 

Följ de här stegen:
1. Etablera ett huvudnamn för tjänsten i Azure Active Directory (Azure AD) och registrera sin nyckel.
2. Tilldela behörighet till tjänstens huvudnamn i Data Lake Store.
3. Använd service principal autentiseringsuppgifter i anteckningsboken för att komma åt en fil i Data Lake Store.

Mer information finns i [Använd Data Lake Store med Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Åtgärda problem

Här följer några problem som kan uppstå med Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problem: Den här prenumerationen har inte registrerats för användning av namnrymden 'Microsoft.Databricks'

#### <a name="error-message"></a>Felmeddelande

”Den här prenumerationen har inte registrerats för användning av namnrymden 'Microsoft.Databricks'. Se https://aka.ms/rps-not-found för att registrera prenumerationer. (Code: MissingSubscriptionRegistration) ”

#### <a name="solution"></a>Lösning

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj **prenumerationer**, den prenumeration som du använder, och sedan **resursproviders**. 
3. I listan över resursproviders, mot **Microsoft.Databricks**väljer **registrera**. Du måste ha rollen Medarbetare eller ägare på prenumerationen på registrera resursprovidern.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: Kontot {e-post} inte har rollen som ägare eller deltagare på resursen Databricks arbetsytan i Azure-portalen

#### <a name="error-message"></a>Felmeddelande

”Ditt konto {e-post} har inte ägare eller deltagare roll på resursen Databricks arbetsytan i Azure-portalen. Det här felet kan också inträffa om du är en gästanvändaren i klienten. Be administratören att ge åtkomst till, eller lägga till dig som en användare direkt i arbetsytan Databricks ”. 

#### <a name="solution"></a>Lösning

Här följer några lösningar på problemet:

* För att initiera klienten, måste du vara inloggad som en användare med klienten inte som gästanvändare. Du måste också ha en deltagarrollen på resursen Databricks arbetsytan. Du kan ge en användaråtkomst från den **åtkomstkontroll (IAM)** fliken inom Databricks arbetsytan i Azure-portalen.

* Det här felet kan också inträffa om ditt domännamn för e-post har tilldelats flera kataloger i Azure AD. Undvik problemet genom att skapa en ny användare i katalogen som innehåller prenumerationen med Databricks arbetsytan.

    a. Gå till Azure AD i Azure-portalen. Välj **användare och grupper** > **lägga till en användare**.

    b. Lägga till en användare med en `@<tenant_name>.onmicrosoft.com` e-post i stället för `@<your_domain>` e-post. Du hittar det här alternativet i **anpassade domäner**, under Azure AD i Azure-portalen.
    
    c. Ge den nya användaren i **deltagare** -rollen på resursen Databricks arbetsytan.
    
    d. Logga in på Azure-portalen med den nya användaren och hitta arbetsytan Databricks.
    
    e. Starta arbetsytan Databricks som den här användaren.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: Kontot {e-post} har inte registrerats i Databricks 

#### <a name="solution"></a>Lösning

Om du inte har skapat arbetsytan och du har lagts till som en användare, kontaktar du den person som skapade arbetsytan. Har personen lägga till dig med hjälp av Azure Databricks Admin-konsolen. Instruktioner finns i [lägga till och hantera användare](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Om du har skapat arbetsytan och fortfarande det här felet visas, försök med att välja **initiera arbetsytan** igen från Azure-portalen.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problem: Molnet providern startfel när du konfigurerar klustret (PublicIPCountLimitReached)

#### <a name="error-message"></a>Felmeddelande

”Molnet startfel för providern: ett moln provider-fel uppstod vid konfigurera klustret. Mer information finns i guiden Databricks. Azure-felkod: PublicIPCountLimitReached. Azure-felmeddelande: Det går inte att skapa mer än 60 offentliga IP-adresser för den här prenumerationen i den här regionen ”.

#### <a name="solution"></a>Lösning

Databricks kluster använder en offentlig IP-adress per nod. Om din prenumeration har redan använts alla dess offentliga IP-adresser, bör du [begäran om att öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Välj **kvot** som den **utfärda typ**, och **nätverk: ARM** som den **Kvottypen**. I **information**, begära en ökad för kvot av offentlig IP-adress. Till exempel om du vill skapa ett kluster med noder 100 gränsen är för närvarande 60 och få gränsen utökad till 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problem: En andra typen av molnet providern startfel när du konfigurerar klustret (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Felmeddelande

”Molnet startfel för providern: ett moln provider-fel uppstod vid konfigurera klustret. Mer information finns i guiden Databricks.
Azure-felkod: MissingSubscriptionRegistration Azure-felmeddelande: prenumerationen har inte registrerats för användning av namnrymden 'Microsoft.Compute'. Se https://aka.ms/rps-not-found för att registrera prenumerationer ”.

#### <a name="solution"></a>Lösning

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj **prenumerationer**, den prenumeration som du använder, och sedan **resursproviders**. 
3. I listan över resursproviders, mot **Microsoft.Compute**väljer **registrera**. Du måste ha rollen Medarbetare eller ägare på prenumerationen på registrera resursprovidern.

Mer instruktioner finns [resursproviders och typer](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problem: Azure Databricks behöver behörighet att komma åt resurser i din organisation som bara en administratör kan ge.

#### <a name="background"></a>Bakgrund

Azure Databricks är integrerad med Azure AD. Detta kan du ange behörigheter i Azure Databricks (till exempel på bärbara datorer eller kluster) genom att ange användare från Azure AD. För Azure Databricks för att kunna visa en lista med namnen på användarna från din Azure AD, kräver den läsbehörighet till informationen. Detta kräver ett medgivande. Felmeddelandet visas om medgivande inte redan är tillgängliga.

#### <a name="solution"></a>Lösning

Logga in som global administratör till Azure-portalen. För Azure Active Directory, gå till den **användarinställningar** fliken och kontrollera att **användare kan samtycker till att appar kommer åt företagsdata åt** är inställd på **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Komma igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Vad är Azure Databricks?](what-is-azure-databricks.md)

