---
title: Skydda data som lagras i Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Lär dig hur du skyddar data i Azure Data Lake Storage Gen1 med hjälp av grupper och åtkomstkontrolllistor
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260311"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Skydda data som lagras i Azure Data Lake Storage Gen1
Att skydda data i Azure Data Lake Storage Gen1 är en trestegsmetod.  Både rollbaserad åtkomstkontroll (RBAC) och åtkomstkontrollistor (ACL: er) måste ställas in så att åtkomst till data för användare och säkerhetsgrupper är helt tillgängliga för åtkomst för användare och säkerhetsgrupper.

1. Börja med att skapa säkerhetsgrupper i Azure Active Directory (AAD). Dessa säkerhetsgrupper används för att implementera rollbaserad åtkomstkontroll (RBAC) i Azure-portalen. Mer information finns [i Rollbaserad åtkomstkontroll i Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Tilldela AAD-säkerhetsgrupperna till datasjölagringsgenm1-kontot. Detta styr åtkomsten till Data Lake Storage Gen1-kontot från portal- och hanteringsåtgärderna från portalen eller API:erna.
3. Tilldela AAD-säkerhetsgrupperna som åtkomstkontrollistor (ACL: er) i filsystemet Data Lake Storage Gen1.
4. Dessutom kan du också ange ett IP-adressintervall för klienter som kan komma åt data i Data Lake Storage Gen1.

Den här artikeln innehåller instruktioner om hur du använder Azure-portalen för att utföra ovanstående uppgifter. Detaljerad information om hur Data Lake Storage Gen1 implementerar säkerhet på konto- och datanivå finns [i Säkerhet i Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Djupdykningsinformation om hur ACL implementeras i Data Lake Storage Gen1 finns [i Översikt över åtkomstkontroll i DataSjölagring gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Skapa säkerhetsgrupper i Azure Active Directory
Instruktioner om hur du skapar AAD-säkerhetsgrupper och hur du lägger till användare i gruppen finns [i Hantera säkerhetsgrupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Du kan lägga till både användare och andra grupper i en grupp i Azure AD med Azure-portalen. Använd dock [Azure AD:s PowerShell-modul](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)för att lägga till ett tjänsthuvudnamn i en grupp.
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Tilldela användare eller säkerhetsgrupper till DataSjölagringsgend1-konton
När du tilldelar användare eller säkerhetsgrupper till Data Lake Storage Gen1-konton styr du åtkomsten till hanteringsåtgärderna på kontot med hjälp av Azure-portalen och Azure Resource Manager-API:erna. 

1. Öppna ett Data Lake Storage Gen1-konto. Klicka på Alla **resurser**i den vänstra rutan och klicka sedan på det kontonamn som du vill tilldela en användare eller säkerhetsgrupp till från bladet Alla resurser.

2. Klicka på **Åtkomstkontroll (IAM)** i kontobladet För datasjölagringsgenm1 . Bladet visar som standard prenumerationsägarna som ägare.
   
    ![Tilldela säkerhetsgrupp till Azure Data Lake Storage Gen1-konto](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Tilldela säkerhetsgrupp till Azure Data Lake Storage Gen1-konto")

3. Öppna bladet Lägg **till** **behörigheter** i **bladet Åtkomstkontroll (IAM).** Välj en **roll** för användaren/gruppen i bladet **Lägg till behörigheter.** Leta efter säkerhetsgruppen som du skapade tidigare i Azure Active Directory och välj den. Om du har många användare och grupper att söka från använder du textrutan **Markera** för att filtrera efter gruppnamnet. 
   
    ![Lägga till en roll för användaren](./media/data-lake-store-secure-data/adl.add.user.1.png "Lägga till en roll för användaren")
   
    Rollen **Ägare** och **Deltagare** ger åtkomst till en mängd olika administrationsfunktioner på datasjökontot. För användare som interagerar med data i datasjön men fortfarande behöver visa kontohanteringsinformation kan du lägga till dem i **rollen Läsare.** Omfattningen av dessa roller är begränsad till de hanteringsåtgärder som är relaterade till Data Lake Storage Gen1-kontot.
   
    För dataåtgärder definierar enskilda filsystembehörigheter vad användarna kan göra. Därför kan en användare som har en läsarroll bara visa administrativa inställningar som är associerade med kontot, men kan eventuellt läsa och skriva data baserat på filsystembehörigheter som tilldelats dem. Data Lake Storage Gen1 filsystembehörigheter beskrivs på [Tilldela säkerhetsgrupp som ACL:er till filsystemet Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Endast **ägarrollen** aktiverar automatiskt filsystemåtkomst. **Deltagaren,** **läsaren**och alla andra roller kräver ACL:er för att aktivera alla åtkomstnivåer för mappar och filer.  **Ägarrollen** ger superanvändarfil och mappbehörigheter som inte kan åsidosättas via ÅTKOMSTKONTROLL. Mer information om hur RBAC-principer mappar till dataåtkomst finns i [RBAC för kontohantering](data-lake-store-security-overview.md#rbac-for-account-management).

4. Om du vill lägga till en grupp/användare som inte finns med i bladet **Lägg till behörigheter** kan du bjuda in dem genom att skriva deras e-postadress i textrutan **Markera** och sedan markera dem i listan.
   
    ![Lägga till en säkerhetsgrupp](./media/data-lake-store-secure-data/adl.add.user.2.png "Lägga till en säkerhetsgrupp")
   
5. Klicka på **Spara**. Du bör se säkerhetsgruppen som lagts till enligt nedan.
   
    ![Säkerhetsgrupp har lagts till](./media/data-lake-store-secure-data/adl.add.user.3.png "Säkerhetsgrupp har lagts till")

6. Din användar-/säkerhetsgrupp har nu åtkomst till Data Lake Storage Gen1-kontot. Om du vill ge åtkomst till specifika användare kan du lägga till dem i säkerhetsgruppen. Om du vill återkalla åtkomsten för en användare kan du ta bort dem från säkerhetsgruppen. Du kan också tilldela flera säkerhetsgrupper till ett konto. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Tilldela användare eller säkerhetsgrupper som ACL:er till filsystemet Data Lake Storage Gen1
Genom att tilldela användar-/säkerhetsgrupper till filsystemet Data Lake Storage Gen1 anger du åtkomstkontroll för data som lagras i DataSjölagring gen1.

1. Klicka på **Data Explorer**i kontobladet Datasjölagringsgenm1.
   
    ![Visa data via Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Visa data via Data Explorer")
2. Klicka på den mapp som du vill konfigurera åtkomstkontrollistan i bladet **Data Explorer** och klicka sedan på **Access**. Om du vill tilldela ACL:er till en fil måste du först klicka på filen för att förhandsgranska den och sedan klicka på **Access** från bladet **Förhandsgranskning av fil.**
   
    ![Ange ACL:er för datalagringsgenm1-filsystem](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL:er för datalagringsgenm1-filsystem")
3. **I bladet Access** visas de ägare och tilldelade behörigheter som redan har tilldelats roten. Klicka på ikonen **Lägg** till om du vill lägga till ytterligare åtkomstkontrollistor.
    > [!IMPORTANT]
    > Att ange åtkomstbehörigheter för en enskild fil ger inte nödvändigtvis en användare/grupp åtkomst till filen. Sökvägen till filen måste vara tillgänglig för den tilldelade användaren/gruppen. Mer information och exempel finns i [Vanliga scenarier relaterade till behörigheter](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista standard och anpassad åtkomst](./media/data-lake-store-secure-data/adl.acl.2.png "Lista standard och anpassad åtkomst")
   
   * **Ägarna** och **alla andra** ger UNIX-stil åtkomst, där du anger läsa, skriva, köra (rwx) till tre olika användarklasser: ägare, grupp och andra.
   * **Tilldelade behörigheter** motsvarar de POSIX-ACL:er som gör att du kan ange behörigheter för specifika namngivna användare eller grupper utanför filens ägare eller grupp. 
     
     Mer information finns i [HDFS ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Mer information om hur ACL implementeras i Gen1 för lagring av datasjö finns [i Åtkomstkontroll i Gen1 för lagring av datasjö.](data-lake-store-access-control.md)
4. Klicka på ikonen **Lägg** till för att öppna bladet **Tilldela behörigheter.** I det här bladet klickar du på **Välj användare eller grupp**och letar sedan efter säkerhetsgruppen som du skapade tidigare i Azure Active Directory i Välj **användar- eller gruppblad.** Om du har många grupper att söka från använder du textrutan högst upp för att filtrera på gruppnamnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Markera**.
   
    ![Lägga till en grupp](./media/data-lake-store-secure-data/adl.acl.3.png "Lägga till en grupp")
5. Klicka på **Välj behörigheter,** välj behörigheter, om behörigheterna ska tillämpas rekursivt och om du vill tilldela behörigheterna som åtkomstkontrollista, standardförordnare eller båda. Klicka på **OK**.
   
    ![Tilldela behörigheter till grupp](./media/data-lake-store-secure-data/adl.acl.4.png "Tilldela behörigheter till grupp")
   
    Mer information om behörigheter i Gen1 för lagring av datasjö och ACL:er för standard/åtkomst finns [i Åtkomstkontroll i DataSjölagringgengen1](data-lake-store-access-control.md).
6. När du har klickat på **Ok** i bladet **Välj behörigheter** visas nu den nyligen tillagda gruppen och tillhörande behörigheter i **bladet Access.**
   
    ![Tilldela behörigheter till grupp](./media/data-lake-store-secure-data/adl.acl.5.png "Tilldela behörigheter till grupp")
   
   > [!IMPORTANT]
   > I den aktuella versionen kan du ha upp till 28 poster under **Tilldelade behörigheter**. Om du vill lägga till fler än 28 användare bör du skapa säkerhetsgrupper, lägga till användare i säkerhetsgrupper, lägga till ge åtkomst till dessa säkerhetsgrupper för DataSjölagringsgenm1-kontot.
   > 
   > 
7. Om det behövs kan du också ändra åtkomstbehörigheterna när du har lagt till gruppen. Avmarkera eller markera kryssrutan för varje behörighetstyp (Läs, Skriv, Kör) baserat på om du vill ta bort eller tilldela den behörigheten till säkerhetsgruppen. Klicka på **Spara** om du vill spara ändringarna eller **Ignorera** om du vill ångra ändringarna.

## <a name="set-ip-address-range-for-data-access"></a>Ange IP-adressintervall för dataåtkomst
Med DataSjölagring Gen1 kan du ytterligare låsa åtkomsten till ditt datalager på nätverksnivå. Du kan aktivera brandvägg, ange en IP-adress eller definiera ett IP-adressintervall för dina betrodda klienter. När det är aktiverat kan endast klienter som har IP-adresser inom definierat intervall ansluta till arkivet.

![Brandväggsinställningar och IP-åtkomst](./media/data-lake-store-secure-data/firewall-ip-access.png "Brandväggsinställningar och IP-adress")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Ta bort säkerhetsgrupper för ett Gen1-konto för datasjölagring1
När du tar bort säkerhetsgrupper från Data Lake Storage Gen1-konton ändrar du bara åtkomsten till hanteringsåtgärderna på kontot med azure portal- och Azure Resource Manager-API:erna.  

Åtkomsten till data är oförändrad och hanteras fortfarande av åtkomstkontrollistor.  Undantaget är användare/grupper i rollen Ägare.  Användare/grupper som tas bort från ägarrollen är inte längre superanvändare och deras åtkomst går tillbaka för att komma åt ACL-inställningar. 

1. Klicka på **Åtkomstkontroll (IAM)** i kontobladet För datasjölagringsgenm1 . 
   
    ![Tilldela säkerhetsgrupp till Data Lake Storage Gen1-konto](./media/data-lake-store-secure-data/adl.select.user.icon.png "Tilldela säkerhetsgrupp till Data Lake Storage Gen1-konto")
2. Klicka på den eller de säkerhetsgrupper som du vill ta bort i bladet **Åtkomstkontroll (IAM).** Klicka på **Ta bort**.
   
    ![Säkerhetsgrupp har tagits bort](./media/data-lake-store-secure-data/adl.remove.group.png "Säkerhetsgrupp har tagits bort")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Ta bort ACL:er för säkerhetsgrupp från ett datasjölagringsgenm1-filsystem
När du tar bort ACL:er för säkerhetsgrupp från ett datasjölagringsgenm1-filsystem ändrar du åtkomsten till data i datasjölagringsgenm1-kontot.

1. Klicka på **Data Explorer**i kontobladet Datasjölagringsgenm1.
   
    ![Skapa kataloger i Data Lake Storage Gen1-konto](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Skapa kataloger i Data Lake Storage Gen1-konto")
2. Klicka på den mapp som du vill ta bort åtkomstkontrollistan i bladet **Data Explorer** och klicka sedan på **Access**. Om du vill ta bort ACL:er för en fil måste du först klicka på filen för att förhandsgranska den och sedan klicka på **Access** från bladet **Förhandsgranskning av fil.** 
   
    ![Ange ACL:er för datalagringsgenm1-filsystem](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL:er för datalagringsgenm1-filsystem")
3. Klicka på den säkerhetsgrupp som du vill ta bort i **bladet Access.** Klicka på **Ta bort**i bladet **Access-information** .
   
    ![Tilldela behörigheter till grupp](./media/data-lake-store-secure-data/adl.remove.acl.png "Tilldela behörigheter till grupp")

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Komma igång med Data Lake Storage Gen1 med hjälp av PowerShell](data-lake-store-get-started-powershell.md)
* [Komma igång med DataSjölagring Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Åtkomstdiagnostiska loggar för Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)

