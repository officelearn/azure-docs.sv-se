---
title: Metodtips för Azure Stack-verifiering | Microsoft Docs
description: Den här artikeln beskriver Metodtips för verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: a90ec05fa3224033436830e7666c7eb81ff881f6
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247001"
---
# <a name="create-an-oem-package"></a>Skapa ett OEM-paket

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack OEM-tilläggspaket används av vilka OEM specifikt innehåll läggs till i Azure Stack-infrastruktur för användning i distributionen samt operativa processer som update, expansion och fältet ersättning.

## <a name="creating-the-package"></a>Skapa paketet

När du har skapat och verifierats, kan OEM-tilläggspaket användas i VaaS.  Innan du fortsätter, kontrollera att du har slutfört stegen för att [skapar en OEM-paket](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Paketet skickas sedan till Microsoft tillsammans med VaaS testresultaten för att logga in lösningen valideringsarbetsflödet. Följande steg förklarar vi hur du sammanställer de genererade filerna i en enda zip-fil som VaaS konsumerar.

1. Identifiera följande innehåll för paketet:
    - En körbar fil med namnet `<Publisher>-<Model>-<Version>.exe`
    - En eller flera filer binära filer med namnet `<Publisher><Model>-<Version>-#.bin`, där # är ett serienummer som startar med 1. Antalet binära filer är beroende av den totala storleken på paketinnehållet.
    - En manifestfil som heter `oemMetadata.xml`, vilket ska vara identiska i innehåll till metadata.xml-filen i roten av paketinnehållet.

2. Välj innehållsfilerna och skapa en zip-fil från innehållet:

    ![ZIP-filens innehåll](media/vaas-create-oem-package-1.png) ![Komprimera innehåll för objekt](media/vaas-create-oem-package-2.png)

3. Byt namn på den resulterande filen så att den är beskrivande för dig att identifiera den.

## <a name="verifying-the-contents"></a>Verifiera innehållet

Granska den för att verifiera strukturen för zip-filen och kontrollera att det finns inga undermappar. TLD har komprimerade innehållet. Ett giltigt paket-struktur visas nedan.
> [!IMPORTANT]
> Komprimeringen den överordnade mappen i stället för innehållet genereras paketet signering misslyckas.

![Korrekt komprimerade paketinnehåll](media/vaas-create-oem-package-3.png)

Zip-filen kan nu överföras till VaaS och signerat av Microsoft i lösningen valideringsarbetsflödet.

## <a name="next-steps"></a>Nästa steg

- [Validera en OEM-paketet](azure-stack-vaas-validate-oem-package.md)
