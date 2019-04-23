---
title: Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 43f335bdd8b918fa717d9084d3c9c30d97920c10
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004966"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster

Azure Data Lake Storage Gen2 är en molnlagringstjänst som är dedikerad för analys av stordata, bygger på Azure Blob storage. Data Lake Storage Gen2 kombinerar funktionerna i Azure Blob storage och Azure Data Lake Storage Gen1. Resulterande tjänsten erbjuder funktioner från Azure Data Lake Storage Gen1, till exempel filsystemen, katalog-nivå och filnivå säkerhet och skalbarhet, tillsammans med nivåindelad lagring med låg kostnad, hög tillgänglighet och katastrofåterställning funktioner från Azure Blob storage.

## <a name="data-lake-storage-gen2-availability"></a>Tillgänglighet för data Lake Storage Gen2

Data Lake Storage Gen2 är tillgängligt som ett lagringsalternativ för nästan alla typer av Azure HDInsight-kluster som både en standard- och ett annat lagringskonto. HBase, men kan ha endast ett Data Lake Storage Gen2-konto.

> [!Note] 
> När du har valt Data Lake Storage Gen2 som din **primär lagringstyp**, du kan inte välja ett Data Lake Storage Gen1-konto som ytterligare lagringsutrymme.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure portal

Följ dessa steg om du vill konfigurera ett Data Lake Storage Gen2-konto för att skapa ett HDInsight-kluster som använder Data Lake Storage Gen2 för lagring.

### <a name="create-a-user-managed-identity"></a>Skapa en hanterad användaridentitet

Skapa en Användartilldelad hanterad identitet, om du inte redan har ett. Se [skapa, lista, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Mer information om hur hanterade identiteter arbete i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md).

