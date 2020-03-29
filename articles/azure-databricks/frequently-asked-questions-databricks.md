---
title: 'Azure Databricks: Vanliga frågor och hjälp'
description: Få svar på vanliga frågor och felsökningsinformation om Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671565"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Vanliga frågor och svar om Azure Databricks

I den här artikeln visas de vanligaste frågorna som du kan ha relaterade till Azure Databricks. Den listar också några vanliga problem som du kan ha när du använder Databricks. Mer information finns i [Vad är Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Kan jag använda Azure Key Vault för att lagra nycklar/hemligheter som ska användas i Azure Databricks?
Ja. Du kan använda Azure Key Vault för att lagra nycklar/hemligheter som kan användas med Azure Databricks. Mer information finns i [Azure Key Vault-stödda scope](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Kan jag använda Virtuella Azure-nätverk med Databricks?
Ja. Du kan använda ett Virtuellt Azure-nätverk (VNET) med Azure Databricks. Mer information finns i [Distribuera Azure Databricks i ditt virtuella Azure-nätverk](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Hur kommer jag åt Azure Data Lake Storage från en anteckningsbok? 

Följ de här stegen:
1. Etablera ett huvudnamn för tjänsten i Azure Active Directory (Azure AD) och registrera dess nyckel.
1. Tilldela nödvändiga behörigheter till tjänstens huvudnamn i DataSjölagring.
1. Om du vill komma åt en fil i DataSjölagring använder du tjänstens huvudnamnsautentiseringsuppgifter i Notebook.

Mer information finns i [Använda Azure Data Lake Storage med Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Åtgärda vanliga problem

Här är några problem som du kan stöta på med Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problem: Den här prenumerationen är inte registrerad för att använda namnområdet "Microsoft.Databricks"

#### <a name="error-message"></a>Felmeddelande

"Den här prenumerationen är inte registrerad för att använda namnområdet 'Microsoft.Databricks'. Se https://aka.ms/rps-not-found hur du registrerar prenumerationer. (Kod: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Lösning

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Välj **Prenumerationer**, den prenumeration du använder och sedan **Resursleverantörer**. 
1. Välj **Registrera**i listan över resursleverantörer mot **Microsoft.Databricks**. Du måste ha rollen deltagare eller ägare i prenumerationen för att kunna registrera resursleverantören.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: Ditt konto {email} har inte ägar- eller deltagarrollen på Databricks-arbetsytans resurs i Azure-portalen

#### <a name="error-message"></a>Felmeddelande

"Ditt konto {email} har inte rollen Ägare eller Deltagare på Databricks-arbetsytans resurs i Azure-portalen. Det här felet kan också uppstå om du är gästanvändare i klienten. Be administratören att ge dig åtkomst eller lägga till dig som användare direkt på Databricks-arbetsytan." 

#### <a name="solution"></a>Lösning

Följande är ett par lösningar på det här problemet:

* Om du vill initiera klienten måste du vara inloggad som en vanlig användare av klienten, inte som gästanvändare. Du måste också ha en deltagarroll på databricks-arbetsytans resurs. Du kan bevilja en användare åtkomst från fliken **Åtkomstkontroll (IAM)** på databricks-arbetsytan i Azure-portalen.

* Det här felet kan också uppstå om ditt e-postdomännamn har tilldelats flera kataloger i Azure AD. Du kan lösa problemet genom att skapa en ny användare i katalogen som innehåller prenumerationen med din Databricks-arbetsyta.

    a. Gå till Azure AD i Azure-portalen. Välj **Användare och grupper** > **Lägg till en användare**.

    b. Lägg till en `@<tenant_name>.onmicrosoft.com` användare `@<your_domain>` med ett e-postmeddelande i stället för e-post. Du hittar det här alternativet i **anpassade domäner**under Azure AD i Azure-portalen.
    
    c. Ge den här nya användaren **rollen Deltagare** på databricks-arbetsytans resurs.
    
    d. Logga in på Azure-portalen med den nya användaren och hitta Databricks-arbetsytan.
    
    e. Starta Databricks arbetsytan som den här användaren.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: Ditt konto {email} har inte registrerats i Databricks 

#### <a name="solution"></a>Lösning

Om du inte skapade arbetsytan och du läggs till som användare kontaktar du den person som skapade arbetsytan. Be den personen lägga till dig med hjälp av Azure Databricks Admin Console. Instruktioner finns i [Lägga till och hantera användare](/azure/databricks/administration-guide/users-groups/users). Om du har skapat arbetsytan och fortfarande får det här felet kan du prova att välja **Initialize Workspace** igen från Azure-portalen.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problem: Starta fel i molnleverantören när klustret konfigurerades (PublicIPCountLimitReached)

#### <a name="error-message"></a>Felmeddelande

"Molnproviderns startfel: Ett molnproviderfel påträffades när klustret konfigurerades. Mer information finns i Databricks-guiden. Azure-felkod: PublicIPCountLimitReached. Azure-felmeddelande: Det går inte att skapa fler än 10 offentliga IP-adresser för den här prenumerationen i den här regionen."

#### <a name="background"></a>Bakgrund

Databricks-kluster använder en offentlig IP-adress per nod (inklusive drivrutinsnoden). Azure-prenumerationer har [offentliga IP-adressgränser](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) per region. Klusterskapande och uppskalningsåtgärder kan därför misslyckas om de skulle leda till att antalet offentliga IP-adresser som allokerats till den prenumerationen i den regionen överskrider gränsen. Den här gränsen omfattar även offentliga IP-adresser som allokerats för icke-Databricks-användning, till exempel anpassade användardefinierade virtuella datorer.

I allmänhet förbrukar kluster bara offentliga IP-adresser medan de är aktiva. Fel `PublicIPCountLimitReached` kan dock fortsätta att inträffa under en kort tidsperiod även efter att andra kluster har avslutats. Detta beror på att Databricks tillfälligt cachelagrar Azure-resurser när ett kluster avslutas. Resurscachening är avsiktligt, eftersom det avsevärt minskar svarstiden för klusterstart och automatisk skalning i många vanliga scenarier.

#### <a name="solution"></a>Lösning

Om din prenumeration redan har nått sin offentliga IP-adressgräns för en viss region bör du göra det ena eller det andra av följande.

- Skapa nya kluster på en annan Databricks-arbetsyta. Den andra arbetsytan måste finnas i en region där du inte har nått prenumerationens offentliga IP-adressgräns.
- [Begäran om att öka din offentliga IP-adressgräns](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Välj **Kvot** som **problemtyp**och **Nätverk: ARM** som **kvottyp**. I **Information**begär du en ökning av kvoten för offentlig IP-adress. Om gränsen till exempel är 60 och du vill skapa ett kluster med 100 noder begär du en gränsökning till 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problem: En andra typ av molnprovider startar fel när du ställer in klustret (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Felmeddelande

"Molnproviderns startfel: Ett molnproviderfel påträffades när klustret konfigurerades. Mer information finns i Databricks-guiden.
Azure felkod: MissingSubscriptionRegistration Azure felmeddelande: Prenumerationen är inte registrerad för att använda namnområdet "Microsoft.Compute". Se https://aka.ms/rps-not-found hur du registrerar prenumerationer."

#### <a name="solution"></a>Lösning

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Välj **Prenumerationer**, den prenumeration du använder och sedan **Resursleverantörer**. 
1. Välj **Registrera**i listan över resursleverantörer mot **Microsoft.Compute**. Du måste ha rollen deltagare eller ägare i prenumerationen för att kunna registrera resursleverantören.

Mer detaljerade instruktioner finns i [Resursleverantörer och typer](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problem: Azure Databricks behöver behörigheter för att komma åt resurser i organisationen som bara en administratör kan bevilja.

#### <a name="background"></a>Bakgrund

Azure Databricks är integrerat med Azure Active Directory. Du kan ange behörigheter i Azure Databricks (till exempel på anteckningsböcker eller kluster) genom att ange användare från Azure AD. För att Azure Databricks ska kunna lista namnen på användarna från din Azure AD krävs läsbehörighet till den informationen och samtycke till att ges. Om medgivandet inte redan är tillgängligt visas felet.

#### <a name="solution"></a>Lösning

Logga in som global administratör på Azure-portalen. För Azure Active Directory går du till fliken **Användarinställningar** och ser **till att användare kan samtycka till att appar som ansluter till företagsdata för deras räkning** är inställt på **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Vad är Azure Databricks?](what-is-azure-databricks.md)
