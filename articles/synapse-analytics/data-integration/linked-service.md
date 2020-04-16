---
title: Etablera och skydda en länkad tjänst i Azure Synapse Analytics
description: Lär dig hur du etablerar och säkrar en länkad tjänst med Hanterat Vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430557"
---
# <a name="securing-a-linked-service-with-private-links"></a>Skydda en länkad tjänst med privata länkar 

I den här artikeln får du lära dig att skydda en länkad tjänst i Synapse med en privat slutpunkt.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration:** Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto:** Du använder Azure Data Lake Gen 2 som ett *källdatalager.* Om du inte har ett lagringskonto läser du [Skapa ett Azure Storage-konto](../../storage/blobs/data-lake-storage-quickstart-create-account.md) för steg för att skapa ett. Kontrollera att lagringskontot har IP-filtrningen i Synapse Studio för åtkomst till det och att du bara tillåter **valda nätverk** att komma åt lagringskontot. Inställningen under bladet **Brandväggar och virtuella nätverk** bör se ut som bilden nedan.

![Konto för skyddad lagring](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Skapa en länkad tjänst med privata länkar

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutningsinformation till andra tjänster. I det här avsnittet lägger du till Azure Synapse Analytics och Azure Data Lake Gen 2 som länkade tjänster.

1. Öppna Azure Synapse Studio och gå till fliken **Hantera.**
1. Under **Externa anslutningar**väljer du Länkade **tjänster**.
1. Om du vill lägga till en länkad tjänst klickar du på **Ny**.
1. Välj panelen Azure Data Lake Storage Gen2 i listan och klicka på **Fortsätt**.
1. Se till att du aktiverar **interaktiv redigering**. Det kan ta cirka 1 minut att aktivera. 
1. Ange dina autentiseringsuppgifter. Kontonyckel, tjänsthuvudnamn och hanterad identitet stöds för närvarande autentiseringstyper. Klicka på testanslutningen för att kontrollera att dina autentiseringsuppgifter är korrekta.
1. Välj **Test-anslutning**bör det misslyckas eftersom lagringskontot inte aktiverar åtkomst till den utan att skapa och godkänna en privat slutpunkt. I felmeddelandet bör du se en länk för att skapa en **privat slutpunkt** som du kan följa för att gå till nästa del. Om du följer den länken hoppar du över nästa del.
1. Välj **Skapa** när du är klar.

## <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slutpunkt

Om du inte klickade i hyperlänken när du testade anslutningen ovan följer du följande sökväg. Nu måste du skapa en hanterad privat slutpunkt som du ansluter till den länkade tjänsten som skapats ovan.

1. Gå till fliken **Hantera.**
1. Gå till avsnittet **Hanterade virtuella nätverk.**
1. Välj **+ Nytt** under Hanterad privat slutpunkt.
1. Välj panelen Azure Data Lake Storage Gen2 i listan och välj **Fortsätt**.
1. Ange namnet på det lagringskonto som du skapade ovan.
1. Välj **Skapa**
1. Du bör se efter att ha väntat några sekunder att den privata länken som skapats behöver ett godkännande.

## <a name="approval-of-a-private-link"></a>Godkännande av en privat länk
1. Välj den privata slutpunkt som du skapade ovan. Du kan se en hyperlänk som gör att du kan godkänna den privata slutpunkten på lagringskontonivå. *Ett alternativ är att gå direkt till Azure portal Storage Account och gå in i bladet **Privata slutpunktsanslutningar.***
1. Markera den privata slutpunkt som du skapade i studion och välj **Godkänn**.
1. Lägga till en beskrivning och klicka på **ja**
1. Gå tillbaka till Synapse Studio under avsnittet **Hanterade virtuella nätverk** på fliken **Hantera.**
1. Det bör ta cirka 1 minut att få godkännandet återspeglas för din privata slutpunkt.

## <a name="check-the-connection-works"></a>Kontrollera att anslutningen fungerar
1. Gå till fliken **Hantera** och välj den länkade tjänst som du har skapat.
1. Kontrollera att **interaktiv redigering** är aktiv.
1. Välj **Testanslutning**. Du bör se anslutningen lyckas.

Du har nu etablerat en säker och privat förbindelse mellan Synapse och din länkade tjänst!

## <a name="next-steps"></a>Nästa steg

Om du vill utveckla ytterligare förståelse för Hanterad privat slutpunkt i Synapse Analytics läser du artikeln [Koncept runt Synapse Managed private endpoint.](data-integration-data-lake.md)

Mer information om dataintegration för Synapse Analytics finns [i intagsdata i en datasjöartikel.](data-integration-data-lake.md)