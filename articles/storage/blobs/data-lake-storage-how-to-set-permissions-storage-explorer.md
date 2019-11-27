---
title: 'Azure Storage Explorer: hantera åtkomst i Azure Data Lake Storage Gen2'
description: I dessa anvisningar lär du dig hur du anger behörigheter med Azure Storage Explorer för filer och kataloger på ditt lagringskonto med funktioner för Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b34103e521def678acce17e3292e04fca95b5e6e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327969"
---
# <a name="use-azure-storage-explorer-to-manage-access-in-azure-data-lake-storage-gen2"></a>Använd Azure Storage Explorer för att hantera åtkomst i Azure Data Lake Storage Gen2

Filer som lagras i Azure Data Lake Storage Gen2 stöder detaljerade behörigheter och hantering av åtkomstkontrollistor (ACL). Tillsammans kan du, med detaljerade behörigheter och ACL-hantering, hantera åtkomsten till dina data på en mycket detaljerad nivå.

I den här artikeln lär du dig hur du använder Azure Storage Explorer till att:

> [!div class="checklist"]
> * ange behörigheter på filnivå
> * ange behörigheter på katalognivå
> * lägga till användare eller grupper i en åtkomstkontrollista

## <a name="prerequisites"></a>Krav

För att du ska få den bästa beskrivningen av processen behöver du slutföra [snabbstarten för Azure Storage Explorer](data-lake-storage-Explorer.md). Detta säkerställer att ditt lagrings konto kommer att vara i det lämpligaste stadiet (behållare skapat och data som överförs till det).

## <a name="managing-access"></a>Hantera åtkomst

Du kan ange behörigheter i roten för din behållare. För att göra det måste du vara inloggad på Azure Storage Explorer med ditt enskilda konto med behörighet att göra detta (till skillnad från med en anslutnings sträng). Högerklicka på din behållare och välj **Hantera behörigheter**, så visas dialog rutan **Hantera behörighet** .

![Microsoft Azure Storage Explorer – Hantera katalogåtkomst](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

I dialogrutan **Hantera behörighet** kan du hantera behörigheter för ägare och ägargruppen. Du kan även lägga till nya användare och grupper i åtkomstkontrollistan, som du sedan kan lägga till behörigheter för.

Du kan lägga till en ny användare eller grupp i åtkomstkontrollistan genom att välja fältet **Lägg till användare eller grupp**.

Ange motsvarande Azure Active Directory-post (AAD) du vill lägga till i listan och välj sedan **Lägg till**.

Användaren eller gruppen visas nu i fältet **Användare och grupper:** , där du kan börja hantera deras behörigheter.

> [!NOTE]
> Vi rekommenderar bästa praxis att skapa en säkerhetsgrupp i AAD och hantera behörigheter för gruppen istället för enskilda användare. Information om den här rekommendationen, samt andra metodtips, finns i [metodtipsen för Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Det finns två kategorier av behörigheter du kan tilldela: åtkomst-ACL och standard-ACL.

* **Åtkomst**: åtkomst kontrol listor styr åtkomst till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

* **Standard**: en mall för ACL: er som är kopplade till en katalog som fastställer åtkomst-ACL: er för underordnade objekt som skapas under den katalogen. Filer har inte standard-ACL:er.

I båda dessa kategorier finns tre behörigheter som du kan tilldela till filer eller kataloger: **läsa**, **skriva**och **köra**.

>[!NOTE]
> När du gör val här anges inte behörigheter för några befintliga objekt i katalogen. Du måste gå till varje enskilt objekt och ange behörigheterna manuellt, om filen redan finns.

Du kan hantera behörigheter för enskilda kataloger, och enskilda filer, vilket är det som ger dig detaljerad åtkomstkontroll. Processen för att hantera behörigheter för både kataloger och filer är samma som beskrivs ovan. Högerklicka på filen eller katalogen du vill hantera behörigheter för och följ samma process.

## <a name="next-steps"></a>Nästa steg

I dessa anvisningar har du lärt dig hur du anger behörigheter för filer och kataloger med hjälp av **Azure Storage Explorer**. Mer information om åtkomstkontrollistor (ACL), t.ex. standard- och åtkomst-ACL:er, och deras motsvarande behörigheter får du om du fortsätter till vår konceptuella artikel om ämnet.

> [!div class="nextstepaction"]
> [Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