![Skapa en användartilldelad hanterad identitet](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Skapa ett Data Lake Storage Gen2-konto

Skapa ett Azure Data Lake Storage Gen2-lagringskonto. Se till att den **hierarkiskt namnområde** är aktiverat. Mer information finns i [ Snabbstart: Skapa ett lagringskonto i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Skärmbild som visar lagringskontot har skapats i Azure portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Konfigurera behörigheter för den hanterade identitet för kontot Data Lake Storage Gen2

Tilldela den hantera identitet som den **Dataägaren för Storage Blob** rollen på lagringskontot. Mer information finns i [hantera åtkomsträttigheter till Azure-Blob och kö data med RBAC (förhandsversion)](../storage/common/storage-auth-aad-rbac.md).

1. I den [Azure-portalen](https://portal.azure.com)går du till ditt lagringskonto.
1. Välj ditt lagringskonto och sedan **åtkomstkontroll (IAM)** att visa inställningarna av åtkomstkontrollen för kontot. Välj den **rolltilldelningar** flik för att se en lista över rolltilldelningar.
    
    ![Skärmbild som visar behörighetsinställningar för lagring](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Välj den **+ Lägg till rolltilldelning** för att lägga till en ny roll.
1. I den **Lägg till rolltilldelning** väljer den **Dataägaren för Storage Blob** roll. Välj den prenumeration som har det hanterade identitets- och storage-kontot. Nu ska söka efter den användartilldelade hanterade identitet som du skapade tidigare. Välj slutligen hanterad identitet och kommer att anges under **valda medlemmar**.
    
    ![Skärmbild som visar hur du tilldelar en RBAC-roll](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Välj **Spara**. Användartilldelad identitet som du har valt visas nu under den valda rollen.
1. När den här installationen är klar kan du skapa ett kluster via portalen. Klustret måste vara i samma Azure-region som lagringskontot. I den **Storage** avsnittet av menyn kluster skapas väljer du följande alternativ:
        
    * För **primär lagringstyp**väljer **Azure Data Lake Storage Gen2**.
    * Under **Välj ett lagringskonto**, Sök efter och välj Data Lake Storage Gen2 nyligen skapade lagringskontot.
        
        ![Lagringsinställningar för att använda Data Lake Storage Gen2 med Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Under **identitet**väljer du rätt prenumeration och den nyligen skapade användartilldelade hanterad identitet.
        
        ![Inställningarna för att använda Data Lake Storage Gen2 med Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Du kan lägga till ett eller flera Data Lake Storage Gen2 konton som sekundär lagring i samma kluster. Bara Upprepa stegen ovan på varje Data Lake Storage Gen2-konto som du vill lägga till med hjälp av samma hanterad identitet.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure CLI

Du kan [ladda ned en exempelfil i mallen](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) och [ladda ned en exempelfil parametrar](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Innan du använder mallen, Ersätt strängen `<SUBSCRIPTION_ID>` med ditt faktiska Azure prenumerations-ID. Dessutom måste du ersätta strängen `<PASSWORD>` med din valda lösenord för att ange både det lösenord som du använder för att logga in på ditt kluster och SSH-lösenordet.

Kodfragmentet nedan gör följande förberedelser:

1. Loggarna i ditt Azure-konto.
1. Anger aktiv prenumeration där skapa åtgärder kommer att göras.
1. Skapar en ny resursgrupp för de nya distribution aktiviteter med namnet `hdinsight-deployment-rg`.
1. Skapar en Användartilldelad hanterad identitet med namnet `test-hdinsight-msi`.
1. Lägger till ett tillägg till Azure CLI för att använda funktioner för Data Lake Storage Gen2.
1. Skapar ett nytt Data Lake Storage Gen2-konto med namnet `hdinsightadlsgen2`, med hjälp av den `--hierarchical-namespace true` flaggan.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Logga sedan in på portalen. Lägg till den nya användartilldelade hanterade identitet som den **Storage Blob Data-deltagare** -rollen på storage-konto, enligt beskrivningen i steg 3 under [med Azure portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

När du har tilldelat rollen för den hanterade Användartilldelad identitet, distribuera mallen med hjälp av följande kodavsnitt.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Åtkomstkontroll för Data Lake Storage Gen2 i HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Vilka typer av behörigheter har stöd för Data Lake Storage Gen2?

Data Lake Storage Gen2 använder en modell för åtkomstkontroll som stöder både rollbaserad åtkomstkontroll (RBAC) och POSIX-liknande åtkomstkontrollistor (ACL). Data Lake Storage Gen1 stöder åtkomstkontrollistor endast för att styra åtkomst till data.

RBAC används rolltilldelningar för att ge effektivt uppsättningar av behörigheter till användare, grupper och tjänstens huvudnamn för Azure-resurser. Normalt, dessa Azure-resurser är begränsade till översta resurser (till exempel Azure storage-konton). Azure Storage, och även Data Lake Storage Gen2 finns har den här mekanismen utökats till system-filresurs.

 Läs mer om behörigheter för filen med RBAC [Azure rollbaserad åtkomstkontroll (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Läs mer om behörigheter för filen med åtkomstkontrollistor [åtkomstkontrollistor för filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hur jag för att kontrollera åtkomsten till Mina data i Data Lake Storage Gen2?

HDInsight-klustrets möjlighet att komma åt filer i Data Lake Storage Gen2 styrs via hanterade identiteter. En hanterad identitet är en identitet som har registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvudnamn i Azure AD eller underhålla autentiseringsuppgifter, till exempel certifikat.

Azure-tjänster har två typer av hanterade identiteter: systemtilldelade och användaren tilldelas. HDInsight använder användartilldelade hanterade identiteter för att komma åt Data Lake Storage Gen2. En hanterad Användartilldelad identitet skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure.

Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till. Läs mer om hanterade identiteter, [gör hanterade identiteter för Azure-resurser arbete?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hur ställer jag in behörigheter för Azure AD-användare att fråga data i Data Lake Storage Gen2 med hjälp av Hive- eller andra tjänster?

Använda Azure AD-säkerhetsgrupper som det tilldelade huvudnamnet i ACL: er för att ställa in behörigheter för användare att köra frågor mot data. Inte direkt att tilldela behörighet till enskilda användare eller tjänstens huvudnamn. När du använder Azure AD-säkerhetsgrupper för att styra flödet av behörigheter kan du lägga till och ta bort användare eller tjänstens huvudnamn utan ACL: er på nytt tillämpa på en hel katalogstruktur. Du behöver bara lägga till eller ta bort användare från den aktuella Azure AD-säkerhetsgruppen. ACL: er överförs inte, så att återställa ACL: er kräver uppdateras ACL på varje fil och underkatalog.

## <a name="next-steps"></a>Nästa steg

* [Uppdatera Azure HDInsight-integrering med Data Lake Storage Gen2 förhandsgranskning - ACL och säkerhet](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
