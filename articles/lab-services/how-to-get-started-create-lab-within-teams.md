---
title: Kom igång och skapa ett Azure Lab Services labb från team
description: Lär dig hur du kommer igång och skapar ett Azure Lab Services labb från team.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946849"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Kom igång och skapa ett labb tjänst labb från team

Den här artikeln visar hur du lägger till en Azure Lab Services app i Teams. Sedan kan du skapa ett labb från team.

## <a name="add-a-lab-services-app-to-teams"></a>Lägg till en labb tjänst app i Teams

Du kan lägga till labb tjänster direkt i team kanalerna och sedan är appen tillgänglig för alla i teamet som ska användas. Följ dessa tre steg:

1. Navigera till den Team-kanal där du vill lägga till appen och välj **+** att lägga till en flik genom att klicka på "..." överst i det högra fönstret. 
1. Sök efter **Azure Lab Services** från fliken Alternativ och Lägg till den här appen. 

    > [!NOTE]
    > Endast grupp **ägare** kommer att kunna skapa labb för teamet.
1. Välj ett labb tjänst konto som du vill använda för att skapa klass rum labb i det här teamet. 

    Azure Lab Services använder enkel inloggning på [Azure Lab Services webbplats](https://labs.azure.com) och hämtar alla labb konton som du har åtkomst till. 

    De konton som finns i samma klient organisation som team och för vilka du har åtkomst till **ägare**, **deltagare**eller **skapare** visas. 

   ![Välkommen till sensorn](./media/integrate-with-teams/welcome.png) 
1. Tryck på **Spara** och appen läggs till i team och fliken läggs till i kanalen. 

    Nu kan du välja fliken **Azure Lab Services** från din kanal och börja hantera labb enligt beskrivningen i följande steg.

    När en medlem i ett team lägger till fliken visas den för alla i kanalen. Alla användare som har åtkomst till appen får enkel inloggning med de autentiseringsuppgifter de använder för Microsoft Teams. Användare som inte har åtkomst till appen kan se fliken i Teams, men blockeras tills du ger dem behörighet till den lokala appen och den Azure Portal publicerade versionen av appen.

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

När labb kontot har valts kommer team ägare att kunna skapa labb för teamet. Hela processen för att skapa labb och alla aktiviteter på labb nivån kan utföras i team. Användare kan välja att skapa flera labb i samma team och teamets ägare, med lämplig åtkomst på labb konto nivån, ser bara de labb som är kopplade till det aktuella teamet.

## <a name="giving-access-to-users-of-the-lab-account"></a>Ge åtkomst till användare av labb kontot

Att tillhandahålla åtkomst till användare på labb konto nivån måste ske i [Azure](https://ms.portal.azure.com/) -portalen.

1. I Azure Portal navigerar du till ditt Azure Lab Services-konto. 
1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** följt av **+ Lägg till** och sedan **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Skapa klass rum labb

Processen för att skapa klass rum är detsamma oavsett om du skapar labb från team eller [labb tjänst webbplatsen](https://labs.azure.com). 

Mer information om hur du skapar den här artikeln: [Hantera klass rums labb i Azure Lab Services](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Ta bort klass rums labb

Ett labb som skapats i team kan tas bort på [webbplatsen labb tjänster](https://labs.azure.com) genom att ta bort labbet direkt, enligt beskrivningen i [Hantera klass rum labb i Azure Lab Services](how-to-manage-classroom-labs.md). 

Labb borttagning utlöses också när teamet tas bort. Om teamet som labbet skapas i tas bort, skulle labbet tas bort automatiskt 24 timmar efter att synkroniseringen av den automatiska användar listan har utlösts. 

Borttagning av fliken eller avinstallation av appen innebär inte att labbet tas bort. Om fliken tas bort kommer användare i listan över team medlemskap fortfarande att kunna komma åt de virtuella datorerna på [webbplatsen labb tjänster](https://labs.azure.com) , såvida inte borttagningen av labbet uttryckligen utlöses genom att du tar bort labbet på webbplatsen eller tar bort teamet. 

## <a name="next-steps"></a>Nästa steg

När ett labb skapas i team fylls labb användar listan i automatiskt och synkroniseras med team medlemskapet. Alla i teamet, inklusive ägare, medlemmar och gäster kommer automatiskt att läggas till i labb användar listan. Azure Lab Services kommer att underhålla en synkronisering med grupp medlemskapet och en automatisk synkronisering utlöses var 24: e timme. Mer information finns i:

[Hantera användar listor i labb tjänster från team](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Se även

Se även följande artiklar:

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Hantera en VM-pool i labb tjänster från team](how-to-manage-vm-pool-within-teams.md)
- [Skapa scheman för labb tjänster från team](how-to-create-schedules-within-teams.md)
- [Åtkomst till en virtuell dator (elev visning) i labb tjänster från team](how-to-access-vm-for-students-within-teams.md)

