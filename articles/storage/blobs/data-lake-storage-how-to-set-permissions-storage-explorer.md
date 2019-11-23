---
title: 'Azure Storage Explorer: Manage access in Azure Data Lake Storage Gen2'
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
# <a name="use-azure-storage-explorer-to-manage-access-in-azure-data-lake-storage-gen2"></a>Use Azure Storage Explorer to manage access in Azure Data Lake Storage Gen2

Filer som lagras i Azure Data Lake Storage Gen2 stöder detaljerade behörigheter och hantering av åtkomstkontrollistor (ACL). Tillsammans kan du, med detaljerade behörigheter och ACL-hantering, hantera åtkomsten till dina data på en mycket detaljerad nivå.

I den här artikeln lär du dig hur du använder Azure Storage Explorer till att:

> [!div class="checklist"]
> * ange behörigheter på filnivå
> * ange behörigheter på katalognivå
> * lägga till användare eller grupper i en åtkomstkontrollista

## <a name="prerequisites"></a>Krav

För att du ska få den bästa beskrivningen av processen behöver du slutföra [snabbstarten för Azure Storage Explorer](data-lake-storage-Explorer.md). This ensures your storage account will be in the most appropriate state (container created and data uploaded to it).

## <a name="managing-access"></a>Hantera åtkomst

You can set permissions at the root of your container. To do so, you must be logged into Azure Storage Explorer with your individual account with rights to do so (as opposed to with a connection string). Right-click your container and select **Manage Permissions**, bringing up the **Manage Permission** dialog box.

![Microsoft Azure Storage Explorer – Hantera katalogåtkomst](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

I dialogrutan **Hantera behörighet** kan du hantera behörigheter för ägare och ägargruppen. Du kan även lägga till nya användare och grupper i åtkomstkontrollistan, som du sedan kan lägga till behörigheter för.

Du kan lägga till en ny användare eller grupp i åtkomstkontrollistan genom att välja fältet **Lägg till användare eller grupp**.

Ange motsvarande Azure Active Directory-post (AAD) du vill lägga till i listan och välj sedan **Lägg till**.

Användaren eller gruppen visas nu i fältet **Användare och grupper:** , där du kan börja hantera deras behörigheter.

> [!NOTE]
> Vi rekommenderar bästa praxis att skapa en säkerhetsgrupp i AAD och hantera behörigheter för gruppen istället för enskilda användare. Information om den här rekommendationen, samt andra metodtips, finns i [metodtipsen för Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Det finns två kategorier av behörigheter du kan tilldela: åtkomst-ACL och standard-ACL.

* **Access**: Access ACLs control access to an object. Filer och kataloger har båda åtkomst-ACL:er.

* **Default**: A template of ACLs associated with a directory that determines the access ACLs for any child items that are created under that directory. Filer har inte standard-ACL:er.

Within both of these categories, there are three permissions you can then assign on files or directories: **Read**, **Write**, and **Execute**.

>[!NOTE]
> När du gör val här anges inte behörigheter för några befintliga objekt i katalogen. Du måste gå till varje enskilt objekt och ange behörigheterna manuellt, om filen redan finns.

Du kan hantera behörigheter för enskilda kataloger, och enskilda filer, vilket är det som ger dig detaljerad åtkomstkontroll. Processen för att hantera behörigheter för både kataloger och filer är samma som beskrivs ovan. Högerklicka på filen eller katalogen du vill hantera behörigheter för och följ samma process.

## <a name="next-steps"></a>Nästa steg

I dessa anvisningar har du lärt dig hur du anger behörigheter för filer och kataloger med hjälp av **Azure Storage Explorer**. Mer information om åtkomstkontrollistor (ACL), t.ex. standard- och åtkomst-ACL:er, och deras motsvarande behörigheter får du om du fortsätter till vår konceptuella artikel om ämnet.

> [!div class="nextstepaction"]
> [Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
