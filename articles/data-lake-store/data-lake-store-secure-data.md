---
title: Att säkra data som lagras i Azure Data Lake Store | Microsoft Docs
description: Lär dig att skydda data i Azure Data Lake Store med hjälp av grupper och åtkomstkontrollistor
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
ms.openlocfilehash: 5b83f02c55d0aa7b2e122d7fc8c9ef5734cdd924
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197043"
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Att säkra data som lagras i Azure Data Lake Store
Skydda data i Azure Data Lake Store är en metod för tre steg.  Både rollbaserad åtkomstkontroll (RBAC) och åtkomstkontrollistor (ACL) måste anges till fullständigt Aktivera åtkomst till data för användare och säkerhetsgrupper.

1. Börja med att skapa säkerhetsgrupper i Azure Active Directory (AAD). Dessa säkerhetsgrupper används för att implementera rollbaserad åtkomstkontroll (RBAC) i Azure-portalen. Mer information finns i [rollbaserad åtkomstkontroll i Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Tilldela Azure Data Lake Store-konto Add-säkerhetsgrupper. Detta styr åtkomsten till Data Lake Store-konto från portalen och hantering av åtgärder från portalen eller API: er.
3. Tilldela Add-säkerhetsgrupper som åtkomst Kontrollistor (ACL) på Data Lake Store-filsystem.
4. Dessutom kan du också ange ett IP-adressintervall för klienter som kan komma åt data i Data Lake Store.

Den här artikeln innehåller instruktioner om hur du använder Azure-portalen för att utföra uppgifterna som ovan. Detaljerad information om hur Data Lake Store implementerar säkerhet på kontot och data finns [säkerhet i Azure Data Lake Store](data-lake-store-security-overview.md). Djupgående information om hur ACL: er implementeras i Azure Data Lake Store, se [översikt över åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Skapa säkerhetsgrupper i Azure Active Directory
Instruktioner om hur du skapar AAD-säkerhetsgrupper och lägga till användare i gruppen finns [hantera säkerhetsgrupper i Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Du kan lägga till både användare och andra grupper i en grupp i Azure AD med Azure-portalen. Men för att lägga till ett huvudnamn för tjänsten i en grupp, använda [Azure AD PowerShell-modulen](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Tilldela användare eller säkerhetsgrupper för Azure Data Lake Store-konton
När du tilldelar användarna eller säkerhetsgrupperna till Azure Data Lake Store-konton kan styra du åtkomsten till hanteringsåtgärder på det konto som använder Azure-portalen och Azure Resource Manager API: er. 

1. Öppna ett Azure Data Lake Store-konto. I den vänstra rutan klickar du på **alla resurser**, och klicka sedan på namnet på kontot som du vill tilldela en grupp med användare eller säkerhetsgrupp från bladet alla resurser.

2. I ditt Data Lake Store-kontoblad klickar du på **Access Control (IAM)**. Bladet som standard innehåller prenumerationen ägare som ägare.
   
    ![Tilldela Azure Data Lake Store-konto säkerhetsgrupp](./media/data-lake-store-secure-data/adl.select.user.icon.png "tilldela säkerhetsgrupp till Azure Data Lake Store-konto")

3. I den **Access Control (IAM)** bladet, klickar du på **Lägg till** att öppna den **lägga till behörigheter** bladet. I den **lägga till behörigheter** bladet väljer en **rollen** för användaren eller gruppen. Leta efter den säkerhetsgrupp du skapade tidigare i Azure Active Directory och markera den. Om du har många användare och grupper för att söka i, Använd den **Välj** textruta för att filtrera på gruppnamnet. 
   
    ![Lägga till en roll för användaren](./media/data-lake-store-secure-data/adl.add.user.1.png "lägga till en roll för användaren")
   
    Den **ägare** och **deltagare** rollen ger åtkomst till en mängd funktioner för administration på data lake-konto. Om en användare kommer att interagera med data i data lake men fortfarande behöver visa kontoinformation för hantering, kan du lägga till dem i den **Reader** roll. Omfattningen av dessa roller är begränsad till hanteringsåtgärder som är relaterade till Azure Data Lake Store-konto.
   
    För dataåtgärder definiera enskilda behörigheter vad användarna kan göra. Därför kan en användare med rollen Läsare bara visa administrativa inställningar som är associerat med kontot men kan potentiellt läsa och skriva data baserat på filsystembehörigheter tilldelad. Data Lake Store-filsystembehörigheterna beskrivs på [tilldela säkerhetsgrupp som ACL: er till Azure Data Lake Store-filsystem](#filepermissions).

    > [!IMPORTANT]
    > Endast den **ägare** rollen aktiveras automatiskt åtkomst tll filsystem. Den **deltagare**, **Reader**, och alla andra roller kräver ACL: er att aktivera alla åtkomstnivå till mappar och filer.  Den **ägare** rollen ger en Användarfil- och mappbehörigheter som inte kan åsidosättas via ACL: er. Mer information om hur RBAC principer mappas till dataåtkomst finns [RBAC för kontohantering](data-lake-store-security-overview.md#rbac-for-account-management).

4. Om du vill lägga till en grupp/användare som inte finns med i den **lägga till behörigheter** bladet kan du be dem genom att skriva sina e-postadress i den **Välj** textruta och välja dem från listan.
   
    ![Lägg till en säkerhetsgrupp](./media/data-lake-store-secure-data/adl.add.user.2.png "lägger till en säkerhetsgrupp")
   
5. Klicka på **Spara**. Du bör se säkerhetsgruppen läggas till som visas nedan.
   
    ![Lägga till säkerhetsgruppen](./media/data-lake-store-secure-data/adl.add.user.3.png "säkerhetsgrupp som lagts till")

6. Användaren/säkerhetsgrupp har nu tillgång till Azure Data Lake Store-konto. Om du vill ge åtkomst till specifika användare, kan du lägga till dem i säkerhetsgruppen. På liknande sätt kan du vill återkalla åtkomst för en användare kan du ta bort dem från säkerhetsgruppen. Du kan också tilldela flera säkerhetsgrupper till ett konto. 

## <a name="filepermissions"></a>Tilldela användarna eller säkerhetsgrupperna som ACL: er till Azure Data Lake Store-filsystem
Genom att tilldela användaren/säkerhetsgrupper till filsystemet Azure Data Lake kan ange du åtkomstkontroll på data som lagras i Azure Data Lake Store.

1. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Visa data via Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "visa data via Data Explorer")
2. I den **Data Explorer** bladet, klickar du på mappen som du vill konfigurera ACL och klicka sedan på **åtkomst**. Om du vill tilldela ACL: er till en fil, måste du först klicka på filen för att förhandsgranska den och klicka sedan på **åtkomst** från den **Filförhandsgranskning** bladet.
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er i Data Lake-filsystem")
3. Den **åtkomst** bladet visar ägare och behörigheterna som redan har tilldelats till roten. Klicka på den **Lägg till** ikon för att lägga till ytterligare åtkomst ACL: er.
    > [!IMPORTANT]
    > Ange behörigheter för en enskild fil inte nödvändigtvis bevilja en användare/grupp åtkomst till filen. Sökvägen till filen måste vara tillgänglig för tilldelade användargruppen. Mer information och exempel finns i [vanliga scenarier som är relaterade till behörigheter](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Visa en lista över standard och anpassad åtkomst](./media/data-lake-store-secure-data/adl.acl.2.png "listan standard och anpassad åtkomst")
   
   * Den **ägare** och **alla andra** ge åtkomst för UNIX-format, anger du läsa, skriva, köra (rwx) till tre olika klasser: ägare och gruppen.
   * **Behörigheterna** motsvarar POSIX ACL: er som gör det möjligt att ange behörighet för specifika namngivna användare eller grupper utöver filens ägare eller grupp. 
     
     Mer information finns i [HDFS ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Mer information om hur ACL: er implementeras i Data Lake Store finns [åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).
4. Klicka på den **Lägg till** ikon för att öppna den **tilldela behörigheter** bladet. I det här bladet, klickar du på **Välj användare eller grupp**, och klicka sedan på **Välj användare eller grupp** bladet, leta efter den säkerhetsgrupp du skapade tidigare i Azure Active Directory. Om du har många grupper att söka i, använder du textrutan längst upp för att filtrera efter gruppnamnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Välj**.
   
    ![Lägga till en grupp](./media/data-lake-store-secure-data/adl.acl.3.png "lägga till en grupp")
5. Klicka på **Välj behörigheter**väljer behörigheterna, om behörigheterna som ska tillämpas rekursivt och om du vill tilldela behörigheterna som en access ACL, standard ACL eller båda. Klicka på **OK**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.acl.4.png "tilldela behörigheter till gruppen")
   
    Mer information om behörigheter i Data Lake Store och standard-/ Access ACL: er finns [åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).
6. När du klickar på **Ok** i den **Välj behörigheter** bladet ny grupp och associerade behörigheter visas nu i den **åtkomst** bladet.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.acl.5.png "tilldela behörigheter till gruppen")
   
   > [!IMPORTANT]
   > I den aktuella versionen kan du kan ha upp till 28 poster under **behörigheterna**. Om du vill lägga till fler än 28 användare kan du skapa säkerhetsgrupper, lägga till användare i säkerhetsgrupper, lägga till ge åtkomst till de säkerhetsgrupperna för Data Lake Store-konto.
   > 
   > 
7. Om det behövs kan du också ändra behörigheter för åtkomst när du har lagt till gruppen. Avmarkera eller markera kryssrutan för varje behörighetstyp (läsa, skriva, köra) baserat på om du vill ta bort eller tilldela behörigheten till säkerhetsgruppen. Klicka på **spara** att spara ändringarna, eller **Ignorera** ångra ändringarna.

## <a name="set-ip-address-range-for-data-access"></a>Ange IP-adressintervall för dataåtkomst
Azure Data Lake Store kan du ytterligare Lås åtkomst till dina data store på nätverksnivån. Du kan aktivera brandväggen, ange en IP-adress eller definiera ett intervall med IP-adresser för dina betrodda klienter. När du har aktiverat, kan endast klienter som har IP-adresser inom definierade intervallet ansluta till arkivet.

![Brandväggsinställningar och IP-åtkomst till](./media/data-lake-store-secure-data/firewall-ip-access.png "inställningar och IP-adress för brandvägg")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Ta bort säkerhetsgrupper för ett Azure Data Lake Store-konto
När du tar bort säkerhetsgrupper från Azure Data Lake Store-konton kan ändrar du bara åtkomst till hanteringsåtgärder på det konto som använder Azure-portalen och Azure Resource Manager API: er.  

Åtkomst till data är oförändrade och fortfarande hanteras av åtkomst ACL: er.  Undantaget till detta är användare eller grupper i rollen ägare.  Användare eller grupper som har tagits bort från rollen ägare är inte längre superanvändare och deras åtkomst faller tillbaka till ACL åtkomstinställningar. 

1. I ditt Data Lake Store-kontoblad klickar du på **Access Control (IAM)**. 
   
    ![Tilldela Azure Data Lake-konto säkerhetsgrupp](./media/data-lake-store-secure-data/adl.select.user.icon.png "tilldela säkerhetsgrupp till Azure Data Lake-konto")
2. I den **Access Control (IAM)** bladet, klickar du på säkerhet eller grupperna som du vill ta bort. Klicka på **ta bort**.
   
    ![Säkerhetsgrupp bort](./media/data-lake-store-secure-data/adl.remove.group.png "säkerhetsgrupp tas bort")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Ta bort säkerhetsgruppen ACL: er från Azure Data Lake Store-filsystem
När du tar bort säkerhetsgrupp ACL: er från Azure Data Lake Store-filsystem, ändrar du åtkomst till data i Data Lake Store.

1. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Skapa kataloger i Data Lake-konto](./media/data-lake-store-secure-data/adl.start.data.explorer.png "skapa kataloger i Data Lake-konto")
2. I den **Data Explorer** bladet, klickar du på mappen som du vill ta bort ACL och klicka sedan på **åtkomst**. Om du vill ta bort ACL: er för en fil, måste du först klicka på filen för att förhandsgranska den och klicka sedan på **åtkomst** från den **Filförhandsgranskning** bladet. 
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er i Data Lake-filsystem")
3. I den **åtkomst** bladet på säkerhetsgruppen som du vill ta bort. I den **komma åt information** bladet, klickar du på **ta bort**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.remove.acl.png "tilldela behörigheter till gruppen")

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
* [Kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Kom igång med Data Lake Store med hjälp av PowerShell](data-lake-store-get-started-powershell.md)
* [Kom igång med Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Åtkomst till diagnostikloggar för Data Lake Store](data-lake-store-diagnostic-logs.md)

