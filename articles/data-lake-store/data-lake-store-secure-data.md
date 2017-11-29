---
title: "Att säkra data som lagras i Azure Data Lake Store | Microsoft Docs"
description: "Lär dig att skydda data i Azure Data Lake Store med hjälp av grupper och åtkomstkontrollistor"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e246abaf8d1d7939765e1c878f6dfaf6375294f6
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Att säkra data som lagras i Azure Data Lake Store
Skydda data i Azure Data Lake Store är en metod för tre steg.

1. Börja med att skapa säkerhetsgrupper i Azure Active Directory (AAD). Dessa säkerhetsgrupper används för att implementera rollbaserad åtkomstkontroll (RBAC) i Azure-portalen. Mer information finns i [rollbaserad åtkomstkontroll i Microsoft Azure](../active-directory/role-based-access-control-configure.md).
2. Tilldela Azure Data Lake Store-konto Add-säkerhetsgrupper. Detta styr åtkomsten till Data Lake Store-konto från portalen och hantering av åtgärder från portalen eller API: er.
3. Tilldela Add-säkerhetsgrupper som åtkomst Kontrollistor (ACL) på Data Lake Store-filsystem.
4. Dessutom kan du också ange ett IP-adressintervall för klienter som kan komma åt data i Data Lake Store.

