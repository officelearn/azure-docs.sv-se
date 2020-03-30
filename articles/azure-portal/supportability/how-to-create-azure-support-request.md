---
title: Så här skapar du en Azure-supportbegäran | Microsoft-dokument
description: Kunder som behöver hjälp kan använda Azure-portalen för att hitta självbetjäningslösningar och skapa och hantera supportförfrågningar.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248494"
---
# <a name="how-to-create-an-azure-support-request"></a>Skapa en supportbegäran för Azure

## <a name="overview"></a>Översikt

Med Azure kan du skapa och hantera supportärenden, även kallade supportbiljetter. Du kan skapa och hantera begäranden i [Azure-portalen](https://portal.azure.com), som beskrivs i den här artikeln. Du kan också skapa och hantera begäranden programmässigt med hjälp av [AZURE-supportbiljettEN REST API](/rest/api/support).

> [!NOTE]
> Azure-portal-URL:en är specifik för Azure-molnet där din organisation distribueras.
>
>* Azure-portal för kommersiellt bruk är:[https://portal.azure.com](https://portal.azure.com)
>* Azure portal för Tyskland är:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure-portalen för USA:s regering är:[https://portal.azure.us](https://portal.azure.us)
>
>

Baserat på feedback från kunder har vi uppdaterat supportförfrågoden för att fokusera på tre huvudmål:

* **Strömlinjeformad**: Gör support och felsökning lätt att hitta och förenkla hur du skickar in en supportbegäran.
* **Integrerad:** Du kan enkelt öppna en supportbegäran när du felsöker ett problem med en Azure-resurs, utan att byta kontext.
* **Effektiv**: Samla in viktig information som supportagenten behöver för att effektivt lösa problemet.

## <a name="getting-started"></a>Komma igång

Du kan komma åt **hjälp + support** i Azure-portalen. Den är tillgänglig från Azure-portalmenyn, det globala huvudet eller resursmenyn för en tjänst. Innan du kan lämna in en supportbegäran måste du ha rätt behörighet.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Om du vill skapa en supportbegäran måste du vara administratör eller tilldelas rollen [Support request contributor](../../role-based-access-control/built-in-roles.md#support-request-contributor) på prenumerationsnivå.

### <a name="go-to-help--support-from-the-global-header"></a>Gå till hjälp + stöd från det globala huvudet

Så här startar du en supportbegäran var som helst i Azure-portalen:

1. Välj **?** i den globala rubriken. Välj sedan **Stöd för Hjälp +**.

   ![Hjälp och support](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Välj **Ny supportbegäran**. Följ anvisningarna för att ge oss information om ditt problem. Vi föreslår några möjliga lösningar, samlar in information om problemet och hjälper dig att skicka och spåra supportbegäran.

   ![Ny supportbegäran](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Gå till hjälp + stöd från en resursmeny

Om du vill starta en supportbegäran i samband med resursen arbetar du för närvarande med:

1. Välj **Ny supportbegäran**i avsnittet **Support + felsökning** på resursmenyn .

   ![I sitt sammanhang](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Följ anvisningarna för att ge oss information om problemet du har. När du startar supportbegäran från resursen är vissa alternativ förvalda för dig.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Vi hjälper dig att lösa det genom några steg för att samla in information om ditt problem och hjälpa dig att lösa det. Varje steg beskrivs i följande avsnitt.

### <a name="basics"></a>Grundläggande inställningar

Det första steget i supportbegäran processen samlar grundläggande information om ditt problem och din supportplan.

På fliken **Grunderna** i **Ny supportbegäran**använder du selektorerna för att börja berätta om problemet. Först ska du identifiera några allmänna kategorier för ärendetypen och välja den relaterade prenumerationen. Välj tjänsten, till exempel **Virtuell dator som kör Windows**. Välj resursen, till exempel namnet på den virtuella datorn. Beskriv problemet med egna ord och välj sedan **problemtyp** för att få mer specifik.

![Bladet Grundläggande inställningar](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure ger obegränsad support för prenumerationshantering, vilket inkluderar fakturering, kvotjusteringar och kontoöverföringar. För teknisk support behöver du en supportplan. [Läs mer om supportplaner](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Lösningar

Efter att ha samlat in grundläggande information visar vi dig sedan lösningar för att prova på egen hand. I vissa fall kan vi även köra en snabb diagnos. Lösningarna är skrivna av Azure-tekniker och löser de vanligaste problemen.

### <a name="details"></a>Information

Därefter samlar vi in ytterligare information om problemet. Genom att tillhandahålla detaljerad och detaljerad information i det här steget kan vi dirigera din supportbegäran till rätt agent.

Om möjligt, berätta för oss när problemet började och eventuella steg för att reproducera den. Du kan ladda upp en fil, till exempel en loggfil eller utdata från diagnostik.

När vi har all information om problemet väljer du hur vi ska få support. Välj **allvarlighetsgraden** för påverkan i avsnittet Supportmetod i **Detalj.** Ge önskad kontaktmetod, en bra tid att kontakta dig och ditt supportspråk.

Fyll sedan i avsnittet **Kontaktinformation** så att vi vet hur du kontaktar dig.

### <a name="review--create"></a>Granska + skapa

Fyll i all nödvändig information på varje flik och välj sedan **Granska + skapa**. Kontrollera informationen som du skickar till supporten. Gå tillbaka till valfri flik för att göra en ändring om det behövs. När du är nöjd väljer supportbegäran klar väljer du **Skapa**.

En supportagent kontaktar dig med den metod du angav. Information om inledande svarstid finns i [Supportomfattning och svarstider](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Alla supportförfrågningar

Du kan visa information och status för supportförfrågningar genom att gå till **Hjälp + support** >  **Alla supportförfrågningar**.

![Alla supportförfrågningar](./media/how-to-create-azure-support-request/allrequestslower.png)

På den här sidan kan du filtrera supportbegäranden efter **prenumeration**, **skapat** datum (UTC) och **Status**. Dessutom kan du sortera och söka efter supportärenden på den här sidan.

Välj en supportbegäran om du vill visa information, inklusive allvarlighetsgrad och förväntad tid det tar för en supportagent att svara.

Om du vill ändra allvarlighetsgraden för begäran väljer du **Affärspåverkan**. Välj från en lista över allvarlighetsgrader att tilldela.

> [!NOTE]
> Den maximala allvarlighetsgraden beror på din supportplan. [Läs mer om supportplaner](https://azure.microsoft.com/support/plans).
>
>
Om du vill veta mer om självhjälpssupportalternativ i Azure kan du titta på det här videoklippet:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Nästa steg

* [Skicka oss dina synpunkter och förslag](https://feedback.azure.com/forums/266794-support-feedback)
* Samarbeta med oss på [Twitter](https://twitter.com/azuresupport)
* Få hjälp av dina kollegor i [MSDN-forumen](https://social.msdn.microsoft.com/Forums/azure)
* Läs mer i [Vanliga frågor och svar om Azure Support](https://azure.microsoft.com/support/faq)
