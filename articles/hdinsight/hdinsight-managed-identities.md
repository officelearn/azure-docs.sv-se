---
title: Hanterade identiteter i Azure HDInsight
description: Innehåller en översikt över implementeringen av hanterade identiteter i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 02ea164a1fa29b494801623d418be73fc47d069c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077072"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Hanterade identiteter i Azure HDInsight

En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvud namn i Azure AD eller ha autentiseringsuppgifter som certifikat.

Hanterade identiteter kan användas i Azure HDInsight för att tillåta att dina kluster får åtkomst till Azure AD Domain Services, åtkomst Azure Key Vault eller åtkomst till filer i Azure Data Lake Storage Gen2.

Det finns två typer av hanterade identiteter: användare tilldelade och tilldelade system. Azure HDInsight använder användarspecifika hanterade identiteter. En användare som tilldelats en hanterad identitet skapas som en fristående Azure-resurs som du sedan kan tilldela till en eller flera Azure Service-instanser. Däremot skapas en systemtilldelad hanterad identitet i Azure AD och aktive ras sedan direkt på en viss Azure-tjänstinstans automatiskt. Livs längden för den systemtilldelade hanterade identiteten är sedan knuten till livs längden för den tjänst instans som den är aktive rad för.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight-hanterad identitets implementering

I Azure HDInsight är hanterade identiteter etablerade på varje nod i klustret. Dessa identitets komponenter kan dock endast användas av HDInsight-tjänsten. Det finns för närvarande ingen metod som stöds för att generera åtkomsttoken med hjälp av hanterade identiteter som är installerade på HDInsight-klusternoder. För vissa Azure-tjänster implementeras hanterade identiteter med en slut punkt som du kan använda för att hämta åtkomsttoken för att interagera med andra Azure-tjänster på egen hand.

## <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Hanterade identiteter kan skapas med någon av följande metoder:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De återstående stegen för att konfigurera den hanterade identiteten beror på scenariot där den ska användas.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Hanterade identitets scenarier i Azure HDInsight

Hanterade identiteter används i Azure HDInsight i flera scenarier. Se relaterade dokument för detaljerade installations-och konfigurations anvisningar:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Nästa steg

* [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