Den här artikeln innehåller instruktioner om hur du använder Azure-portalen för att utföra uppgifterna som ovan. Detaljerad information om hur Data Lake Store implementerar säkerhet på kontot och data finns [säkerhet i Azure Data Lake Store](data-lake-store-security-overview.md). Djupgående information om hur ACL: er implementeras i Azure Data Lake Store, se [översikt över åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Krav
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

1. Öppna ett Azure Data Lake Store-konto. I den vänstra rutan klickar du på **Bläddra**, klickar du på **Datasjölager**, och klicka sedan på namnet på kontot som du vill tilldela en grupp med användare eller säkerhetsgrupp från Data Lake Store-bladet.

2. I din Data Lake Store-konto inställningar-bladet klickar du på **Access Control (IAM)**. Som standard visar bladet **prenumerationsadministratörer** som en ägare.
   
    ![Tilldela Azure Data Lake Store-konto säkerhetsgrupp](./media/data-lake-store-secure-data/adl.select.user.icon.png "tilldela säkerhetsgrupp till Azure Data Lake Store-konto")

    Det finns två sätt att lägga till en grupp och tilldela relevanta rollerna.
   
    * Lägg till en användargrupp på kontot och sedan tilldela en roll eller
    * Lägga till en roll och tilldela sedan användare eller grupper i rollen.
     
    I det här avsnittet ska titta vi på det första tillvägagångssättet lägga till en grupp och sedan tilldela roller. Du kan utföra liknande steg för att först välja en roll och tilldela grupper till rollen.
4. I den **användare** bladet, klickar du på **Lägg till** att öppna den **Lägg till åtkomst** bladet. I den **Lägg till åtkomst** bladet, klickar du på **Välj en roll**, och välj sedan en roll för användaren eller gruppen.
   
    ![Lägga till en roll för användaren](./media/data-lake-store-secure-data/adl.add.user.1.png "lägga till en roll för användaren")
   
    Den **ägare** och **deltagare** rollen ger åtkomst till en mängd funktioner för administration på data lake-konto. För användare som kommer att interagera med data i data lake kan du lägga till dem i den ** Reader ** roll. Omfattningen av dessa roller är begränsad till hanteringsåtgärder som är relaterade till Azure Data Lake Store-konto.
   
    Operations enskilda filsystembehörigheter definiera vad användarna kan göra för data. Därför kan en användare med rollen Läsare bara visa administrativa inställningar som är associerat med kontot men kan potentiellt läsa och skriva data baserat på filsystembehörigheter tilldelad. Data Lake Store-filsystembehörigheterna beskrivs på [tilldela säkerhetsgrupp som ACL: er till Azure Data Lake Store-filsystem](#filepermissions).
5. I den **Lägg till åtkomst** bladet, klickar du på **lägga till användare** att öppna den **lägga till användare** bladet. Leta efter den säkerhetsgrupp du skapade tidigare i Azure Active Directory i det här bladet. Om du har många grupper att söka i, använder du textrutan längst upp för att filtrera efter gruppnamnet. Klicka på **Välj**.
   
    ![Lägg till en säkerhetsgrupp](./media/data-lake-store-secure-data/adl.add.user.2.png "lägger till en säkerhetsgrupp")
   
    Om du vill lägga till en grupp/användare som inte visas kan du bjuda in dem med hjälp av den **bjuda in** ikon och ange e-postadressen för användaren eller gruppen.
6. Klicka på **OK**. Du bör se säkerhetsgruppen läggas till som visas nedan.
   
    ![Lägga till säkerhetsgruppen](./media/data-lake-store-secure-data/adl.add.user.3.png "säkerhetsgrupp som lagts till")

7. Användaren/säkerhetsgrupp har nu tillgång till Azure Data Lake Store-konto. Om du vill ge åtkomst till specifika användare, kan du lägga till dem i säkerhetsgruppen. På liknande sätt kan du vill återkalla åtkomst för en användare kan du ta bort dem från säkerhetsgruppen. Du kan också tilldela flera säkerhetsgrupper till ett konto. 

## <a name="filepermissions"></a>Tilldela användare eller säkerhetsgrupp som ACL: er till Azure Data Lake Store-filsystem
Genom att tilldela användaren/säkerhetsgrupper till filsystemet Azure Data Lake kan ange du åtkomstkontroll på data som lagras i Azure Data Lake Store.

1. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Skapa kataloger i Data Lake Store-konto](./media/data-lake-store-secure-data/adl.start.data.explorer.png "skapa kataloger i Data Lake-konto")
2. I den **Data Explorer** bladet, klickar du på filen eller mappen som du vill konfigurera ACL och klicka sedan på **åtkomst**. Om du vill tilldela en fil ACL, måste du klicka på **åtkomst** från den **Filförhandsgranskning** bladet.
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er i Data Lake-filsystem")
3. Den **åtkomst** bladet visar den standard tillgång och anpassade redan tilldelad till roten. Klicka på den **Lägg till** ikon för att lägga till anpassad nivå ACL: er.
   
    ![Visa en lista över standard och anpassad åtkomst](./media/data-lake-store-secure-data/adl.acl.2.png "listan standard och anpassad åtkomst")
   
   * **Standard åtkomst** är UNIX-format-åtkomst där du kan ange läsa, skriva, köra (rwx) till tre olika klasser: ägare och gruppen.
   * **Anpassade åtkomst** motsvarar POSIX ACL: erna som gör att du kan ange behörigheter för specifika namngivna användare eller grupper, och inte bara filens ägare eller grupp. 
     
     Mer information finns i [HDFS ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Mer information om hur ACL: er implementeras i Data Lake Store finns [åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).
4. Klicka på den **Lägg till** ikon för att öppna den **Lägg till anpassad åtkomst** bladet. I det här bladet, klickar du på **Välj användare eller grupp**, och klicka sedan på **Välj användare eller grupp** bladet, leta efter den säkerhetsgrupp du skapade tidigare i Azure Active Directory. Om du har många grupper att söka i, använder du textrutan längst upp för att filtrera efter gruppnamnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Välj**.
   
    ![Lägga till en grupp](./media/data-lake-store-secure-data/adl.acl.3.png "lägga till en grupp")
5. Klicka på **Välj behörigheter**behörigheter och välj om du vill tilldela behörigheterna som standard ACL åtkomst till ACL eller båda. Klicka på **OK**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.acl.4.png "tilldela behörigheter till gruppen")
   
    Mer information om behörigheter i Data Lake Store och standard-/ Access ACL: er finns [åtkomstkontroll i Data Lake Store](data-lake-store-access-control.md).
6. I den **Lägg till anpassad åtkomst** bladet, klickar du på **OK**. Den nyligen tillagda gruppen med associerade behörigheter visas nu i den **åtkomst** bladet.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.acl.5.png "tilldela behörigheter till gruppen")
   
   > [!IMPORTANT]
   > I den aktuella versionen kan du bara ha 9 poster under **anpassad åtkomst**. Om du vill lägga till mer än 9-användare bör du skapa säkerhetsgrupper, lägga till användare i säkerhetsgrupper, lägga till ge åtkomst till de säkerhetsgrupperna för Data Lake Store-konto.
   > 
   > 
