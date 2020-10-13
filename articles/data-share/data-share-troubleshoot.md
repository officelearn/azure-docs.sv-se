---
title: Felsöka Azure Data Share
description: Lär dig hur du felsöker problem med inbjudningar och fel när du skapar eller tar emot data resurser med Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/02/2020
ms.openlocfilehash: 620fe1e693a177123e166220ab94bbd74c4826ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761540"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Felsöka vanliga problem i Azure Data Share 

Den här artikeln visar hur du felsöker vanliga problem med Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-inbjudningar 

I vissa fall, när en ny användare klickar på **acceptera inbjudan** från e-postinbjudan som skickades, kan de visas med en tom lista med inbjudningar. 

![Inga inbjudningar](media/no-invites.png)

Det här kan bero på något av följande:

* **Azure Data Share-tjänsten är inte registrerad som en resursprovider för någon Azure-prenumeration i klientorganisationen.** Du ser det här felet om det inte finns någon Data Share-resurs i Azure-klienten. När du skapar en Azure Data Share-resurs registrerar den automatiskt resursprovidern i din Azure-prenumeration. Du kan också registrera Data Share-tjänsten manuellt med följande steg. Du måste ha en deltagarroll i Azure för att utföra de här stegen.

    1. Gå till **Prenumerationer** i Azure-portalen
    1. Välj den prenumeration du vill använda till att skapa Azure Data Share-resursen
    1. Klicka på **Resursprovidrar**
    1. Sök efter **Microsoft.DataShare**
    1. Klicka på **Registrera** 

    Du måste ha [Azure Contributor-rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) till Azure-prenumerationen för att slutföra de här stegen. 

* **Inbjudan skickas till ditt e-postalias snarare än din e-postadress för Azure-inloggning.** Om du har registrerat Azure Data Share-tjänsten eller redan har skapat en Data Share-resurs i Azure-klienten, men ändå inte kan se inbjudan, kan det bero på att providern har angett ditt e-postalias som mottagare snarare än din e-postadress för Azure-inloggning. Kontakta din dataprovider och kontrollera att de har skickat inbjudan till din e-postadress för Azure-inloggning och inte ditt e-postalias.

* **Inbjudan har redan accepterats.** Länken i e-postmeddelandet tar dig till sidan för Data Share-inbjudningar i Azure-portalen, och där visas bara väntande inbjudningar. Om du redan har accepterat inbjudan visas den inte längre på sidan för Data Share-inbjudningar. Gå vidare till din Data Share-resurs som du använde till att ta emot inbjudan för att visa mottagna resurser och konfigurera ditt Azure Data Explorer-klustermål.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fel vid skapande eller mottagning av en ny resurs

"Det gick inte att lägga till data uppsättningar"

"Det gick inte att mappa data uppsättningar"

"Det gick inte att bevilja data resursen Resource x åtkomst till y"

"Du har inte rätt behörighet till x"

"Det gick inte att lägga till Skriv behörigheter för Azure Data Share-konto till en eller flera av de valda resurserna"

Om du får ovanstående fel när du skapar en ny resurs eller mappar data uppsättningar, kan det bero på otillräcklig behörighet för Azure Data Store. Se [roller och krav](concepts-roles-permissions.md) för nödvändiga behörigheter. 

Du måste ha Skriv behörighet för att dela eller ta emot data från ett Azure-datalager, som vanligt vis finns i deltagar rollen. 

Om det här är första gången du delar eller tar emot data från Azure Data Store, behöver du även *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet, som vanligt vis finns i ägar rollen. Även om du har skapat Azure Data Store-resursen så blir det inte automatiskt ägaren till resursen. Med rätt behörighet ger Azure Data Share service automatiskt den hanterade identitets åtkomsten för data resursen till data lagret. Den här processen kan ta några minuter att börja gälla. Om det uppstår ett problem på grund av den här fördröjningen, försök igen om några minuter.

SQL-baserad delning kräver ytterligare behörigheter. Se [Dela från SQL-källor](how-to-share-from-sql.md) för detaljerad lista över krav.

## <a name="snapshot-failed"></a>Ögonblicks bild misslyckades
Det gick inte att ta ögonblicks bilder på grund av olika orsaker. Du hittar ett detaljerat fel meddelande genom att klicka på Start tiden för ögonblicks bilden och sedan på status för varje data uppsättning. Följande är orsaken till varför det inte går att ögonblicks bilder:

* Data resursen har inte behörighet att läsa från käll data lagret eller skriva till mål data lagret. Se [roller och krav](concepts-roles-permissions.md) för detaljerade behörighets krav. Om det här är första gången du tar en ögonblicks bild kan det ta några minuter för data resurs resursen att beviljas åtkomst till Azure Data Store. Vänta några minuter och försök igen.
* Data delnings anslutningen till käll-eller mål data lagret blockeras av brand väggen.
* Den delade data mängden eller käll-eller mål data lagret har tagits bort.
* För SQL-delning stöds inte data typerna av ögonblicks bild processen eller mål data lagret. Mer information finns i [Dela från SQL-källor](how-to-share-from-sql.md#supported-data-types) .

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) . 

Om du vill lära dig hur du tar emot data fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) .

