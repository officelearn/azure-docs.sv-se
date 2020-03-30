---
title: Felsöka Azure Data Share
description: Lär dig hur du felsöker problem med inbjudningar och fel när du skapar eller tar emot dataresurser med Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964234"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Felsöka vanliga problem i Azure Data Share 

Den här artikeln visar hur du felsöker vanliga problem för Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Inbjudningar till Azure Data Share 

I vissa fall kan en tom lista med inbjudningar visas när en ny användare klickar på **Acceptera inbjudan** från e-postbjudan som skickades. 

![Inga inbjudningar](media/no-invites.png)

Detta kan bero på följande:

* **Azure Data Share-tjänsten registreras inte som en resursleverantör av någon Azure-prenumeration i Azure-klienten.** Du kommer att uppleva det här problemet om det inte finns någon dataresursresurs i din Azure-klientorganisation. När du skapar en Azure Data Share-resurs registrerar den automatiskt resursleverantören i din Azure-prenumeration. Du kan också registrera tjänsten Datadelning manuellt i dessa steg. Du måste ha azure contributor-rollen för att kunna utföra dessa steg.

    1. Navigera till **Prenumerationer** i Azure-portalen
    1. Välj den prenumeration som du vill använda för att skapa Azure Data Share-resurs
    1. Klicka på **Resursleverantörer**
    1. Sök efter **Microsoft.DataShare**
    1. Klicka på **Registrera** 

    Du måste ha [Azure Contributor RBAC-rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) för att kunna utföra dessa steg. 

* **Inbjudan skickas till ditt e-postalias i stället för ditt Azure-inloggningsmeddelande.** Om du har registrerat Azure Data Share-tjänsten eller redan har skapat en dataresursresurs i Azure-klienten, men fortfarande inte kan se inbjudan, kanske det beror på att leverantören har angett ditt e-postalias som mottagare i stället för din Azure-inloggningsadress. Kontakta din dataleverantör och se till att de har skickat inbjudan till din Azure-inloggningsadress och inte ditt e-postalias.

* **Inbjudan har redan accepterats.** Länken i e-postmeddelandet tar dig till sidan Inbjudning av datadelning i Azure-portalen, som bara innehåller en lista över väntande inbjudningar. Om du redan har accepterat inbjudan visas den inte längre på sidan Inbjudning av datadelning. Fortsätt till din dataresursresurs som du använde för att acceptera inbjudan till för att visa mottagna resurser och konfigurera inställningen för Azure Data Explorer-kluster.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fel vid skapande eller mottagning av en ny resurs

"Det gick inte att lägga till datauppsättningar"

"Det gick inte att mappa datauppsättningar"

"Det går inte att bevilja dataresurs x åtkomst till y"

"Du har inte rätt behörighet till x"

"Vi kunde inte lägga till skrivbehörigheter för Azure Data Share-konto i en eller flera av dina valda resurser"

Om du får något av ovanstående fel när du skapar en ny resurs eller mappning av datauppsättningar, kan det bero på otillräckliga behörigheter till Azure-datalagret. Se [Roller och krav](concepts-roles-permissions.md) för nödvändiga behörigheter. 

Du behöver skrivbehörighet för att dela eller ta emot data från ett Azure-datalager, som vanligtvis finns i rollen Deltagare. 

Om det är första gången du delar eller tar emot data från Azure-datalagret behöver du också *Microsoft.Authorization/role assignments/write-behörighet,* som vanligtvis finns i ägarrollen. Även om du har skapat Azure-datalagringsresursen gör den dig INTE automatiskt till resursens ägare. Med rätt behörighet ger Azure Data Share-tjänsten automatiskt dataresursens hanterade identitetsåtkomst till datalagret. Denna process kan ta några minuter att träda i kraft. Om du får fel på grund av den här fördröjningen kan du försöka igen om några minuter.

SQL-baserad delning kräver ytterligare behörigheter. Mer information finns i Felsökning av SQL-baserad delning.

## <a name="troubleshooting-sql-based-sharing"></a>Felsöka SQL-baserad delning

"Användare x finns inte i SQL-databasen"

Om det här felet visas när du lägger till en datauppsättning från en SQL-baserad källa kan det bero på att du inte skapade en användare för den hanterade identiteten för Azure Data Share på din SQL Server.  LÃ¶s problemet genom att köra följande skript:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Om det här felet visas när du mappar datauppsättning till ett SQL-baserat mål kan det bero på att du inte skapade en användare för den hanterade identiteten för Azure Data Share på din SQL Server.  LÃ¶s problemet genom att köra följande skript:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Observera att *<share_acc_name>* är namnet på din datadelningsresurs.      

Se till att du har följt alla förutsättningar som anges i [Dela dina data](share-your-data.md) och acceptera och ta emot [data](subscribe-to-data-share.md) självstudiekurs.

## <a name="snapshot-failed"></a>Ögonblicksbilden misslyckades
Ögonblicksbild kan misslyckas på grund av en mängd olika orsaker. Du kan hitta detaljerade felmeddelande genom att klicka på starttiden för ögonblicksbilden och sedan status för varje datauppsättning. 

Om felmeddelandet är relaterat till behörighet kontrollerar du att tjänsten Datadelning har den behörighet som krävs. Mer [information finns i Roller och krav.](concepts-roles-permissions.md) Om det är första gången du tar en ögonblicksbild kan det ta några minuter innan datadelningsresursen beviljas åtkomst till Azure-datalagret. Vänta några minuter och försök igen.

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du börjar dela data fortsätter du till [att dela din datahandledning.](share-your-data.md) 

Om du vill veta hur du tar emot data fortsätter du till att [acceptera och ta emot datahandledning.](subscribe-to-data-share.md)

