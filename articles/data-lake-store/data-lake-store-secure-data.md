---
title: Skydda data som lagrats i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Lär dig att skydda data i Azure Data Lake Storage Gen1 med hjälp av grupper och åtkomstkontrollistor
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 92135c005779bf262c16a132b6dd6b7dace8d450
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717802"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Skydda data som lagrats i Azure Data Lake Storage Gen1
Skydda data i Azure Data Lake Storage Gen1 är en trestegsmetod.  Både rollbaserad åtkomstkontroll (RBAC) och åtkomstkontrollistor (ACL) måste anges till fullständigt Aktivera åtkomst till data för användare och säkerhetsgrupper.

1. Börja med att skapa säkerhetsgrupper i Azure Active Directory (AAD). Dessa säkerhetsgrupper används för att implementera rollbaserad åtkomstkontroll (RBAC) i Azure-portalen. Mer information finns i [rollbaserad åtkomstkontroll i Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Tilldela kontot Azure Data Lake Storage Gen1 Add-säkerhetsgrupper. Detta styr åtkomst till Data Lake Storage Gen1-konto från portalen och hanteringsåtgärder från portalen eller API: er.
3. Tilldela Add-säkerhetsgrupper som åtkomst åtkomstkontrollistor (ACL) på Data Lake Store-filsystemet.
4. Dessutom kan du också ange ett IP-adressintervall för klienter som har åtkomst till data i Data Lake Store.

Den här artikeln innehåller instruktioner om hur du använder Azure-portalen för att utföra uppgifter för ovan. Detaljerad information om hur Data Lake Store implementerar säkerhet på konto- och data finns i [säkerhet i Azure Data Lake Store](data-lake-store-security-overview.md). Djupgående information om hur ACL: er implementeras i Azure Data Lake Store, finns i [översikt över åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Skapa säkerhetsgrupper i Azure Active Directory
Anvisningar för hur du skapar AAD-säkerhetsgrupper och lägga till användare i gruppen finns i [hantera säkerhetsgrupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Du kan lägga till både användare och andra grupper i en grupp i Azure AD med Azure portal. Men för att lägga till ett huvudnamn för tjänsten till en grupp, använda [Azure AD PowerShell-modulen](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Tilldela användare eller säkerhetsgrupper till Azure Data Lake Store-konton
När du tilldelar användare eller säkerhetsgrupper till Azure Data Lake Store-konton kan styra du åtkomsten till hanteringsåtgärder på det konto som använder Azure-portalen och Azure Resource Manager API: er. 

1. Öppna ett Azure Data Lake Store-konto. I den vänstra rutan klickar du på **alla resurser**, och klicka sedan på namnet på kontot som du vill tilldela en grupp med användare eller säkerhetsgrupp från bladet alla resurser.

2. I ditt Data Lake Store-kontoblad klickar du på **åtkomstkontroll (IAM)**. Bladet som standard visas prenumerationsägarna som ägare.
   
    ![Tilldela säkerhetsgrupp till Azure Data Lake Storage Gen1 konto](./media/data-lake-store-secure-data/adl.select.user.icon.png "tilldela säkerhetsgrupp till Azure Data Lake Storage Gen1 konto")

3. I den **åtkomstkontroll (IAM)** bladet klickar du på **Lägg till** att öppna den **Lägg till behörigheter** bladet. I den **Lägg till behörigheter** bladet och välja en **rollen** för användare/grupp. Leta efter den säkerhetsgrupp du skapade tidigare i Azure Active Directory och markera den. Om du har massor av användare och grupper för att söka från kan använda den **Välj** textrutan för att filtrera på gruppnamnet. 
   
    ![Lägg till en roll för användaren](./media/data-lake-store-secure-data/adl.add.user.1.png "lägga till en roll för användaren")
   
    Den **ägare** och **deltagare** rollen ger åtkomst till en mängd funktioner för administration på data lake-konto. För användare som kommer att interagera med data i data lake men fortfarande behovet av att visa information om kontohantering, kan du lägga till dem i den **läsare** roll. Omfattningen av dessa roller är begränsad till vilka hanteringsåtgärder som rör Azure Data Lake Storage Gen1-konto.
   
    För data definierar enskilda filsystemsbehörigheter vad användarna kan göra. Därför kan en användare med läsarrollen bara visa administrativa inställningar som är associerade med kontot men kan potentiellt läsa och skriva data baserat på filsystembehörigheter tilldelad. Data Lake Store-filsystembehörigheter beskrivs på [tilldela säkerhetsgrupp som ACL: er till filsystemet Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Endast den **ägare** rollen aktiveras automatiskt åtkomst tll filsystem. Den **deltagare**, **läsare**, och alla andra roller kräver ACL: er att aktivera alla åtkomstnivå till mappar och filer.  Den **ägare** rollen ger superanvändare fil- och mappbehörigheter som inte kan åsidosättas via ACL: er. Mer information om hur RBAC-principer mappas till dataåtkomst finns i [RBAC för kontohantering](data-lake-store-security-overview.md#rbac-for-account-management).

4. Om du vill lägga till en grupp/användare som inte finns med i den **Lägg till behörigheter** bladet, du kan bjuda in dem genom att skriva sina e-postadress i den **Välj** textrutan och sedan markera dem i listan.
   
    ![Lägg till en säkerhetsgrupp](./media/data-lake-store-secure-data/adl.add.user.2.png "Lägg till en säkerhetsgrupp")
   
5. Klicka på **Spara**. Du bör se gruppen har lagts till som visas nedan.
   
    ![Säkerhetsgruppen har lagts till](./media/data-lake-store-secure-data/adl.add.user.3.png "säkerhetsgrupp som har lagts till")

6. Användare/säkerhetsgrupp har nu åtkomst till Azure Data Lake Storage Gen1-konto. Om du vill ge åtkomst till specifika användare, kan du lägga till dem i gruppen. På samma sätt, om du vill återkalla åtkomst för en användare, du kan ta bort dem från gruppen. Du kan också tilldela flera säkerhetsgrupper till ett konto. 

## <a name="filepermissions"></a>Tilldela användare eller säkerhetsgrupper som ACL: er till filsystemet Azure Data Lake Storage Gen1
Genom att tilldela användare/säkerhetsgrupper till filsystemet Azure Data Lake kan ange du åtkomstkontroll på data som lagras i Azure Data Lake Store.

1. I ditt Data Lake Storage Gen1-kontoblad klickar du på **Datautforskaren**.
   
    ![Visa data via Datautforskaren](./media/data-lake-store-secure-data/adl.start.data.explorer.png "visa data via Data Explorer")
2. I den **Datautforskaren** bladet klickar du på mappen som du vill konfigurera ACL: och klicka sedan på **åtkomst**. Om du vill tilldela ACL: er till en fil, måste du först klicka på filen för att förhandsgranska den och klicka sedan på **åtkomst** från den **Filförhandsgranskning** bladet.
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er i Data Lake-filsystemet")
3. Den **åtkomst** bladet visar en lista över ägare och behörigheterna som redan har tilldelats till roten. Klicka på den **Lägg till** ikon för att lägga till ytterligare åtkomst-ACL: er.
    > [!IMPORTANT]
    > Ange behörigheter för en enskild fil beviljar nödvändigtvis en användare/grupp åtkomst till filen. Sökvägen till filen måste vara tillgänglig för tilldelade användare/grupp. Mer information och exempel finns i [vanliga scenarier som rör behörigheter](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista över standardentiteter och anpassade åtkomst](./media/data-lake-store-secure-data/adl.acl.2.png "lista standardentiteter och anpassade åtkomst")
   
   * Den **ägare** och **alla andra** ger åtkomst till UNIX-format, där du anger läsa, skriva, köra (rwx) till tre olika användarklasser: ägare och gruppen.
   * **Tilldelade behörigheter** motsvarar POSIX ACL: er som gör det möjligt att ange behörigheter för specifika namngivna användare eller grupper utöver filens ägare eller grupp. 
     
     Mer information finns i [HDFS ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Mer information om hur ACL: er implementeras i Data Lake Storage Gen1 finns [åtkomstkontroll i Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Klicka på den **Lägg till** ikonen för att öppna den **tilldela behörigheter** bladet. I det här bladet, klickar du på **Välj användare eller grupp**, och sedan i **Välj användare eller grupp** bladet letar du reda på säkerhetsgruppen som du skapade tidigare i Azure Active Directory. Om du har många grupper att söka från, använder du textrutan längst upp för att filtrera efter gruppnamnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Välj**.
   
    ![Lägga till en grupp](./media/data-lake-store-secure-data/adl.acl.3.png "lägga till en grupp")
5. Klicka på **Välj behörigheter**väljer behörigheterna, oavsett om behörigheterna som ska tillämpas rekursivt och om du vill tilldela behörigheterna som en åtkomst-ACL, standard-ACL, eller båda. Klicka på **OK**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.acl.4.png "tilldela behörigheter till gruppen")
   
    Läs mer om behörigheter i Data Lake Storage Gen1 och standard/åtkomst-ACL: er, [åtkomstkontroll i Data Lake Storage Gen1](data-lake-store-access-control.md).
6. När du klickar på **Ok** i den **Välj behörigheter** bladet, nyligen tillagda grupp och associerade behörigheter visas nu i den **åtkomst** bladet.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.acl.5.png "tilldela behörigheter till gruppen")
   
   > [!IMPORTANT]
   > I den aktuella versionen kan du ha upp till 28 indata under **behörigheterna**. Om du vill lägga till fler än 28 användare, bör du skapa säkerhetsgrupper, lägga till användare till säkerhetsgrupper, lägga till ge åtkomst till de säkerhetsgrupperna för Data Lake Storage Gen1-konto.
   > 
   > 
7. Om det behövs kan ändra du också åtkomstbehörigheter när du har lagt till i gruppen. Avmarkera eller markera kryssrutan för varje behörighetstyp (läsa, skriva, köra) baserat på om du vill ta bort eller tilldela behörighet till gruppen. Klicka på **spara** att spara ändringarna, eller **Ignorera** att ångra ändringarna.

## <a name="set-ip-address-range-for-data-access"></a>Ange IP-adressintervall för dataåtkomst
Azure Data Lake Storage Gen1 kan du ytterligare låsa åtkomsten till ditt datalager på nätverksnivå. Du kan aktivera brandväggen, ange en IP-adress eller definiera ett IP-adressintervall för dina betrodda klienter. När aktiverat kan kan endast klienter som har IP-adresser inom definierade intervallet ansluta till arkivet.

![Brandväggsinställningar och IP-åtkomst till](./media/data-lake-store-secure-data/firewall-ip-access.png "brandväggen inställningar och IP-adress")

## <a name="remove-security-groups-for-an-azure-data-lake-storage-gen1-account"></a>Ta bort säkerhetsgrupper för ett konto med Azure Data Lake Storage Gen1
När du tar bort säkerhetsgrupper från Azure Data Lake Storage Gen1 konton kan ändrar du endast åtkomst till hanteringsåtgärder på det konto som använder Azure Portal och Azure Resource Manager API: er.  

Åtkomst till data ändras och fortfarande hanteras av åtkomst-ACL: er.  Undantaget till detta är användare/grupper i rollen ägare.  Användare/grupper tas bort från rollen ägare är inte längre superanvändare och deras åtkomst faller tillbaka till inställningar för åtkomst-ACL. 

1. I ditt Data Lake Storage Gen1-kontoblad klickar du på **åtkomstkontroll (IAM)**. 
   
    ![Tilldela säkerhetsgrupp till Azure Data Lake-konto](./media/data-lake-store-secure-data/adl.select.user.icon.png "tilldela säkerhetsgrupp till Azure Data Lake-konto")
2. I den **åtkomstkontroll (IAM)** bladet klickar du på security-grupper som du vill ta bort. Klicka på **ta bort**.
   
    ![Säkerhetsgrupp bort](./media/data-lake-store-secure-data/adl.remove.group.png "säkerhetsgrupp som har tagits bort")

## <a name="remove-security-group-acls-from-azure-data-lake-storage-gen1-file-system"></a>Ta bort säkerhetsgruppen ACL: er från Azure Data Lake Storage Gen1 filsystem
När du tar bort säkerhetsgrupp ACL: er från Azure Data Lake Store-filsystem kan ändra du åtkomst till data i Data Lake Storage Gen1.

1. I ditt Data Lake Storage Gen1-kontoblad klickar du på **Datautforskaren**.
   
    ![Skapa kataloger i Data Lake-konto](./media/data-lake-store-secure-data/adl.start.data.explorer.png "skapa kataloger i Data Lake-konto")
2. I den **Datautforskaren** bladet klickar du på mappen som du vill ta bort ACL och klicka sedan på **åtkomst**. Om du vill ta bort ACL: er för en fil, måste du först klicka på filen för att förhandsgranska den och klicka sedan på **åtkomst** från den **Filförhandsgranskning** bladet. 
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er i Data Lake-filsystemet")
3. I den **åtkomst** bladet klickar du på gruppen du vill ta bort. I den **komma åt information** bladet klickar du på **ta bort**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.remove.acl.png "tilldela behörigheter till gruppen")

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopiera data från Azure Storage BLOB till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Komma igång med Data Lake Storage Gen1 med hjälp av PowerShell](data-lake-store-get-started-powershell.md)
* [Komma igång med Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Åtkomst till diagnostikloggar för Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)

