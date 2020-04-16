---
title: Hanterade identiteter i Azure HDInsight
description: Ger en översikt över implementeringen av hanterade identiteter i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408944"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Hanterade identiteter i Azure HDInsight

En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänsthuvudnamn i Azure AD. Eller underhåll autentiseringsuppgifter som certifikat.

Hanterade identiteter används i Azure HDInsight för att komma åt Azure AD-domäntjänster eller komma åt filer i Azure Data Lake Storage Gen2 när det behövs.

Det finns två typer av hanterade identiteter: användartilldelade och systemtilldelade. Azure HDInsight stöder endast användartilldelade hanterade identiteter. HDInsight stöder inte systemtilldelade hanterade identiteter. En användartilldelad hanterad identitet skapas som en fristående Azure-resurs, som du sedan kan tilldela till en eller flera Azure-tjänstinstanser. Däremot skapas en systemtilldelad hanterad identitet i Azure AD och aktiveras sedan direkt på en viss Azure-tjänstinstans automatiskt. Livslängden för den systemtilldelade hanterade identiteten är sedan knuten till livslängden för den tjänstinstans som den är aktiverad på.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight-implementering av hanterade identiteter

I Azure HDInsight etableras hanterade identiteter på varje nod i klustret. Dessa identitetskomponenter kan dock endast kan kanserbara av HDInsight-tjänsten. Det finns för närvarande ingen metod som stöds för att generera åtkomsttoken med hjälp av hanterade identiteter som är installerade på HDInsight-klusternoder. För vissa Azure-tjänster implementeras hanterade identiteter med en slutpunkt som du kan använda för att hämta åtkomsttoken. Använd token för att interagera med andra Azure-tjänster på egen hand.

## <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Hanterade identiteter kan skapas med någon av följande metoder:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De återstående stegen för att konfigurera den hanterade identiteten beror på scenariot där den ska användas.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Hanterade identitetsscenarier i Azure HDInsight

Hanterade identiteter används i Azure HDInsight i flera scenarier. Se relaterade dokument för detaljerade installations- och konfigurationsinstruktioner:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kundhanterad nyckeldiskkryptering](disk-encryption.md)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Vad händer om jag tar bort den hanterade identiteten när klustret har skapats?

Klustret stöter på problem när den hanterade identiteten behövs. Det finns för närvarande inget sätt att uppdatera eller ändra en hanterad identitet när klustret har skapats. Så vår rekommendation är att se till att den hanterade identiteten inte tas bort under klusterkörningen. Du kan också återskapa klustret och tilldela en ny hanterad identitet.

## <a name="next-steps"></a>Nästa steg

* [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
