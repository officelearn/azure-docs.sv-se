---
title: Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster
description: Lär dig mer om att skapa med Azure Data Lake Storage Gen2 med Azure HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 9a1d0775c12d424c35e9e9d366f69e07ec9b1468
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096984"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster

Azure Data Lake Storage (Data Lake Storage) Gen2 är en uppsättning funktioner för analys av stordata, bygger på Azure Blob storage. Data Lake Storage Gen2 är resultatet av en kombination av funktionerna i Azure Blob storage och Azure Data Lake Storage Gen1. Resultatet är en tjänst som erbjuder funktioner från Azure Data Lake Storage Gen1 som filsystemen, katalog, och nivån filsäkerhet och skala tillsammans med låg kostnad, nivåindelad lagring, hög tillgänglighet/haveriberedskap från Azure Blob lagring.

## <a name="data-lake-storage-gen2-availability"></a>Tillgänglighet för data Lake Storage Gen2

Azure Data Lake Storage Gen2 är tillgängligt som ett lagringsalternativ för nästan alla typer av Azure HDInsight-kluster som både en standard- och ett annat lagringskonto. HBase, men kan bara ha ett Data Lake Storage Gen2-konto.

> [!Note] 
> När du har valt Data Lake Storage Gen2 som din **primär lagringstyp**, du kan inte välja ett Data Lake Storage Gen1-konto som ytterligare lagringsutrymme.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Skapar ett HDInsight-kluster med Data Lake Storage Gen2

Om du vill skapa ett HDInsight-kluster som använder Data Lake Storage Gen2 för lagring, Använd följande steg för att skapa ett Data Lake Storage Gen2-konto som har konfigurerats korrekt.

1. Skapa en Användartilldelad hanterad identitet, om du inte redan har ett. Se [skapa, lista, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

    ![Skapa en användartilldelad hanterad identitet](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Skapa ett Azure Data Lake Storage Gen2 storage-konto. Se till att den **hierarkiskt filsystem** är aktiverat. Gå till [Snabbstart: Skapa ett lagringskonto i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md) för mer information.

    ![Skärmbild som visar lagringskontot har skapats i Azure portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. Tilldela den hantera identitet som den **Storage Blob Data-deltagare (förhandsgranskning)** rollen på lagringskontot. Se [hantera åtkomsträttigheter till Azure-Blob och kö data med RBAC (förhandsversion)](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)

    1. I den [Azure-portalen](https://portal.azure.com), navigera till ditt lagringskonto.
    1. Välj ditt lagringskonto och sedan **åtkomstkontroll (IAM)** att visa inställningar för åtkomstkontroll för kontot. Välj den **rolltilldelningar** flik för att se en lista över rolltilldelningar.
    
        ![Skärmbild som visar behörighetsinställningar för lagring](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Klicka på den **Lägg till rolltilldelning** för att lägga till en ny roll.
    1. I den **Lägg till rolltilldelning** väljer den **Storage Blob Data-deltagare (förhandsgranskning)** roll. Välj den prenumeration som har det hanterade identitets- och storage-kontot. Nu ska söka efter den användartilldelade hanterade identitet som du skapade tidigare. Välj slutligen hanterad identitet och kommer att anges under **valda medlemmar**.
    
        ![Skärmbild som visar hur du tilldelar en RBAC-roll](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Klicka på **Spara**. Användartilldelad identitet som du har valt visas nu den **deltagare** roll.

    1. När den här installationen är klar kan du skapa ett kluster via portalen. Klustret måste vara i samma Azure-region som lagringskontot. I den **Storage** avsnittet av menyn kluster skapas väljer du följande alternativ:
        
        * För **primär lagringstyp**, klickar du på **Azure Data Lake Storage Gen2**.
        * Under **Välj ett Lagringskonto**, Sök efter och välj Data Lake Storage Gen2 nyligen skapade lagringskontot.
        
            ![Lagringsinställningar för att använda Data Lake Storage Gen2 med Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * Under **identitet** Välj rätt prenumeration och den nyligen skapade användartilldelade hanterad identitet.
        
            ![Inställningarna för att använda Data Lake Storage Gen2 med Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Åtkomstkontroll för Data Lake Storage Gen2 i HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Vilka typer av behörigheter har stöd för Data Lake Storage Gen2?

Azure Data Lake Storage Gen2 implementerar en modell för åtkomstkontroll som stöder både Azure rollbaserad åtkomstkontroll (RBAC) och POSIX-liknande åtkomstkontrollistor (ACL). Data Lake åtkomstkontrollistor Storage Gen1 stöds endast för att styra åtkomst till data.

Azure rollbaserad åtkomstkontroll (RBAC) använder rolltilldelningar för att effektivt gäller uppsättningar av behörigheter för användare, grupper och tjänstens huvudnamn för Azure-resurser. Normalt, dessa Azure-resurser är begränsade till översta resurser (till exempel Azure Storage-konton). Azure Storage, och även Azure Data Lake Storage Gen2 finns har den här mekanismen utökats till system-filresurs.

 Läs mer på filbehörigheter med RBAC [Azure rollbaserad åtkomstkontroll (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Mer information om behörigheter med ACL: er finns i [åtkomstkontrollistor för filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Hur jag för att kontrollera åtkomsten till Mina data i Gen2?

Möjlighet för ditt HDInsight-kluster att få åtkomst till filer i Data Lake Storage Gen2 styrs via hanterade identiteter. En hanterad identitet är en identitet som registrerad i Azure AD vars autentiseringsuppgifter hanteras av Azure. Du behöver inte registrera tjänstens huvudnamn i Azure AD och upprätthålla autentiseringsuppgifter, till exempel certifikat.

Det finns två typer av hanterade identiteter för Azure-tjänster: systemtilldelade och användaren tilldelas. Azure HDInsight använder användartilldelade hanterade identiteter för att komma åt Azure Data Lake Storage Gen2. En hanterad Användartilldelad identitet skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure. Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till. Mer information om hanterade identiteter finns [hur fungerar de hanterade identiteterna för Azure-resurser arbete](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Hur ställer jag in behörigheter för Azure AD-användare att fråga data i Data Lake Storage Gen2 med Hive eller andra tjänster?

Använda Azure AD-säkerhetsgrupper som det tilldelade huvudnamnet i ACL: er. Inte direkt tilldela enskilda användare eller tjänstens huvudnamn med åtkomstbehörighet för filen. När du använder AD-säkerhetsgrupper för att styra flödet av behörigheter kan du lägga till och ta bort användare eller tjänstens huvudnamn utan att återställa ACL: er till en hel katalogstruktur. Du behöver bara lägga till eller ta bort användare från den aktuella Azure AD-säkerhetsgruppen. Ärvs inte ACL: er och ACL: er på nytt tillämpa så behöver uppdateras ACL på varje fil och underkatalog.

## <a name="next-steps"></a>Nästa steg

* [Använda Azure Data Lake Storage Gen2 förhandsversion med Azure HDInsight-kluster](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Uppdatera Azure HDInsight-integrering med Data Lake Storage Gen2 förhandsgranskning - ACL och säkerhet](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introduktion till Azure Data Lake Storage Gen2 – förhandsversion](../storage/blobs/data-lake-storage-introduction.md)
