---
title: Hanterade identiteter i Azure HDInsight
description: Innehåller en översikt av hur hanterade identiteter i Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9386bcb8e455bff5ceed1fccdf55874caf7b6507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175810"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Hanterade identiteter i Azure HDInsight

En hanterad identitet är en identitet som har registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvudnamn i Azure AD eller underhålla autentiseringsuppgifter, till exempel certifikat.

Hanterade identiteter kan användas i Azure HDInsight för att tillåta ditt kluster åtkomst till Azure AD domain services, få åtkomst till Azure Key Vault eller komma åt filer i Azure Data Lake Storage Gen2.

Det finns två typer av hanterade identiteter: användartilldelade och systemtilldelade. Azure HDInsight använder användartilldelade hanterade identiteter. En hanterad Användartilldelad identitet skapas som en fristående Azure-resurs som du kan sedan tilldela till en eller flera Azure-tjänstinstanser. Däremot har en hanterad identitet med systemtilldelade skapas i Azure AD och aktiveras direkt på en viss Azure-instans automatiskt. Det systemtilldelade hanterad identitet livslängd kopplas sedan till den tjänstinstans som den är aktiverad på livslängd.

## <a name="hdinsight-managed-identity-implementation"></a>Implementering av HDInsight managed

I Azure HDInsight etableras hanterade identiteter på varje nod i klustret. Dessa identitetskomponenter är dock endast användas av HDInsight-tjänsten. Det finns för närvarande ingen stöds metod att generera åtkomsttoken med hjälp av de hanterade identiteter som är installerade på HDInsight-klusternoder. För vissa Azure-tjänster implementeras hanterade identiteter med en slutpunkt som du kan använda för att hämta åtkomsttoken för att interagera med andra Azure-tjänster på egen hand.

## <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Hanterade identiteter kan skapas med någon av följande metoder:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De återstående stegen för att konfigurera den hanterade identitet beror på scenariot där den ska användas.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenarier för hanterad identitet i Azure HDInsight

Hanterade identiteter som används i Azure HDInsight i flera scenarier. Se relaterade dokument för detaljerad konfiguration och instruktioner för konfiguration:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Nästa steg

* [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)