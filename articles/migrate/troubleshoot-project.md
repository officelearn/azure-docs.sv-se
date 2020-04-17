---
title: Felsöka Azure Migrate-projekt
description: Hjälper dig att felsöka problem med att skapa och hantera Azure Migrate-projekt.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535408"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Felsöka Azure Migrate-projekt

Den här artikeln hjälper dig att felsöka problem när du skapar och hanterar [Azure Migrate-projekt.](migrate-services-overview.md)

## <a name="how-to-add-new-project"></a>Hur lägger man till nya projekt?

Du kan ha flera Azure Migrate-projekt i en prenumeration. [Lär dig hur du](how-to-add-tool-first-time.md) skapar ett projekt för första gången eller [lägger till ytterligare](create-manage-projects.md#create-additional-projects) projekt.

## <a name="what-azure-permissions-are-needed"></a>Vilka Azure-behörigheter behövs?

Du behöver behörigheter för deltagare eller ägare i prenumerationen för att skapa ett Azure Migrate-projekt.

## <a name="cant-find-a-project"></a>Det går inte att hitta ett projekt

Att hitta ett befintligt Azure Migrate-projekt beror på om du använder den aktuella eller gamla versionen av Azure Migrate. [Följ](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Kan inte hitta en geografi

Du kan skapa ett Azure Migrate-projekt i geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) moln och [myndighetsmoln](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="what-are-vm-limits"></a>Vad är VM-gränser?

Du kan bedöma upp till 35 000 virtuella virtuella datorer med VMware eller upp till 35 000 virtuella virtuella hyper-virtuella datorer i ett enda projekt. Ett projekt kan omfatta både virtuella datorer och virtuella datorer med hyper-v, upp till bedömningsgränserna.

## <a name="can-i-upgrade-old-project"></a>Kan jag uppgradera gamla projekt?

Projekt från den tidigare versionen av Azure Migrate kan inte uppdateras. Du måste [skapa ett nytt projekt](how-to-add-tool-first-time.md)och lägga till verktyg i det.

## <a name="cant-create-a-project"></a>Det går inte att skapa ett projekt

Om du försöker skapa ett projekt och stöter på ett distributionsfel:

- Försök att skapa projektet igen om det är ett tillfälligt fel. I **Distributioner**klickar du på **Distribuera om** för att försöka igen.
- Kontrollera att du har behörigheter för deltagare eller ägare i prenumerationen.
- Om du distribuerar i en nyligen tillagd geografi väntar du en kort stund och försöker igen.
- Om du får felmeddelandet "Begäranden måste innehålla användaridentitetshuvuden", kan det tyda på att du inte har åtkomst till Azure Active Directory (Azure AD) för organisationen. Om så är fallet:
    - När du läggs till i en Azure AD-klient för första gången får du en e-post inbjudan att gå med i klienten.
    - Acceptera inbjudan som ska läggas till i klienten.
    - Om du inte kan se e-postmeddelandet kontaktar du en användare med åtkomst till klienten och ber dem skicka inbjudan till dig [igen.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - När du har fått e-postmeddelandet med inbjudan öppnar du det och väljer länken för att acceptera inbjudan. Logga sedan ut från Azure-portalen och logga in igen. (det går inte att uppdatera webbläsaren.) Du kan sedan börja skapa migreringsprojektet.

## <a name="how-do-i-delete-a-project"></a>Hur tar jag bort ett projekt

[Följ dessa instruktioner](create-manage-projects.md#delete-a-project) för att ta bort ett projekt. Observera att när du tar bort ett projekt tas både projektet och metadata om identifierade datorer i projektet bort.

## <a name="added-tools-dont-show"></a>Tillagda verktyg visas inte

Kontrollera att rätt projekt har valts. I hubben Azure Migrate > **servrar** eller i **databaser**klickar du på **Ändra** **bredvid Migrera projekt (Ändra)** i det övre högra hörnet på skärmen. Välj rätt prenumeration och projektnamn > **OK**. Sidan ska uppdateras med de tillagda verktygen i det valda projektet.

## <a name="next-steps"></a>Nästa steg

Lägg till [bedömnings-](how-to-assess.md) eller [migreringsverktyg](how-to-migrate.md) i Azure Migrate-projekt.