7. Om det behövs kan du också ändra behörigheter för åtkomst när du har lagt till gruppen. Avmarkera eller markera kryssrutan för varje behörighetstyp (läsa, skriva, köra) baserat på om du vill ta bort eller tilldela behörigheten till säkerhetsgruppen. Klicka på **spara** att spara ändringarna, eller **Ignorera** ångra ändringarna.

## <a name="set-ip-address-range-for-data-access"></a>Ange IP-adressintervall för dataåtkomst
Azure Data Lake Store kan du ytterligare Lås åtkomst till dina data store på nätverksnivån. Du kan aktivera brandväggen, ange en IP-adress eller definiera ett intervall med IP-adresser för dina betrodda klienter. När du har aktiverat, kan endast klienter som har IP-adresser inom definierade intervallet ansluta till arkivet.

![Brandväggsinställningar och IP-åtkomst till](./media/data-lake-store-secure-data/firewall-ip-access.png "inställningar och IP-adress för brandvägg")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Ta bort säkerhetsgrupper för ett Azure Data Lake Store-konto
När du tar bort säkerhetsgrupper från Azure Data Lake Store-konton kan ändrar du bara åtkomst till hanteringsåtgärder på det konto som använder Azure-portalen och Azure Resource Manager API: er.

1. I ditt Data Lake Store-kontoblad klickar du på **inställningar**. Från den **inställningar** bladet, klickar du på **användare**.
   
    ![Tilldela Azure Data Lake-konto säkerhetsgrupp](./media/data-lake-store-secure-data/adl.select.user.icon.png "tilldela säkerhetsgrupp till Azure Data Lake-konto")
2. I den **användare** bladet klickar du på säkerhetsgruppen som du vill ta bort.
   
    ![Säkerhetsgruppen för att ta bort](./media/data-lake-store-secure-data/adl.add.user.3.png "säkerhetsgrupp ska tas bort")
3. Klicka på bladet för säkerhetsgruppen **ta bort**.
   
    ![Säkerhetsgrupp bort](./media/data-lake-store-secure-data/adl.remove.group.png "säkerhetsgrupp tas bort")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Ta bort säkerhetsgruppen ACL: er från Azure Data Lake Store-filsystem
När du tar bort säkerhetsgrupper ACL: er från Azure Data Lake Store-filsystem, ändrar du åtkomst till data i Data Lake Store.

1. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Skapa kataloger i Data Lake-konto](./media/data-lake-store-secure-data/adl.start.data.explorer.png "skapa kataloger i Data Lake-konto")
2. I den **Data Explorer** bladet på filen eller mappen som du vill ta bort ACL, och klicka på ditt kontoblad, klicka på den **åtkomst** ikon. Om du vill ta bort ACL för en fil, måste du klicka på **åtkomst** från den **Filförhandsgranskning** bladet.
   
    ![Ange ACL: er i Data Lake filsystemet](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er i Data Lake-filsystem")
3. I den **åtkomst** bladet från den **anpassad åtkomst** klickar du på säkerhetsgruppen som du vill ta bort. I den **anpassad åtkomst** bladet, klickar du på **ta bort** och klicka sedan på **OK**.
   
    ![Tilldela behörigheter till gruppen](./media/data-lake-store-secure-data/adl.remove.acl.png "tilldela behörigheter till gruppen")

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
* [Kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Kom igång med Data Lake Store med hjälp av PowerShell](data-lake-store-get-started-powershell.md)
* [Kom igång med Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Åtkomst till diagnostikloggar för Data Lake Store](data-lake-store-diagnostic-logs.md)

