---
title: "Använd ändringar för att uppdatera din API i Azure API Management | Microsoft Docs"
description: "Följ stegen i den här kursen lär dig hur du gör ändringar för hårt med ändringar i API-hantering."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>Ändra hårt på ett säkert sätt med hjälp av ändringar
Den här självstudiekursen beskrivs hur du gör ändringar i din API på ett säkert sätt och meddela ändringen utvecklarna.

## <a name="prerequisites"></a>Krav
Den här kursen måste du skapa en API Management-tjänst och har en befintlig API som du kan ändra (i stället för konferens API).

## <a name="about-revisions"></a>Om ändringar
När din API är redo att sätta igång och startar som ska användas av utvecklare, måste vanligtvis du ta hand i att göra ändringar i den API - utan att störa anropare av din API. Det är också användbart att låta utvecklare känna till om de ändringar du gjort. Vi kan göra detta i Azure API Management med hjälp av **revisioner**.

## <a name="walkthrough"></a>Genomgång
I den här genomgången vi lägga till en ny version, lägga till en åtgärd och gör att revision aktuella - skapa en loggpost för ändra när vi gör.

## <a name="add-a-new-revision"></a>Lägg till en ny version
1. Bläddra till den **API: er** sida i API Management-tjänsten i Azure-portalen.
2. Välj **konferens API** i API-listan, Välj den **revisioner** fliken på menyn längst upp på sidan.
3. Välj **+ Lägg till Revision**

    > [!TIP]
    > Du kan också välja **lägga till Revision** på snabbmenyn (**...** ) på API: et.
![Revisioner menyn övre delen av skärmen](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Ange en beskrivning för en ny revision för att komma ihåg vad den ska användas för.
5. Välj **skapa**
6. Nu skapas en ny version.

    > [!NOTE]
    > Ursprungliga API finns kvar i **Revision 1**. Det här är version användarna kommer att fortsätta att anropa förrän du väljer att göra en annan revision aktuella.

## <a name="make-non-breaking-changes-to-your-revision"></a>Ändra hårt din omarbetning
1. Välj den **Design** fliken längst upp på skärmen.
2. Observera att den **revision selector** (direkt ovanför fliken design) visar den aktuella versionen som **version 2**.

    > [!TIP]
    > Använd revision selector för att växla mellan revisioner som du vill arbeta med.

3. Välj **+ Lägg till åtgärden**.
4. Ange ditt nya åtgärden ska kunna **POST**, och namn och visningsnamn för åtgärden som **Feedback**
5. **Spara** nya igen.
6. Vi har nu gjort en ändring av **version 2**. Använd den **Revision Selector** överst på sidan för att växla tillbaka till **Revision 1**.
7. Observera att nya åtgärden inte visas i **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Gör din omarbetning aktuella och lägger till en ändring loggpost
1. Välj den **revisioner** fliken på menyn längst upp på sidan.
![Menyn revision på skärmen revision.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Öppna snabbmenyn (**...** ) för **version 2**.
3. Välj **se aktuella**.
![Se revision aktuella och publicera om du vill ändra logg](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Välj **Post till offentliga ändringsloggen för detta API**
5. Ange en beskrivning för din ändring som utvecklare visas, till exempel **”lägga till nya Feedback igen”.**
6. **Revision 2** nu är aktuell.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Bläddra developer-portalen om du vill se ändringar och Ändringslogg
1. Välj **Utvecklarportalen** på den översta menyn.
2. Välj **API: er**, och välj sedan **konferens API**.
3. Lägg märke till din nya **Feedback** åtgärden är nu tillgänglig.
4. Välj **API ändringshistoriken** från nedan API-namnet.
5. Observera att din ändring loggpost visas i listan.
![Ändringslogg på Developer-portalen](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Nästa steg
[Publicera API-versionerna med Azure API Management](#api-management-getstarted-publish-versions.md)